---
title: 适用于虚拟机的 Azure 专用主机概述
description: 详细了解如何使用 Azure 专用主机部署虚拟机。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 07/28/2020
ms.author: cynthn
ms.openlocfilehash: 97e65528a7f049c213ec077c83da1b7672469d90
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388163"
---
# <a name="azure-dedicated-hosts-for-virtual-machines"></a>适用于虚拟机的 Azure 专用主机

Azure 专用主机是一种提供物理服务器（能够托管一个或多个虚拟机）的服务，专用于一个 Azure 订阅。 专用主机是数据中心中使用的相同物理服务器，作为资源提供。 你可以在区域、可用性区域和容错域中预配专用主机。 然后，可以按照最能满足你的需要的配置将 VM 直接放入预配的主机中。



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>后续步骤

- 你可以使用[Azure CLI](dedicated-hosts-cli.md)、[门户](dedicated-hosts-portal.md)和[PowerShell](../windows/dedicated-hosts-powershell.md)部署专用主机。

- 有关详细信息，请参阅[专用主机](dedicated-hosts.md)概述。

- [此处](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有一个示例模板，该模板使用区域和容错域来最大限度地提高在某个地区的复原能力。

- 你还可以使用[Azure 专用主机的保留实例](../prepay-dedicated-hosts-reserved-instances.md)节省成本。
