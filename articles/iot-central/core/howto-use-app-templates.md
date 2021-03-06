---
title: 导出 Azure IoT Central 应用程序 | Microsoft Docs
description: 作为解决方案经理，我想要导出某个应用程序模板以便能够重复使用它。
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 39bb129d6edba168ed1ed45b1de205a206c83ed2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678165"
---
# <a name="export-your-application"></a>导出应用程序

本文为解决方案经理介绍如何导出 IoT Central 应用程序，以便能够重复使用它。

可以使用两个选项：

- 如果只需创建应用程序的重复副本，则可以创建应用程序的副本。
- 如果你打算创建多个副本，可以从应用程序创建一个应用程序模板。

## <a name="copy-your-application"></a>复制应用程序

可以创建任一应用程序的副本，但其中不会包括任何设备实例、设备数据历史记录和用户数据。 该副本使用将向你收费的标准定价计划。 无法通过复制应用程序来创建使用免费定价计划的应用程序。

选择“复制”。  在对话框中，输入新应用程序的详细信息。 再次选择“复制”以确认要继续操作。  若要详细了解窗体中的字段，请参阅[创建应用程序](quick-deploy-iot-central.md)快速入门。

![“应用程序设置”页](media/howto-use-app-templates/appcopy2.png)

应用复制操作成功后，可以使用链接导航到新应用程序。

![“应用程序设置”页](media/howto-use-app-templates/appcopy3a.png)

复制某个应用程序也会复制规则和电子邮件操作的定义。 某些操作（例如 Flow 和逻辑应用）通过规则 ID 与特定的规则相关联。 将某个规则复制到另一应用程序后，该规则将获得自身的规则 ID。 在这种情况下，用户必须创建一个新的操作，然后将新规则与该操作相关联。 一般而言，最好是检查规则和操作，以确保它们在新应用中是最新的。

> [!WARNING]
> 如果仪表板包含显示特定设备相关信息的磁贴，则这些磁贴会在新应用程序中显示“找不到请求的资源”。  必须重新配置这些磁贴才能显示有关新应用程序中设备的信息。

## <a name="create-an-application-template"></a>创建应用程序模板

创建 Azure IoT Central 应用程序时，可以选择内置的示例模板。 也可以从现有的 IoT Central 应用程序创建自己的应用程序模板。 然后，可以在创建新应用程序时使用自己的应用程序模板。

创建应用程序模板时，该模板将包含现有应用程序中的以下项：

- 默认应用程序仪表板，包括仪表板布局和定义的所有磁贴。
- 设备模板，包括度量、设置、属性、命令和仪表板。
- 规则。 包括所有规则定义。 但不包括电子邮件操作以外的其他操作。
- 设备集，包括其状态和仪表板。

> [!WARNING]
> 如果仪表板包含显示特定设备相关信息的磁贴，则这些磁贴会在新应用程序中显示“找不到请求的资源”。  必须重新配置这些磁贴才能显示有关新应用程序中设备的信息。

创建应用程序模板时，该模板不包含以下项：

- 设备
- 用户
- 连续数据导出定义

需手动将这些项添加到从应用程序模板创建的任何应用程序。

若要从现有的 IoT Central 应用程序创建应用程序模板：

1. 在应用程序中转到“管理”部分。****
1. 选择“应用程序模板导出”。****
1. 在“应用程序模板导出”页上，输入模板的名称和说明。****
1. 选择“导出”按钮创建应用程序模板。**** 现在可以复制“可共享的链接”，使用户能够从该模板创建新应用程序：****

![创建应用程序模板](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>使用应用程序模板

若要使用应用程序模板创建新的 IoT Central 应用程序，需要事先创建一个**可共享的链接**。 将该**可共享的链接**粘贴到浏览器的地址栏中。 随后会显示“创建应用程序”页，其中已选择你的自定义应用程序模板：****

![从模板创建应用程序](media/howto-use-app-templates/create-app.png)

选择定价计划，并填写窗体上的其他字段。 然后选择“创建”，从应用程序模板创建新的 IoT Central 应用程序。****

### <a name="manage-application-templates"></a>管理应用程序模板

在“应用程序模板导出”页上，可以删除或更新应用程序模板。****

如果删除应用程序模板，则不再可以使用事先生成的可共享链接来创建新应用程序。

若要更新应用程序模板，请在“应用程序模板导出”页上更改模板名称或说明。**** 然后再次选择“导出”按钮。**** 此操作会生成新的**可共享链接**，并使以前的任何**可共享链接** URL 失效。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用应用程序模板，接下来是了解如何[监视连接到 IoT Central 应用程序的设备的总体运行状况](howto-monitor-application-health.md)。
