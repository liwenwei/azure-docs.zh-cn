---
title: 并置 Linux VM
description: 了解并置 Azure VM 资源如何改善延迟。
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 000c8c8955314060ae9bdedb96b086a509fb3e96
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87368636"
---
# <a name="co-locate-resources-for-improved-latency"></a>并置资源以改善延迟

在 Azure 中部署应用程序时，跨区域或可用性区域分布实例会造成网络延迟，这可能会影响应用程序的总体性能。 

## <a name="proximity-placement-groups"></a>邻近放置组

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>后续步骤

使用 Azure CLI 将 VM 部署到[邻近放置组](proximity-placement-groups.md)。

了解如何[测试网络延迟](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

了解如何[优化网络吞吐量](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。  

了解如何[结合使用邻近组和 SAP 应用程序](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
