---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Netskope 管理员控制台的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Netskope 理员控制台之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.openlocfilehash: daef8a91c2f31379ebf50d1e8ec66d0b33ebb2cc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88534806"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Netskope 管理员控制台的集成

本教程介绍如何将 Netskope 管理员控制台与 Azure Active Directory (Azure AD) 集成。 将 Netskope 管理员控制台与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Netskope 管理员控制台。
* 让用户使用其 Azure AD 帐户自动登录到 Netskope 管理员控制台。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Netskope 管理员控制台单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Netskope 管理员控制台支持 SP 和 IDP 发起的 SSO 

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>从库中添加 Netskope 管理员控制台

若要配置 Netskope 管理员控制台与 Azure AD 的集成，需从库中将 Netskope 管理员控制台添加到托管 SaaS 应用程序列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Netskope 管理员控制台”   。
1. 从结果面板中选择“Netskope 管理员控制台”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>配置和测试 Netskope 管理员控制台的 Azure AD 单一登录

使用名为“B.Simon”的测试用户配置并测试 Netskope 管理员控制台的 Azure AD SSO  。 若要正常使用 SSO，需要在 Azure AD 用户与 Netskope 管理员控制台中的相关用户之间建立链接关系。

若要配置并测试 Netskope 管理员控制台的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Netskope 管理员控制台 SSO](#configure-netskope-administrator-console-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Netskope 管理员控制台测试用户](#create-netskope-administrator-console-test-user)** - 在 Netskope 管理员控制台中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Netskope 管理员控制台”应用程序集成页上，找到“管理”部分并选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`<OrgKey>`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 本教程后面的步骤中将介绍这些值。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<tenantname>.goskope.com`

    > [!NOTE]
    > “登录 URL”值不是实际值。 请使用实际登录 URL 更新登录 URL 值。 请联系 [Netskope 管理员控制台客户端支持团队](mailto:support@netskope.com)以获取登录 URL 值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Netskope 管理员控制台应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，Netskope 管理员控制台应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 |  源属性|
    | ---------| --------- |
    | admin-role | user.assignedroles |

    > [!NOTE]
    > 单击[此处](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)以了解如何在 Azure AD 中创建角色。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Netskope 管理员控制台”部分，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Netskope 管理员控制台的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Netskope 管理员控制台”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-netskope-administrator-console-sso"></a>配置 Netskope 管理员控制台 SSO

1. 在浏览器中打开新标签页，并以管理员身分登录 Netskope 管理员控制台公司站点。

1. 在左侧导航窗格中单击“设置”选项卡  。

    ![Netskope 管理员控制台配置](./media/netskope-cloud-security-tutorial/config-settings.png)

1. 单击“管理”选项卡  。

    ![Netskope 管理员控制台配置](./media/netskope-cloud-security-tutorial/config-administration.png)

1. 单击“SSO”选项卡  。

    ![Netskope 管理员控制台配置](./media/netskope-cloud-security-tutorial/config-sso.png)

1. 在“网络设置”部分中执行以下步骤  ：
    
    ![Netskope 管理员控制台配置](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. 复制“断言使用者服务 URL”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中    。

    b. 复制“服务提供商实体 ID”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中    。

1. 单击“SSO/SLO设置”部分下的“编辑设置”   。

    ![Netskope 管理员控制台配置](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. 在“设置”弹出窗口中，执行以下步骤  ；

    ![Netskope 管理员控制台配置](./media/netskope-cloud-security-tutorial/configuration.png)

    a. 选择“启用 SSO”  。

    b. 在“IDP URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 在“IDP 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    d. 在记事本中打开下载的 Base64 编码证书，将其内容复制到剪贴板，并粘贴到“IDP 证书”文本框中  。

    e. 选择“启用 SSO”  。

    f. 在“IDP SLO URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    g. 单击“提交”  。

### <a name="create-netskope-administrator-console-test-user"></a>创建 Netskope 管理员控制台测试用户

1. 在浏览器中打开新标签页，并以管理员身分登录 Netskope 管理员控制台公司站点。

1. 在左侧导航窗格中单击“设置”选项卡  。

    ![Netskope 管理员控制台用户创建](./media/netskope-cloud-security-tutorial/config-settings.png)

1. 单击“活动平台”选项卡  。

    ![Netskope 管理员控制台用户创建](./media/netskope-cloud-security-tutorial/user1.png)

1. 单击“用户”选项卡。 

    ![Netskope 管理员控制台用户创建](./media/netskope-cloud-security-tutorial/add-user.png)

1. 单击“添加用户”  。

    ![Netskope 管理员控制台用户创建](./media/netskope-cloud-security-tutorial/user-add.png)

1. 输入要添加的用户的电子邮件地址，然后单击“添加”  。

    ![Netskope 管理员控制台用户创建](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Netskope 管理员控制台”磁贴时，应会自动登录到为其设置了 SSO 的 Netskope 管理员控制台。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [使用 Azure AD 试用 Netskope 管理员控制台](https://aad.portal.azure.com/)
