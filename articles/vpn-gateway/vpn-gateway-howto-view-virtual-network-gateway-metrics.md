---
title: 查看 Azure VPN 网关指标
description: 查看 VPN 网关指标的步骤
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/12/2020
ms.author: alzam
ms.openlocfilehash: b3a79b8101a55eaf401c20cb118be3b0796b7aca
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531071"
---
# <a name="view-vpn-gateway-metrics"></a>查看 VPN 网关指标

可以使用 Azure Monitor 来监视 Azure VPN 网关。 本文讨论可通过门户获得的指标。 指标是轻型的，可以支持近实时方案，使其对警报和快速的问题检测非常有用。


|**指标**   | **单位** | **粒度** | **说明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 字节/秒  | 5 分钟| 网关上所有站点到站点连接的组合带宽平均利用率。     |
|**P2SBandwidth**| 字节/秒  | 1 分钟  | 网关上所有点到站点连接的组合带宽平均利用率。    |
|**P2SConnectionCount**| 计数  | 1 分钟  | 网关上点到站点连接的计数。   |
|**TunnelAverageBandwidth** | 字节/秒    | 5 分钟  | 在网关上创建的隧道的带宽平均利用率。 |
|**TunnelEgressBytes** | 字节 | 5 分钟 | 在网关上创建的隧道中的传出流量。   |
|**TunnelEgressPackets** | 计数 | 5 分钟 | 在网关上创建的隧道中的传出数据包的计数。   |
|**TunnelEgressPacketDropTSMismatch** | 计数 | 5 分钟 | 隧道中因流量选择器不匹配导致的被丢弃传出数据包的计数。 |
|**TunnelIngressBytes** | 字节 | 5 分钟 | 在网关上创建的隧道中的传入流量。   |
|**TunnelIngressPackets** | 计数 | 5 分钟 | 在网关上创建的隧道中的传入数据包的计数。   |
|**TunnelIngressPacketDropTSMismatch** | 计数 | 5 分钟 | 隧道中因流量选择器不匹配导致的被丢弃传入数据包的计数。 |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>以下步骤可帮助你查找和查看指标：

1. 导航到门户中的虚拟网络网关资源
2. 选择 "**概述**" 以查看总隧道入口和出口指标。

   ![用于创建警报规则的选项](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "视图")

3. 查看上面列出的任何其他指标。 单击虚拟网络网关资源下面的 "**指标**" 部分，并从下拉列表中选择该度量值。

   ![资源列表中的“选择”按钮和 VPN 网关](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "Select")

## <a name="next-steps"></a>后续步骤

若要针对隧道指标配置警报，请参阅[针对 VPN 网关指标设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。

若要配置对隧道资源日志的警报，请参阅[设置 VPN 网关资源日志的警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
