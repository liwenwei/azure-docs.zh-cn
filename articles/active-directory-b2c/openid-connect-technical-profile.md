---
title: 在自定义策略中定义 OpenID Connect 技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中定义 OpenID Connect 技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5a83a8ab608e685f43056debe45877965e0c53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85203988"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 OpenID Connect 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 为 [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) 协议标识提供者提供支持。 OpenID Connect 1.0 在 OAuth 2.0 基础上提供一个标识层，代表现代身份验证协议的最新技术。 使用 OpenID Connect 技术配置文件，可以与基于 OpenID Connect 的标识提供者（如 Azure AD）联合。 与标识提供者联合允许用户使用其现有的社交或企业标识登录。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `OpenIdConnect`。 例如，**MSA-OIDC** 技术配置文件的协议为 `OpenIdConnect`：

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>输入声明

**InputClaims** 和 **InputClaimsTransformations** 元素不是必需的。 但是，你可能需要向标识提供者发送更多参数。 以下示例将值为 `contoso.com` 的 **domain_hint** 查询字符串参数添加到授权请求。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>输出声明

**OutputClaims**元素包含由 OpenID connect 标识提供程序返回的声明列表。 可能需要将策略中定义的声明名称映射到标识提供者中定义的名称。 如果设置了 `DefaultValue` 属性，则还可以包含标识提供者不会返回的声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

以下示例演示 Microsoft Account 标识提供者返回的声明：

- 映射到**issuerUserId**声明的**子**声明。
- 映射到 **displayName** 声明的 **name** 声明。
- 没有名称映射的 **email**。

技术配置文件还会返回标识提供者不返回的声明：

- **identityProvider** 声明，其中包含标识提供者的名称。
- **authenticationSource** 声明，其默认值为 **socialIdpAuthentication**。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>元数据

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| client_id | 是 | 标识提供者的应用程序标识符。 |
| IdTokenAudience | 否 | id_token 的受众。 如果指定，Azure AD B2C 将检查 `aud` 标识提供者返回的令牌中的声明是否等于 IdTokenAudience 元数据中指定的声明。  |
| METADATA | 是 | 指向 OpenID Connect 标识提供程序配置文档的 URL，也称为 OpenID 众所周知的配置终结点。 URL 可以包含 `{tenant}` 表达式，该表达式将替换为租户名称。  |
| authorization_endpoint | 否 | 指向 OpenID Connect 标识提供程序配置授权终结点的 URL。 Authorization_endpoint 元数据的值优先于 `authorization_endpoint` OpenID 众所周知的配置终结点中指定的。 URL 可包含 `{tenant}` 表达式，该表达式将替换为租户名称。 |
| 颁发者 | 否 | OpenID Connect 标识提供程序的唯一标识符。 颁发者元数据的值优先于 `issuer` OpenID 众所周知的配置终结点中指定的值。  如果已指定，则 Azure AD B2C 检查 `iss` 标识提供者返回的令牌中的声明是否等于颁发者元数据中指定的声明。 |
| ProviderName | 否 | 标识提供者的名称。  |
| response_types | 否 | 符合 OpenID Connect Core 1.0 规范的响应类型。 可能的值：`id_token`、`code` 或 `token`。 |
| response_mode | 否 | 标识提供者在将结果发送回 Azure AD B2C 时使用的方法。 可能的值：`query`、`form_post`（默认值）或 `fragment`。 |
| scope | 否 | 根据 OpenID Connect Core 1.0 规范定义的请求的范围。 例如 `openid`、`profile` 和 `email`。 |
| HttpBinding | 否 | 预期的 HTTP 绑定，绑定到访问令牌和声明令牌终结点。 可能的值：`GET` 或 `POST`。  |
| ValidTokenIssuerPrefixes | 否 | 一个密钥，可以在使用多租户标识提供者（例如 Azure Active Directory）时用于登录到每个租户。 |
| UsePolicyInRedirectUri | 否 | 指示在构造重定向 URI 时是否要使用策略。 在标识提供者中配置应用程序时，需指定重定向 URI。 重定向 URI 指向 Azure AD B2C `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`。  如果指定 `false`，需为每个使用的策略添加重定向 URI。 例如：`https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`。 |
| MarkAsFailureOnStatusCode5xx | 否 | 指示在 Http 状态代码处于 5xx 范围内的情况下，是否应将对外部服务的请求标记为失败。 默认值为 `false`。 |
| DiscoverMetadataByTokenIssuer | 否 | 指示是否应使用 JWT 令牌中的颁发者来发现 OIDC 元数据。 |
| IncludeClaimResolvingInClaimsHandling  | 否 | 对于输入和输出声明，指定[声明解析](claim-resolver-overview.md)是否包含在技术配置文件中。 可能的值：`true` 或 `false` （默认值）。 若要使用技术配置文件中的声明解析程序，请将此项设为 `true`。 |

### <a name="ui-elements"></a>UI 元素
 
以下设置可用于配置失败时显示的错误消息。 应在 OpenID Connect 技术配置文件中配置元数据。 可以将错误消息[本地化](localization-string-ids.md#sign-up-or-sign-in-error-messages)。

| 属性 | 必需 | 描述 |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | 否 | 如果在目录中找不到具有提供的用户名的帐户，则显示给用户的消息。 |
| UserMessageIfInvalidPassword | 否 | 密码不正确时向用户显示的消息。 |
| UserMessageIfOldPasswordUsed| 否 |  当使用旧密码时向用户显示的消息。|

## <a name="cryptographic-keys"></a>加密密钥

**CryptographicKeys** 元素包含以下属性：

| 特性 | 必需 | 描述 |
| --------- | -------- | ----------- |
| client_secret | 是 | 标识提供者应用程序的客户端机密。 只有在将 **response_types** 元数据设置为 `code` 的情况下，才需要加密密钥。 在这种情况下，Azure AD B2C 会再次进行调用，以便用授权代码来交换访问令牌。 如果元数据已设置为 `id_token`，则可省略加密密钥。  |

## <a name="redirect-uri"></a>重定向 URI

配置标识提供者的重定向 URI 时，请输入 `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`。 确保将 `{your-tenant-name}` 替换为你的租户名称。 重定向 URI 需要采用全小写形式。

示例：

- [使用自定义策略添加 Microsoft Account (MSA) 作为标识提供者](identity-provider-microsoft-account-custom.md)
- [使用 Azure AD 帐户登录](identity-provider-azure-ad-single-tenant-custom.md)
- [让用户使用自定义策略登录到多租户 Azure AD 标识提供者](identity-provider-azure-ad-multi-tenant-custom.md)
