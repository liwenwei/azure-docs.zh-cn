---
title: 使用 Azure Active Directory B2C 设置直接登录 | Microsoft Docs
description: 了解如何预填充登录名或直接重定向到社交标识提供者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9e7c537e85039675f27fa3e276b6b964ce1679b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388589"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置直接登录

使用 Azure Active Directory (AD) B2C 为应用程序设置登录时，可以预填充登录名或直接登录到特定的社交标识提供者，例如 Facebook、LinkedIn 或 Microsoft 帐户。

## <a name="prepopulate-the-sign-in-name"></a>预填充登录名

在登录用户旅程中，信赖方应用程序可以针对特定用户或域名。 当针对用户时，应用程序可以在授权请求中使用用户登录名指定 `login_hint` 查询参数。 Azure AD B2C 自动填充登录名，而用户只需提供密码。

![URL 中突出显示了 login_hint 查询参数的注册登录页](./media/direct-signin/login-hint.png)

用户可以更改登录文本框中的值。

如果使用自定义策略，将重写 `SelfAsserted-LocalAccountSignin-Email` 技术配置文件。 在 `<InputClaims>` 节中，将 signInName 声明的 DefaultValue 设置为 `{OIDC:LoginHint}`。 `{OIDC:LoginHint}` 变量包含 `login_hint` 参数的值。 Azure AD B2C 将读取 signInName 声明的值并预填充 signInName 文本框。

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="redirect-sign-in-to-a-social-provider"></a>将登录重定向到社交提供者

如果已将应用程序的登录旅程配置为包括社交帐户（如 Facebook、LinkedIn 或 Google），则可以指定 `domain_hint` 参数。 此查询参数向 Azure AD B2C 提供有关应该用于登录的社交标识提供者的提示。 例如，如果应用程序指定 `domain_hint=facebook.com`，则登录会直接转到 Facebook 登录页。

![在 URL 中突出显示 domain_hint 查询参数时注册登录页](./media/direct-signin/domain-hint.png)

如果使用自定义策略，则可以使用任何 `<ClaimsProvider>` 的 `<Domain>domain name</Domain>` XML 元素配置域名。

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


