---
title: 加速数据库恢复
titleSuffix: Azure SQL
description: 加速数据库恢复为 Azure SQL 组合中的数据库提供快速且一致的数据库恢复、即时事务回滚和严格的日志截断。
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 05/19/2020
ms.openlocfilehash: a6d95bbcb0873086a799dcf216beab4a6b0d33de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84344690"
---
# <a name="accelerated-database-recovery-in-azure-sql"></a>Azure SQL 中的加速数据库恢复 
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

**加速数据库恢复（ADR）** 是一项 SQL Server 的数据库引擎功能，它通过重新设计 SQL Server 数据库引擎恢复过程，大大提高数据库的可用性，特别是在存在长时间运行的事务时。 ADR 目前适用于 Azure SQL 数据库、Azure SQL 托管实例、azure VM SQL Server 和 Azure Synapse Analytics 中的数据库（当前为预览版）。 ADR 的主要优势在于：

- **快速且一致的数据库恢复**

  使用 ADR，长时间运行的事务不会影响整体恢复时间，且无论系统中活动事务的数量或大小如何，都可以实现快速且一致的数据库恢复。

- **即时事务回滚**

  使用 ADR，事务回滚是即时的，与事务处于活动状态的时间或已执行的更新次数无关。

- **主动日志截断**

  使用 ADR，即使存在活动的长期运行事务，也会主动截断事务日志，这可以防止其增长失控。

## <a name="standard-database-recovery-process"></a>标准数据库恢复过程

数据库恢复遵循[ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf)恢复模式，其中包含三个阶段，如下图所示，并在关系图后面进行了详细说明。

![当前恢复过程](./media/accelerated-database-recovery/current-recovery-process.png)

- **分析阶段**

  从上一个成功的检查点的开始（或最早的脏页 LSN）开始向前扫描事务日志，以确定数据库停止时每个事务的状态。

- **重做阶段**

  从最早的未提交事务开始向前扫描事务日志直至结束，通过恢复所有提交的操作将数据库恢复到故障时的状态。

- **撤消阶段**

  对于在故障时处于活动状态的每个事务，向后遍历日志，撤消该事务执行的操作。

根据此设计，在发生崩溃时，SQL Server 数据库引擎从意外的重新启动恢复的时间与系统中最长活动事务的大小成正比。 恢复需要回滚所有未完成的事务。 所需的时间长度与事务已执行的工作及其处于活动状态的时间成正比。 因此，在存在长时间运行的事务（如大型大容量插入操作或针对大型表的索引生成操作）时，恢复过程可能需要很长时间。

此外，基于此设计，取消/回滚大型事务也可能需要很长时间，因为它使用与上述流程相同的撤消恢复阶段。

此外，当存在长时间运行的事务时，SQL Server 数据库引擎无法截断事务日志，因为恢复和回滚过程需要其相应的日志记录。 作为 SQL Server 数据库引擎的这一设计的结果，某些客户曾面临着这一问题：事务日志的大小会增长得非常大，并消耗大量的驱动器空间。

## <a name="the-accelerated-database-recovery-process"></a>加速的数据库恢复过程

ADR 通过完全重新设计 SQL Server 数据库引擎恢复过程来解决上述问题：

- 通过避免以最早的活动事务为起始点/结束点扫描日志，使其保持恒定时间/即时状态。 使用 ADR，事务日志仅从最后一个成功检查点（或最早的脏页日志序列号 (LSN)）开始处理。 因此，恢复时间不受长时间运行的事务影响。
- 由于不再需要为整个事务处理日志，因此可最大程度地减少所需的事务日志空间。 当检查点和备份出现时，可以主动截断事务日志。

从较高层次来看，ADR 可通过对所有物理数据库修改进行版本控制并仅撤消逻辑操作（逻辑操作比较有限，且几乎可以立即撤消）来实现快速数据库恢复。 在故障时处于活动状态的任何事务都被标记为已中止，因此，并发用户查询可以忽略这些事务生成的任何版本。

ADR 恢复过程与当前恢复过程具有相同的三个阶段。 下图说明了这些阶段在 ADR 中的运作方式，并在该示意图后附带了详细的说明。

![ADR 恢复过程](./media/accelerated-database-recovery/adr-recovery-process.png)

- **分析阶段**

  除了为非版本控制操作重构 sLog 和复制日志记录，此过程与以前的恢复过程相同。
  
- **重做阶段**

  分为两个阶段 (P)
  - 阶段 1

      从 sLog 重做（从最早的未提交事务到上一个检查点）。 重做是一种快速操作，因为它仅需要处理 sLog 中的一些记录。

  - 阶段 2

     从事务日志开始恢复，从最后一个检查点（而不是最早的未提交事务）开始

- **撤消阶段**

   使用 ADR 的撤销阶段几乎是瞬间完成的，方法是使用 sLog 通过逻辑还原撤消非版本化操作和持久版本存储 (PVS) 以在行级别执行基于版本的撤消。

## <a name="adr-recovery-components"></a>ADR 恢复组件

ADR 的四个关键组件是：

- **持久化版本存储区（PVS）**

  持久性版本存储区是一种新的 SQL Server 数据库引擎机制，用于持久保存在数据库本身而不是传统版本存储区中生成的行版本 `tempdb` 。 PVS 支持资源隔离，并提高可读辅助数据库的可用性。

- **逻辑还原**

  逻辑还原是一种异步过程，负责在行级别执行基于版本的撤消 - 为所有版本化操作实现即时事务回滚和撤消功能。 逻辑还原通过以下方式来完成：

  - 跟踪所有已中止事务，并将它们标记为对其他事务不可见。 
  - 使用 PVS 执行所有用户事务的回滚操作，而不是通过物理方式扫描事务日志并逐一撤消更改。
  - 在事务中止后立即释放所有锁定。 由于中止涉及到直接在内存中标记更改，此过程很高效，因此不需长时间维持锁定状态。

- **sLog**

  sLog 是一个辅助数据库内存中日志流，用于存储非版本控制操作（如元数据缓存无效、锁获取等）的日志记录。 sLog 具有以下特性：

  - 低容量和内存中
  - 通过在检查点过程中序列化保留在磁盘上
  - 提交事务时定期被截断
  - 通过仅处理非版本控制操作来加速重做和撤消  
  - 通过仅保留所需的日志记录来实现主动事务日志截断

- **清理器**

  清理器是定期唤醒并清除不需要的页面版本的异步过程。

## <a name="accelerated-database-recovery-patterns"></a>加速数据库恢复模式

以下类型的工作负载最受益于 ADR：

- 具有长期运行的事务的工作负载。
- 出现活动事务导致事务日志显著增长情况的工作负载。  
- 由于长时间运行恢复（如意外的服务重启或手动事务回滚）而导致数据库不可用的长时间工作负荷。
