---
title: Azure 安全中心的警报验证（EICAR 测试文件） |Microsoft Docs
description: 本文档介绍了如何在 Azure 安全中心验证安全警报。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cf732b92c1a208dd4c312ae442969ef958a021b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791174"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Azure 安全中心中的警报验证（EICAR 测试文件）
本文档介绍如何验证系统是否已针对 Azure 安全中心警报进行了适当的配置。

## <a name="what-are-security-alerts"></a>什么是安全警报？
警报是安全中心在检测到资源遭受威胁时生成的通知。 安全中心会按优先级列出警报，同时还会提供所需信息以快速调查问题。 安全中心还提供有关如何针对攻击采取补救措施的建议。
有关详细信息，请参阅[安全中心中的安全警报](security-center-alerts-overview.md)和[管理和响应安全警报](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>警报验证

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>在 Windows 虚拟机上验证警报 <a name="validate-windows"></a>

在计算机上安装安全中心代理以后，请在想让其成为受攻击的警报资源的计算机上执行以下步骤：

1. 复制一个可执行文件（例如“calc.exe”）到计算机的桌面或方便操作的其他目录，然后将其重命名为“ASC_AlertTest_662jfi039N.exe” 。
1. 打开命令提示符，使用一个参数（假参数名称即可）执行此文件，例如：```ASC_AlertTest_662jfi039N.exe -foo```
1. 等待 5 到 10 分钟，然后打开安全中心警报。 应该会出现警报。

> [!NOTE]
> 查看此 Windows 的测试警报时，请确保“启用参数审核”字段为 true 。 如果为 false，则需启用命令行参数审核。 若要启用它，请使用以下命令：
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>在 Linux VM 上验证警报 <a name="validate-linux"></a>

在计算机上安装安全中心代理以后，请在想让其成为受攻击的警报资源的计算机上执行以下步骤：
1. 复制一个可执行文件到方便操作的位置，然后将其重命名为“./asc_alerttest_662jfi039n”，例如：

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. 打开命令提示符并执行此文件：

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 等待 5 到 10 分钟，然后打开安全中心警报。 应该会出现警报。


## <a name="validate-alerts-on-kubernetes"></a>在 Kubernetes 上验证警报 <a name="validate-kubernetes"></a>

如果使用的是集成 Azure Kubernetes 服务的安全中心预览功能，请运行以下 kubectl 命令来测试警报是否正常工作：

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

有关将 Azure Kubernetes 服务与 Azure 安全中心集成的详细信息，请参阅[本文](azure-kubernetes-service-integration.md)。

## <a name="next-steps"></a>后续步骤
本文介绍了警报验证过程。 熟悉该验证以后，请尝试阅读以下文章：

* [Validating Azure Key Vault Threat Detection in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)（在 Azure 安全中心验证 Azure Key Vault 威胁检测）
* [管理和响应 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) - 了解如何管理和响应安全中心的安全事件。
* [Azure 安全中心的安全运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
* [了解 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) - 了解各种类型的安全警报。
