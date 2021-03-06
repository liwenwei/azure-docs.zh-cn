---
title: 映射数据流性能和优化指南
description: 了解影响 Azure 数据工厂中映射数据流性能的关键因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 08/12/2020
ms.openlocfilehash: cf91dd0b7f16bf0dcd3d84da1b942b2353ec5bd0
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212033"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>映射数据流性能和优化指南

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在 Azure 数据工厂中映射数据流提供了一个无代码界面，用于大规模设计和运行数据转换。 如果你不熟悉映射数据流，请参阅[映射数据流概述](concepts-data-flow-overview.md)。 本文重点介绍了优化和优化数据流的各种方式，以便它们能够满足您的性能基准。

观看下面的视频，看看显示了用数据流转换数据的一些示例计时。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>测试数据流逻辑

在从 ADF UX 设计和测试数据流时，调试模式允许以交互方式测试实时 Spark 群集。 这样，便可以在不等待群集预热的情况下预览数据并执行数据流。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

## <a name="monitoring-data-flow-performance"></a>监视数据流性能

使用调试模式验证转换逻辑后，在管道中将数据流作为活动运行。 数据流是使用 "执行数据流" [活动](control-flow-execute-data-flow-activity.md)在管道中操作化的。 与其他 Azure 数据工厂活动（显示转换逻辑的详细执行计划和性能配置文件）相比，数据流活动具有独特的监视体验。 若要查看数据流的详细监视信息，请单击管道活动的 "运行输出" 中的眼镜图标。 有关详细信息，请参阅[监视映射数据流](concepts-data-flow-monitoring.md)。

![数据流监视器](media/data-flow/monitoring-details.png "数据流监视器 2")

在监视数据流性能时，需要考虑四个可能的瓶颈：

* 群集启动时间
* 从源读取
* 转换时间
* 写入接收器 

![数据流监视](media/data-flow/monitoring-performance.png "数据流监视器 3")

群集启动时间是加速 Apache Spark 群集所用的时间。 此值位于 "监视" 屏幕的右上角。 数据流以实时模型运行，其中每个作业都使用一个独立的群集。 此启动时间通常需3-5 分钟。 对于顺序作业，可以通过启用 "生存时间" 值来减少此值。 有关详细信息，请参阅 [优化 Azure Integration Runtime](#ir)。

数据流利用 Spark 优化器，在 "阶段" 中重新排序并运行业务逻辑，使其尽可能快地执行。 对于数据流写入的每个接收器，监视输出会列出每个转换阶段的持续时间，以及向接收器写入数据所用的时间。 最大的时间可能是数据流的瓶颈。 如果使用最大值的转换阶段包含源，则你可能需要进一步优化读取时间。 如果转换需要很长时间，则可能需要重新分区或增加集成运行时的大小。 如果接收器处理时间较大，则可能需要向上扩展数据库，或验证是否未输出到单个文件。

确定数据流的瓶颈后，请使用以下优化策略来提高性能。

## <a name="optimize-tab"></a>“优化”选项卡

" **优化** " 选项卡包含配置 Spark 群集的分区方案的设置。 此选项卡存在于每个数据流转换中，并指定是否要在转换完成 **后** 对数据进行重新分区。 通过调整分区，可以控制跨计算节点和数据区域优化的数据分布，同时对整体数据流性能产生正面和负面影响。

![优化](media/data-flow/optimize.png "优化")

默认情况下，选择 " *使用当前分区* "，将指示 Azure 数据工厂保留转换的当前输出分区。 由于重新分区数据耗费时间，因此在大多数情况下，建议 *使用当前分区* 。 可能需要对数据进行重新分区的情况包括：聚合和联接，这些聚合和联接明显地倾斜数据或在 SQL 数据库上使用源分区。

若要更改任何转换的分区，请选择 " **优化** " 选项卡，然后选择 " **设置分区** " 单选按钮。 将显示一系列用于分区的选项。 分区的最佳方法根据数据量、候选键、null 值和基数而有所不同。 

> [!IMPORTANT]
> 单个分区将所有分布式数据组合到单个分区中。 这是一种非常缓慢的操作，也会显著影响所有下游转换和写入。 Azure 数据工厂强烈建议使用此选项，除非有明确的业务原因要这样做。

每个转换中都提供以下分区选项：

### <a name="round-robin"></a>轮循机制 

轮循机制跨分区均匀分布数据。 如果没有合理的关键候选项来实现坚实的智能分区策略，请使用轮循机制。 可以设置物理分区数目。

### <a name="hash"></a>哈希

Azure 数据工厂生成列哈希，以生成统一分区，使具有相似值的行位于同一个分区中。 使用 Hash 选项时，请测试可能的分区偏差。 可以设置物理分区数目。

### <a name="dynamic-range"></a>动态范围

动态范围基于您提供的列或表达式使用 Spark 动态范围。 可以设置物理分区数目。 

### <a name="fixed-range"></a>固定范围

生成一个表达式，该表达式为分区数据列中的值提供固定范围。 若要避免分区歪斜，应在使用此选项之前对数据有充分的了解。 为表达式输入的值将用作分区函数的一部分。 可以设置物理分区数目。

### <a name="key"></a>密钥

如果您对数据的基数有充分了解，键分区可能是一个不错的策略。 键分区为列中的每个唯一值创建分区。 不能设置分区数，因为该数字基于数据中的唯一值。

> [!TIP]
> 手动设置分区方案会 reshuffles 数据，并可以抵消 Spark 优化器的优势。 最佳做法是，除非需要，否则不要手动设置分区。

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> 优化 Azure Integration Runtime

数据流在运行时启动的 Spark 群集上运行。 所使用的群集的配置是在 integration runtime (活动的 IR) 中定义的。 定义集成运行时需要考虑三个性能注意事项：群集类型、群集大小和生存时间。

有关如何创建 Integration Runtime 的详细信息，请参阅 [Azure 数据工厂中的 Integration Runtime](concepts-integration-runtime.md)。

### <a name="cluster-type"></a>群集类型

有三个可用的 Spark 群集类型的选项： "通用"、"内存优化" 和 "计算优化"。

**常规用途** 的群集是默认选择，将非常适合大多数数据流工作负荷。 它们往往是性能和成本的最佳平衡。

如果数据流有很多联接和查找，则可能要使用 **内存优化** 群集。 内存优化群集可以将更多的数据存储在内存中，并将可以获得的内存不足错误降到最低。 优化内存每个核心的价格最高，但往往会导致更成功的管道。 如果在执行数据流时遇到内存不足错误，请切换到内存优化的 Azure IR 配置。 

**优化计算** 并非适用于 ETL 工作流，Azure 数据工厂团队不建议用于大多数生产工作负荷。 对于简单的非内存密集型数据转换（例如筛选数据或添加派生列），可以按每个核心的更便宜的价格使用计算优化群集。

### <a name="cluster-size"></a>群集大小

数据流将数据处理分散到 Spark 群集中的不同节点，以并行执行操作。 具有更多内核的 Spark 群集增加了计算环境中节点的数量。 更多节点会增加数据流的处理能力。 增加群集的大小通常是减少处理时间的一种简单方法。

默认群集大小为四个驱动程序节点和四个辅助角色节点。  处理更多数据时，建议使用较大的群集。 下面是可能的大小调整选项：

| 辅助角色核心 | 驱动程序核心 | 核心总数 | 备注 |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | 不可用于计算优化 |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

数据流的价格为 vcore，这意味着群集大小和执行时间因素均为此。 随着你的纵向扩展，每分钟的群集成本将增加，但你的整体时间会降低。

> [!TIP]
> 群集的大小对数据流的性能有很大的影响。 根据数据的大小，增加群集大小的一个点将停止提高性能。 例如，如果节点数多于数据分区，添加其他节点将不会有帮助。 最佳做法是从小规模开始，并根据性能需求进行扩展。 

### <a name="time-to-live"></a>生存时间

默认情况下，每个数据流活动会根据 IR 配置来旋转新群集。 群集启动时间花了几分钟的时间，数据处理直到完成后才会开始。 如果管道包含多个 **顺序** 数据流，则可以启用 (TTL) 值的生存时间。 指定生存时间值会使群集在一段时间内的执行完成后保持活动状态。 如果新作业在 TTL 时间内使用 IR 开始，则会重复使用现有群集并启动时间，而不是以秒为单位。 第二个作业完成后，该群集将再次保持处于活动状态的 TTL 时间。

一次只能在一个群集上运行一个作业。 如果有可用的群集，但两个数据流开始，则仅有一个使用活动群集。 第二个作业将启动其自身的独立群集。

如果大部分数据流并行执行，则不建议启用 TTL。 

> [!NOTE]
> 使用自动解析 integration runtime 时，生存时间不可用

## <a name="optimizing-sources"></a>优化源

对于 Azure SQL 数据库之外的每个源，建议你将 **当前分区** 保持为所选值。 在从所有其他源系统中进行读取时，数据流会根据数据的大小，自动对数据进行分区。 将为每 128 MB 的数据创建一个新的分区。 随着数据大小的增加，分区数量会增加。

任何自定义分区都在 Spark 读入数据 *后* 发生，并将对数据流性能产生负面影响。 读取数据时，不建议使用此数据。 

> [!NOTE]
> 读取速度可以受源系统吞吐量的限制。

### <a name="azure-sql-database-sources"></a>Azure SQL Database 源

Azure SQL 数据库具有一个名为 "源" 分区的唯一分区选项。 启用源分区可以通过在源系统上启用并行连接来改善 Azure SQL 数据库中的读取时间。 指定分区数以及如何对数据进行分区。 使用具有高基数的分区列。 您还可以输入与您的源表的分区方案匹配的查询。

> [!TIP]
> 对于源分区，SQL Server 的 i/o 是瓶颈。 添加过多的分区可能会使源数据库饱和。 通常，使用此选项时，最好使用四个或五个分区。

![源分区](media/data-flow/sourcepart3.png "源分区")

#### <a name="isolation-level"></a>隔离级别

Azure SQL 源系统上的读取隔离级别对性能有影响。 选择 "未提交读" 将提供最快的性能，并防止任何数据库锁。 若要了解有关 SQL 隔离级别的详细信息，请参阅 [了解隔离级别](https://docs.microsoft.com/sql/connect/jdbc/understanding-isolation-levels?view=sql-server-ver15)。

#### <a name="read-using-query"></a>使用查询进行读取

可以使用表或 SQL 查询从 Azure SQL 数据库读取数据。 如果要执行 SQL 查询，则必须先完成查询，然后才能开始转换。 SQL 查询可用于推送执行速度更快的操作，并减少从 SQL Server （如 SELECT、WHERE 和 JOIN 语句）读取的数据量。 当向下推送操作时，在数据进入数据流之前，您将失去跟踪转换的沿袭和性能的能力。

### <a name="azure-synapse-analytics-sources"></a>Azure Synapse Analytics 源

使用 Azure Synapse Analytics 时，源选项中存在称为 " **启用过渡** " 的设置。 这允许 ADF 使用 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15)从 Synapse 读取，这大大提高了读取性能。 启用 PolyBase 需要在数据流活动设置中指定一个 Azure Blob 存储或 Azure Data Lake Storage gen2 暂存位置。

![启用暂存](media/data-flow/enable-staging.png "启用暂存")

### <a name="file-based-sources"></a>基于文件的源

尽管数据流支持多种文件类型，但 Azure 数据工厂建议使用 Spark-native Parquet 格式，以获得最佳的读取和写入时间。

如果对一组文件运行相同的数据流，则建议从文件夹读取，使用通配符路径或从文件列表读取。 单个数据流活动运行可在批处理中处理所有文件。 有关如何设置这些设置的详细信息，请参阅 [Azure Blob 存储](connector-azure-blob-storage.md#source-transformation)等连接器文档。

如果可能，请避免使用 For-每个活动对一组文件运行数据流。 这将导致每个迭代都启动其自己的 Spark 群集，这通常不是必需的，并且可能会消耗大量资源。 

## <a name="optimizing-sinks"></a>优化接收器

当数据流写入接收器时，任何自定义分区都将在写入之前立即发生。 与源一样，在大多数情况下，建议您将 **当前分区** 保持为所选分区选项。 即使目标未分区，分区数据的写入速度要远远快于未分区数据。 下面是各种接收器类型的各个注意事项。 

### <a name="azure-sql-database-sinks"></a>Azure SQL 数据库接收器

使用 Azure SQL 数据库时，默认分区在大多数情况下都适用。 你的接收器可能有太多分区，你的 SQL 数据库无法处理。 如果正在运行，请减少 SQL 数据库接收器输出的分区数。

#### <a name="disabling-indexes-using-a-sql-script"></a>使用 SQL 脚本禁用索引

在 SQL 数据库中的负载之前禁用索引可以极大地提高写入表的性能。 在写入 SQL 接收器之前，请运行以下命令。

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

写入完成后，使用以下命令重新生成索引：

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

这两种情况都可以使用 Azure SQL DB 中的 Pre 和后期 SQL 脚本来实现，也可以在映射数据流的 Synapse 接收器中完成。

![禁用索引](media/data-flow/disable-indexes-sql.png "禁用索引")

> [!WARNING]
> 禁用索引时，数据流实际上会控制数据库并使查询在此时不可能成功。 因此，在晚间，会触发许多 ETL 作业，以避免此冲突。 有关详细信息，请参阅 [禁用索引的约束](https://docs.microsoft.com/sql/relational-databases/indexes/disable-indexes-and-constraints?view=sql-server-ver15)

#### <a name="scaling-up-your-database"></a>扩展数据库

在管道运行的前面，计划重设源和接收器 Azure SQL 数据库与数据仓库的大小，以提高吞吐量，并在达到 DTU 限制后尽量减小 Azure 限制。 管道执行完成后，根据数据库的正常运行速率重设其大小。

### <a name="azure-synapse-analytics-sinks"></a>Azure Synapse 分析接收器

写入 Azure Synapse Analytics 时，请确保将 " **启用过渡** " 设置为 "true"。 这使得 ADF 可以使用 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 进行编写，这会有效地批量加载数据。 使用 PolyBase 时，需要引用 Azure Data Lake Storage gen2 或 Azure Blob 存储帐户来暂存数据。

除 PolyBase 以外，相同的最佳做法也适用于 azure SQL 数据库的 Azure Synapse 分析。

### <a name="file-based-sinks"></a>基于文件的接收器 

尽管数据流支持多种文件类型，但 Azure 数据工厂建议使用 Spark-native Parquet 格式，以获得最佳的读取和写入时间。

如果数据分布均匀，则 **使用当前分区** 将是用于写入文件的最快捷分区选项。

#### <a name="file-name-options"></a>文件名选项

写入文件时，可以选择每个命名选项，这两个选项会对性能产生影响。

![接收器选项](media/data-flow/file-sink-settings.png "接收器选项​​")

选择 " **默认** " 选项将写入速度最快。 每个分区将等同于具有 Spark 默认名称的文件。 如果只是读取数据的文件夹，这会很有用。

设置命名 **模式** 会将每个分区文件重命名为用户友好的名称。 此操作在写入后发生，并稍慢于选择默认值。 每个分区允许您手动命名每个分区。

如果列与数据的输出方式相对应，则可以选择 " **列中的数据" 作为数据**。 这会 reshuffles 数据，如果列不是均匀分布的，可能会影响性能。

**输出到单个文件** 将所有数据组合到单个分区中。 这会导致长时间的写入时间，尤其是对于大型数据集。 除非有明确的业务原因，否则 Azure 数据工厂团队强烈建议 **不要** 选择此选项。

### <a name="cosmosdb-sinks"></a>CosmosDB 接收器

写入 CosmosDB 时，在数据流执行期间更改吞吐量和批大小可以提高性能。 这些更改仅在数据流活动运行期间生效，并将在结束后返回到原始集合设置。 

**批大小：** 计算数据的粗略行大小，并确保行大小 * 批大小小于2000000。 如果小于 200 万，增大批大小可获得更好的吞吐量

**吞吐量：** 在此处设置较高的吞吐量设置，以允许文档更快写入 CosmosDB。 请记住，基于高吞吐量设置的 RU 开销较高。

**写入吞吐量预算：** 使用小于每分钟的总 ru 数的值。 如果具有大量 Spark 分区的数据流，则设置预算吞吐量会允许对这些分区进行更多的均衡。


## <a name="optimizing-transformations"></a>优化转换

### <a name="optimizing-joins-exists-and-lookups"></a>优化联接、存在和查找

#### <a name="broadcasting"></a>广播

在联接、查找和存在转换中，如果一个或两个数据流非常小，足以适应工作节点内存，则可以通过启用 **广播**来优化性能。 广播是指将小型数据帧发送到群集中的所有节点。 这使 Spark 引擎能够在不重新组织大流中的数据的情况下执行联接。 默认情况下，Spark 引擎将自动决定是否广播联接的一方。 如果你熟悉传入的数据，并知道一个流将明显小于另一个，则可以选择 " **固定** 广播"。 固定广播强制 Spark 广播选定流。 

如果广播数据的大小对于 Spark 节点而言太大，则可能会出现内存不足错误。 若要避免内存不足错误，请使用 **内存优化** 群集。 如果在数据流执行期间遇到广播超时，可以关闭广播优化。 但是，这会导致数据流执行速度变慢。

![联接转换优化](media/data-flow/joinoptimize.png "联接优化")

#### <a name="cross-joins"></a>交叉联接

如果在联接条件中使用文本值或在联接的两个两侧都有多个匹配项，则 Spark 会将联接作为交叉联接来运行。 交叉联接是一种完全笛卡尔积，可筛选出联接值。 这明显慢于其他联接类型。 确保在联接条件的两侧都具有列引用，以避免对性能产生影响。

#### <a name="sorting-before-joins"></a>联接前排序

与 SSIS 等工具中的合并联接不同，联接转换不是强制性的合并联接操作。 联接键在转换前无需排序。 在映射数据流时，Azure 数据工厂团队不建议使用排序转换。

### <a name="repartitioning-skewed-data"></a>对歪斜数据重新分区

某些转换（例如联接和聚合）重新配置数据分区，有时会导致数据被扭曲。 斜向数据意味着数据不会均匀地分布到多个分区中。 严重倾斜的数据可能导致下游转换和接收器写入速度变慢。 您可以通过在 "监视" 显示中单击转换来检查数据流运行中任意点的数据偏斜度。

![偏斜度和峰值](media/data-flow/skewness-kurtosis.png "偏斜度和峰值")

监视显示将显示数据分布在每个分区上的方式，以及两个指标，即偏斜度和峰值。 **偏斜度** 是指的是数据的非对称程度，可以具有正数、零、负数或未定义的值。 负倾斜表示左侧尾部比右侧长。 **峰值** 是指数据是重型还是浅尾的度量值。 不需要高峰值值。 不对称度范围介于-3 到3之间，峰值范围小于10。 解释这些数字的一种简单方法是查看分区图，并查看1个条形是否明显大于其余。

如果在转换后未对数据进行均匀分区，则可以使用 " [优化" 选项卡](#optimize-tab) 进行重新分区。 重新组织数据需要很长时间，并且可能不会提高数据流性能。

> [!TIP]
> 如果对数据进行重新分区，但具有重新配置数据的下游转换，请对用作联接键的列使用哈希分区。

## <a name="using-data-flows-in-pipelines"></a>在管道中使用数据流 

构建具有多个数据流的复杂管道时，逻辑流可能会对计时和开销产生重大影响。 本部分介绍了不同的体系结构策略的影响。

### <a name="executing-data-flows-in-parallel"></a>并行执行数据流

如果并行执行多个数据流，则 ADF 会为每个活动旋转单独的 Spark 群集。 这允许单独隔离和运行每个作业，但会导致同时运行多个群集。

如果数据流并行执行，则建议不要启用 "Azure IR 生存时间" 属性，因为它将导致多个未使用的热池。

> [!TIP]
> 无需在每个活动的中多次运行相同的数据流，只需在 data lake 中暂存数据，并使用通配符路径处理单个数据流中的数据。

### <a name="execute-data-flows-sequentially"></a>按顺序执行数据流

如果按顺序执行数据流活动，则建议在 Azure IR 配置中设置 TTL。 ADF 将重复使用计算资源，导致群集启动速度更快。 每个活动仍将被隔离，接收每个执行的新 Spark 上下文。

按顺序运行作业很可能需要最长的时间来执行端到端，但会完全分离逻辑操作。

### <a name="overloading-a-single-data-flow"></a>重载单一数据流

如果将所有逻辑都置于单个数据流中，则 ADF 会对单个 Spark 实例执行整个作业。 虽然这看起来可能会降低成本，但它会将不同的逻辑流组合在一起，并且很难监视和调试。 如果一个组件发生故障，则该作业的所有其他部分也会失败。 Azure 数据工厂团队建议通过独立的业务逻辑流来组织数据流。 如果数据流太大，则将其拆分为多个分隔组件会使监视和调试变得更加容易。 虽然数据流中的转换数没有硬性限制，但如果有太多的限制，则会使该工作复杂化。

## <a name="next-steps"></a>后续步骤

请参阅与性能相关的其他数据流文章：

- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
