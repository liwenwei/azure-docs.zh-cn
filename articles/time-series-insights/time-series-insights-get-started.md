---
title: 创建环境 - Azure 时序见解 | Microsoft Docs
description: 了解如何使用 Azure 门户创建新的 Azure 时序见解环境。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: 02997b421a57363e04a0d988685b76f59954439e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495136"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>在 Azure 门户中创建新的 Azure 时序见解 Gen1 环境

本文介绍如何使用 Azure 门户创建新的 Azure 时序见解环境。

利用 azure 时序见解，你可以在几分钟内开始可视化和查询流入 Azure IoT 中心和事件中心的数据，使你能够在数秒内查询大量时序数据。  时序见解面向物联网 (IoT) 规模的应用，可以处理 TB 量级的数据。

## <a name="steps-to-create-the-environment"></a>创建环境的步骤

遵循以下步骤创建环境：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“+ 创建资源”  按钮。

1. 选择“物联网”类别，再选择“时序见解”。  

   [![创建 Azure 时序见解环境](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. 在“时序见解”页上，选择“创建”。  

1. 填写必需的参数。 下表解释了每个参数：

   [![创建 Azure 时序见解资源组](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   设置|建议的值|说明
   ---|---|---
   环境名称 | 唯一的名称 | 此名称代表[时序资源管理器](https://insights.timeseries.azure.com)中的环境
   订阅 | 订阅 | 如果有多个订阅，最好是选择包含事件源的订阅。 Azure 时序见解可以自动检测同一订阅中现有的 Azure IoT 中心和事件中心资源。
   资源组 | 创建新资源组或使用现有的资源组 | 资源组是结合使用的 Azure 资源的集合。 可以选择现有的资源组，例如，包含事件中心或 IoT 中心的资源组。 或者，如果此资源与其他资源不相关，则可以创建新资源组。
   位置 | 最靠近事件源的位置 | 最好是选择包含事件源数据的同一个数据中心位置，以尽量避免增加跨界和跨区域的带宽费用，以及将数据移出区域时增大延迟。
   定价层 | S1 | 选择所需的吞吐量。 若要尽量降低费用并获得入门容量，请选择 S1。
   容量 | 1 | 容量是应用于入口速率、存储容量和所选 SKU 相关成本的乘数。  可以在创建环境后更改其容量。 若要尽量降低费用，请选择 1 个单位的容量。
  
1. 选择“创建”开始执行预配过程。  这可能需要几分钟时间才能完成。

1. 若要监视部署过程，请选择“通知”符号（钟形图标）。 

   [![监视通知](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. 在资源概述  中，验证部署配置设置。

   [![创建 Azure 时序见解固定到仪表板](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **（可选）** 选择右上角的 "**固定" 图标**，以便在将来轻松访问 Azure 时序见解环境。

## <a name="next-steps"></a>后续步骤

* [定义数据访问策略](time-series-insights-data-access.md)来保护环境。

* [将事件中心事件源添加](time-series-insights-how-to-add-an-event-source-eventhub.md)到 Azure 时序见解环境。

* [向事件源发送事件](time-series-insights-send-events.md)。

* 在[Azure 时序见解资源管理器](https://insights.timeseries.azure.com)中查看环境。
