---
title: 使用 Windows PowerShell DSC 安装连接的计算机代理
description: 本文介绍如何使用支持 Azure Arc 的服务器 (预览版) 使用 Windows PowerShell DSC 将计算机连接到 Azure。
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 25d6e435c261a83bf81c15d5dd445a936d48a08b
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213074"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>如何使用 Windows PowerShell DSC 安装连接的计算机代理

使用 [Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC) ，你可以自动安装和配置 Windows 计算机的软件。 本文介绍如何使用 DSC 在混合 Windows 计算机上安装支持 Azure Arc 的服务器 (预览版) 连接的计算机代理。

## <a name="requirements"></a>要求

- Windows PowerShell 版本4.0 或更高版本

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC 模块

- 用于将计算机连接到已启用 Azure Arc 的服务器的服务主体 (预览版) 无交互的。 如果尚未为启用了 Arc 的服务器创建服务主体，请按照 " [创建用于大规模载入的服务主体](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) " 部分下的步骤 (预览) 。

## <a name="install-the-connectedmachine-dsc-module"></a>安装 ConnectedMachine DSC 模块

1. 若要手动安装模块，请下载源代码并将项目目录的内容解压缩到 `$env:ProgramFiles\WindowsPowerShell\Modules folder` 。 或者，运行以下命令，使用 PowerShell 5.0) 中的 PowerShellGet (从 PowerShell 库进行安装：

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. 若要确认安装，请运行以下命令，并确保看到 Azure 连接的计算机 DSC 资源可用。

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   在输出中，应会看到如下所示的内容：

   ![确认已连接的计算机 DSC 模块安装示例](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>安装代理并连接到 Azure

此模块中的资源旨在管理 Azure 连接的计算机代理配置。 还包括一个 PowerShell 脚本 `AzureConnectedMachineAgent.ps1` ，位于 `AzureConnectedMachineDsc\examples` 文件夹中。 它使用社区资源自动执行下载和安装，并建立与 Azure Arc 的连接。此脚本执行 [从 Azure 门户将混合计算机连接到 Azure](onboard-portal.md) 一文中所述的类似步骤。

如果计算机需要通过代理服务器与服务进行通信，则在安装代理后，您需要运行 [此处](manage-agent.md#update-or-remove-proxy-settings)所述的命令。 此命令将设置代理服务器系统环境变量 `https_proxy`。 您可以使用 [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) 模块通过 DSC 执行此步骤，而不是手动运行该命令。

>[!NOTE]
>若要允许 DSC 运行，需要将 Windows 配置为接收 PowerShell 远程命令，即使在运行 localhost 配置时也是如此。 在提升的 PowerShell 终端中运行 `Set-WsManQuickConfig -Force`，即可轻松地正确配置环境。
>

可以使用 cmdlet 将 (MOF 文件) 的配置文档应用到计算机 `Start-DscConfiguration` 。

下面是传递给要使用的 PowerShell 脚本的参数。

- `TenantId`：唯一标识符 (GUID) ，它表示 Azure AD 的专用实例。

- `SubscriptionId`：你要将计算机置于其中的 Azure 订阅的订阅 ID (GUID) 。

- `ResourceGroup`：你希望连接的计算机所属的资源组名称。

- `Location`：请参阅 [支持的 Azure 区域](overview.md#supported-regions)。 此位置可以与资源组的位置相同或不同。

- `Tags`：应应用于已连接计算机资源的标记的字符串数组。

- `Credential`：一个使用 **ApplicationId** 和 **密码** 的 PowerShell 凭据对象，用于在规模上使用 [服务主体](onboard-service-principal.md)注册计算机。 

1. 在 PowerShell 控制台中，导航到保存该文件的文件夹 `.ps1` 。

2. 运行以下 PowerShell 命令来编译 MOF 文档（有关编译 DSC 配置的信息，请参阅 [DSC 配置](/powershell/scripting/dsc/configurations/configurations?view=powershell-7)：

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. 这会 `localhost.mof file` 在名为的新文件夹中创建 `C:\dsc` 。

安装代理并将其配置为连接到已启用 Azure Arc 的服务器 (预览) ，请参阅 Azure 门户，验证是否已成功连接服务器。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

## <a name="adding-to-existing-configurations"></a>添加到现有配置

可以将此资源添加到现有 DSC 配置，以表示计算机的端到端配置。 例如，你可能希望将此资源添加到设置安全操作系统设置的配置中。

PowerShell 库中的 [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) 模块可用于创建示例配置的 [复合资源](/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) ，以进一步简化组合配置。

## <a name="next-steps"></a>后续步骤

- 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机，例如，进行 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区报告，使用[用于 VM 的 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md) 启用监视等。

- 详细了解 [Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 若要主动监视计算机上运行的 OS 和工作负荷、使用自动化 Runbook 或更新管理等解决方案对其进行管理，或使用其他 Azure 服务（例如 [Azure 安全中心](../../security-center/security-center-intro.md)），需要安装适用于 Windows 和 Linux 的 Log Analytics 代理。