---
title: Azure 多重身份验证数据驻留
description: 了解 Azure 多重身份验证存储了关于你和你的用户的哪些个人和组织数据，以及哪些数据保留在来源国/地区内。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4b15311dfefecd9a533add9c5a028a9b7b22fd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87051159"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Azure 多重身份验证的数据驻留和客户数据

客户数据由 Azure AD 基于你的组织在订阅 Microsoft 联机服务（如 Office 365 和 Azure）时提供的地址存储在相关地理位置。 要了解客户数据的存储位置，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

基于云的 Azure 多重身份验证和 Azure 多重身份验证服务器会处理和存储一些个人数据和组织数据。 本文简要介绍了存储哪些数据及存储在何处。

以下多重身份验证活动目前源自美国数据中心，特别说明的除外：

* 使用电话或短信进行的双重身份验证通常源自美国数据中心，由全球提供商进行路由。
    * 来自其他区域（例如欧洲或澳大利亚）的常规用途的用户身份验证请求目前由该区域的数据中心处理。 其他事件，例如自助式密码重置、Azure B2C 事件或者使用 NPS 扩展或 AD FS 适配器的混合方案，目前都由美国数据中心处理。
* 使用 Microsoft Authenticator 应用的推送通知源自美国数据中心。 此外，设备供应商特定的服务也可能来自其他区域。
* 目前，OATH 代码在美国通常是有效的。
    * 同样，源自其他区域（如欧洲或澳大利亚）的常规用途的用户身份验证事件由该区域的数据中心处理。 其他事件目前由美国数据中心进行处理。

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>通过 Azure 多重身份验证存储的个人数据

个人数据是与特定人员关联的用户级信息。 以下数据存储包含个人信息：

* 被阻止的用户
* 免验证的用户
* Microsoft Authenticator 设备令牌更改请求
* 多重身份验证活动报告
* Microsoft Authenticator 激活

此信息将保留 90 天。

Azure 多重身份验证不记录用户名、电话号码或 IP 地址等个人数据，但有一个 UserObjectId 用于标识对用户的多重身份验证尝试。 日志数据将存储 30 天。

### <a name="azure-multi-factor-authentication"></a>Azure 多重身份验证

对于 Azure 公有云（不包括 Azure B2C 身份验证、NPS 扩展和 Windows Server 2016 或 2019 AD FS 适配器），将存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中     |
| 单向短信                          | 在多重身份验证日志中     |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈） |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈）<br />Microsoft Authenticator 设备令牌更改时的更改请求 |

> [!NOTE]
> 无论是哪个区域负责处理身份验证请求，所有云中的多重身份验证活动报告数据都存储在美国。 Microsoft Azure 德国、由世纪互联运营的 Microsoft Azure 以及 Microsoft 政府都有自己的数据存储，它们独立于公有云区域数据存储，但是这些数据始终存储在美国。

对于 Microsoft Azure 政府、Microsoft Azure 德国、由世纪互联运营的 Microsoft Azure、Azure B2C 身份验证、NPS 扩展，以及 Windows Server 2016 或 2019 AD FS 适配器，将存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 单向短信                          | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈） |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈）<br />Microsoft Authenticator 设备令牌更改时的更改请求 |

### <a name="multi-factor-authentication-server"></a>多重身份验证服务器

如果部署并运行 Azure 多重身份验证服务器，将存储以下个人数据：

> [!IMPORTANT]
> 从 2019 年 7 月 1 日开始，Microsoft 不再为新部署提供多重身份验证服务器。 希望用户执行多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 在 7 月 1 日之前激活了多重身份验证服务器的现有客户可像平时一样下载最新版本和未来的更新，也可生成激活凭据。

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 单向短信                          | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈） |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈）<br />Microsoft Authenticator 设备令牌更改时的更改请求 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>通过 Azure 多重身份验证存储的组织数据

组织数据是可公开配置或环境设置的租户级别的信息。 来自 Azure 门户中下列多重身份验证页面的租户设置可存储组织数据，例如锁定阈值或传入电话身份验证请求的调用方 ID 信息：

* 帐户锁定
* 欺诈警报
* 通知
* 电话呼叫设置

对于 Azure 多重身份验证服务器，Azure 门户的以下页面可能包含组织数据：

* 服务器设置
* 免验证一次
* 缓存规则
* 多重身份验证服务器状态

## <a name="log-data-location"></a>日志数据位置

日志信息的存储位置取决于处理它们的区域。 大多数地理区域都具有本机 Azure 多重身份验证功能，因此日志数据存储在处理多重身份验证请求的同一区域中。 在没有本机 Azure 多重身份验证支持的地理区域中，它们由美国或欧洲地理区域提供服务，日志数据存储在处理多重身份验证请求的同一区域中。

某些核心身份验证日志数据仅存储在美国。 Microsoft Azure 德国以及世纪互联运营的 Microsoft Azure 始终存储在各自的云中。 Microsoft 政府云日志数据始终存储在美国。

## <a name="next-steps"></a>后续步骤

要详细了解基于云的 Azure 多重身份验证和 Azure 多重身份验证服务器收集的用户信息，请参阅 [Azure 多重身份验证用户数据收集](howto-mfa-reporting-datacollection.md)。
