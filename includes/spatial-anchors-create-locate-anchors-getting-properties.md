---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "67172661"
---
在服务上创建定位点后，无法更新定位点的位置 - 你必须创建一个新的定位点并删除旧的，才能跟踪新位置。

如果不需要定位某个定位点来更新其属性，则可以使用 `GetAnchorPropertiesAsync()` 方法，该方法将返回具有属性的 `CloudSpatialAnchor` 对象。
