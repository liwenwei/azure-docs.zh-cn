---
title: Azure Functions 作为 Azure 事件网格事件的事件处理程序
description: 介绍如何将 Azure Functions 用作事件网格事件的事件处理程序。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8e48949bb5fecdf370fdf23146209ad757ffa062
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105755"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Azure Functions 作为事件网格事件的事件处理程序

事件处理程序是发送事件的位置。 处理程序将通过一个操作来处理事件。 几个 Azure 服务已自动配置为处理事件，Azure Functions 就是其中之一。 

在无服务器体系结构中使用 Azure Functions 响应事件网格中的事件。 使用 Azure Functions 作为处理程序时，请使用事件网格触发器而不是通用 HTTP 触发器。 事件网格会自动验证事件网格触发器。 使用通用 HTTP 触发器时，必须自行实现[验证响应](webhook-event-delivery.md)。

有关详细信息，请参阅 [Azure Functions 的事件网格触发器](../azure-functions/functions-bindings-event-grid.md)，概要了解如何在函数中使用事件网格触发器。

## <a name="tutorials"></a>教程

|标题  |说明  |
|---------|---------|
| [快速入门：使用函数处理事件](custom-event-to-function.md) | 将自定义事件发送到函数进行处理。 |
| [教程：使用事件网格自动调整上传图像的大小](resize-images-on-storage-blob-upload-event.md) | 用户通过 Web 应用将映像上传到存储帐户。 创建存储 Blob 后，事件网格会向用于重设已上传映像的大小的函数应用发送一个事件。 |
| [教程：将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md) | 当事件中心创建捕获文件时，事件网格会将一个事件发送到函数应用。 应用会检索捕获文件并将数据迁移到数据仓库。 |
| [教程：Azure 服务总线到 Azure 事件网格集成示例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件网格会将消息从服务总线主题发送到函数应用和逻辑应用。 |

## <a name="rest-example-for-put"></a>REST 示例（对于 PUT）

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>后续步骤
如需支持的事件处理程序的列表，请参阅[事件处理程序](event-handlers.md)一文。 
