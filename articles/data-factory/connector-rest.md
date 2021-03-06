---
title: 使用 Azure 数据工厂从 REST 源复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从云或本地 REST 源复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jingwang
ms.openlocfilehash: c0a64c0a9653bd274e9298401163ad7abc1af99f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852287"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 REST 终结点复制数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂中的复制活动从 REST 终结点复制数据。 本文是根据总体概述复制活动的 [Azure 数据工厂中的复制活动](copy-activity-overview.md)编写的。

此 REST 连接器、 [HTTP 连接器](connector-http.md)和[Web 表连接器](connector-web-table.md)之间的差异如下：

- **REST 连接器**专门支持从 RESTful API 复制数据； 
- **Http 连接器**是从任何 http 终结点检索数据（例如，下载文件）的泛型。 在此 REST 连接器可用之前，可以偶尔使用 HTTP 连接器从 RESTful API 复制数据，这是受支持的，但 HTTP 连接器与 REST 连接器相比功能较少。
- **Web 表连接器**用于从 HTML 网页中提取表内容。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 REST 源复制到任何支持的接收器数据存储。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

具体而言，此泛型 REST 连接器支持：

- 使用 **GET** 或 **POST** 方法从 REST 终结点检索数据。
- 使用以下身份验证方法之一检索数据：“匿名”、“基本”、“AAD 服务主体”和“Azure 资源的托管标识”。    
- REST API 中的 **[分页](#pagination-support)** 。
- [按原样](#export-json-response-as-is)复制 REST JSON 响应，或使用[架构映射](copy-activity-schema-and-type-mapping.md#schema-mapping)对其进行分析。 仅支持 **JSON** 格式的响应有效负载。

> [!TIP]
> 若要在数据工厂中配置 REST 连接器之前测试数据检索请求，请了解标头和正文的 API 规范要求。 可以使用 Postman 或 Web 浏览器等工具进行验证。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 REST 连接器的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

REST 链接服务支持以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **RestService**  。 | 是 |
| url | REST 服务的基 URL。 | 是 |
| enableServerCertificateValidation | 连接到终结点时是否要验证服务器端 TLS/SSL 证书。 | 否<br /> （默认值为 true）  |
| authenticationType | 用于连接到 REST 服务的身份验证类型。 允许的值为**Anonymous**、 **Basic**、 **AadServicePrincipal**和**ManagedServiceIdentity**。 有关其他属性和示例，请参阅下面的相应部分。 | 是 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 从[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则此属性使用默认 Azure Integration Runtime。 |否 |

### <a name="use-basic-authentication"></a>使用基本身份验证

将 **authenticationType** 属性设置为 **Basic**。 除了前面部分所述的通用属性，还指定以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| userName | 用于访问 REST 终结点的用户名。 | 是 |
| password | 用户（userName 值）的密码  。 将此字段标记为 SecureString 类型，以便安全地将其存储在数据工厂中  。 此外，还可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 是 |

**示例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-aad-service-principal-authentication"></a>使用 AAD 服务主体身份验证

将 **authenticationType** 属性设置为 **AadServicePrincipal**。 除了前面部分所述的通用属性，还指定以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定 Azure Active Directory 应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定 Azure Active Directory 应用程序的密钥。 将此字段标记为 **SecureString** 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |
| aadResourceId | 指定你请求授权的 AAD 资源，例如 `https://management.core.windows.net` 。| 是 |
| azureCloudType | 对于 "服务主体身份验证"，请指定 AAD 应用程序注册到的 Azure 云环境的类型。 <br/> 允许的值为**AzurePublic**、 **AzureChina**、 **AzureUsGovernment**和**AzureGermany**。 默认情况下，使用数据工厂的云环境。 | 否 |

**示例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>使用托管标识进行 Azure 资源身份验证

将 **authenticationType** 属性设置为 **ManagedServiceIdentity**。 除了前面部分所述的通用属性，还指定以下属性：

| 属性 | 描述 | 必须 |
|:--- |:--- |:--- |
| aadResourceId | 指定你请求授权的 AAD 资源，例如 `https://management.core.windows.net` 。| 是 |

**示例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

本部分提供 REST 数据集支持的属性列表。 

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 

若要从 REST 复制数据，支持以下属性：

| 属性 | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 **type** 属性必须设置为 **RestResource**。 | 是 |
| relativeUrl | 包含数据的资源的相对 URL。 未指定此属性时，仅使用链接服务定义中指定的 URL。 HTTP 连接器从以下组合 URL 复制数据：`[URL specified in linked service]/[relative URL specified in dataset]`。 | 否 |

如果在数据集中设置了 `requestMethod`、`additionalHeaders`、`requestBody` 和 `paginationRules`，则仍按原样支持该数据集，但建议你以后在活动源中使用新模型。

**示例：**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分提供 REST 源支持的属性列表。

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 

### <a name="rest-as-source"></a>REST 作为源

复制活动**source**部分支持以下属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为 **RestSource**。 | 是 |
| requestMethod | HTTP 方法。 允许的值为 Get（默认值）和 Post   。 | 否 |
| additionalHeaders | 附加的 HTTP 请求标头。 | 否 |
| requestBody | HTTP 请求的正文。 | 否 |
| paginationRules | 用于撰写下一页请求的分页规则。 有关详细信息，请参阅[分页支持](#pagination-support)部分。 | 否 |
| httpRequestTimeout | 用于获取响应的 HTTP 请求的超时 （TimeSpan 值）  。 该值是获取响应而不是读取响应数据的超时。 默认值为 00:01:40  。  | 否 |
| requestInterval | 发送下一页请求之前等待的时间。 默认值为 **00:00:01** |  否 |

>[!NOTE]
>REST 连接器会忽略 `additionalHeaders` 中指定的任何“Accept”标头。 由于 REST 连接器仅支持 JSON 中的响应，它会自动生成 `Accept: application/json` 标头。

**示例 1：对分页使用 Get 方法**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**示例 2：使用 Post 方法**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>分页支持

通常，REST API 将单个请求的响应有效负载大小限制在合理的数字以下；返回大量的数据时，它会将结果拆分到多个页面，并要求调用方发送连续的请求来获取下一页结果。 一般情况下，一个页面的请求是动态的，由上一页响应中返回的信息构成。

此泛型 REST 连接器支持以下分页模式： 

* 下一个请求的绝对或相对 URL = 当前响应正文中的属性值
* 下一个请求的绝对或相对 URL = 当前响应标头中的标头值
* 下一个请求的查询参数 = 当前响应正文中的属性值
* 下一个请求的查询参数 = 当前响应标头中的标头值
* 下一个请求的标头 = 当前响应正文中的属性值
* 下一个请求的标头 = 当前响应标头中的标头值

**分页规则**在 dataset 中定义为字典，其中包含一个或多个区分大小写的键/值对。 该配置将用于从第二页开始生成请求。 当连接器收到 HTTP 状态代码 204（无内容），或者“paginationRules”中的任意 JSONPath 表达式返回 null 时，连接器将停止迭代。

分页规则中**支持的键**：

| 键 | 说明 |
|:--- |:--- |
| AbsoluteUrl | 指示用于发出下一个请求的 URL。 它可以是**绝对 URL 或相对 URL**。 |
| QueryParameters.*request_query_parameter* 或 QueryParameters['request_query_parameter'] | "request_query_parameter" 是用户定义的，它引用下一个 HTTP 请求 URL 中的一个查询参数名称。 |
| Headers.*request_header* 或 Headers['request_header'] | "request_header" 是用户定义的，它引用下一个 HTTP 请求中的标头名称。 |

分页规则中**支持的值**：

| Value | 说明 |
|:--- |:--- |
| Headers.*response_header* 或 Headers['response_header'] | "response_header" 是用户定义的，它引用当前 HTTP 响应中的一个标头名称，其值将用于发出下一个请求。 |
| 以“$”（表示响应正文的根）开头的 JSONPath 表达式 | 响应正文应只包含一个 JSON 对象。 JSONPath 表达式应返回单个基元值，该值用于发出下一个请求。 |

**示例：**

Facebook 图形 API 返回采用以下结构的响应，在此情况下，下一页的 URL 将在 ***paging.next*** 中表示：

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

相应的 REST 复制活动源配置（尤其是 `paginationRules`）如下所示：

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>使用 OAuth
本部分介绍如何使用解决方案模板通过 OAuth 将 REST 连接器中 JSON 格式的数据复制到 Azure Data Lake Storage。 

### <a name="about-the-solution-template"></a>关于解决方案模板

该模板包含两个活动：
- “Web”活动检索持有者令牌，然后将其作为 Authorization 标头传递到后续的“复制”活动  。
- “复制”活动将数据从 REST 复制到 Azure Data Lake Storage  。

该模板定义两个参数：
- SinkContainer 是 Azure Data Lake Storage 中的根文件夹路径，可以将数据复制到其中  。 
- SinkDirectory 是 Azure Data Lake Storage 中的根目录下的目录路径，可以将数据复制到其中  。 

### <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到“使用 OAuth 模板从 REST 或 HTTP 复制”模板  。 对于“源连接”，请创建新连接。 
    ![创建新连接](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    下面是新链接服务 (REST) 设置的关键步骤：
    
     1. 在“基 URL”下，为自己的源 REST 服务指定 URL 参数  。 
     2. 对于“身份验证类型”，请选择“匿名”   。
        ![新建 REST 连接](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. 对于“目标连接”，请创建新连接。  
    ![新建 Gen2 连接](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. 选择“使用此模板”  。
    ![使用此模板](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. 此时会看到创建的管道，如以下示例所示：![管道](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. 选择“Web”活动  。 在“设置”中，指定相应的“URL”、“方法”、“标头”和“正文”，以便从要从其中复制数据的服务的登录 API 检索 OAuth 持有者令牌      。 模板中的占位符展示了 Azure Active Directory (AAD) OAuth 的示例。 请注意，REST 连接器原生支持 AAD 身份验证，这里只是 OAuth 流的一个示例。 

    | 属性 | 描述 |
    |:--- |:--- |:--- |
    | URL |指定要从其中检索 OAuth 持有者令牌的 URL。 例如，在示例中，它是https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | 方法 | HTTP 方法。 允许的值为“Post”和“Get”   。 | 
    | 头文件 | 标头由用户定义，引用 HTTP 请求中的一个标头名称。 | 
    | 正文 | HTTP 请求的正文。 | 

    ![管道](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. 在“复制数据”活动中选择“源”选项卡。可以看到，从上一步骤检索的持有者令牌 (access_token) 将作为“其他标头”下的 Authorization 标头传递到“复制数据”活动    。 在启动管道运行之前，请确认以下属性的设置。

    | 属性 | 说明 |
    |:--- |:--- |:--- | 
    | 请求方法 | HTTP 方法。 允许的值为 Get（默认值）和 Post   。 | 
    | 其他标头 | 附加的 HTTP 请求标头。| 

   ![复制源身份验证](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. 选择“调试”，输入**参数**，然后选择“完成”。  
   ![管道运行](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. 管道运行成功完成后，会看到类似于以下示例的结果：![管道运行结果](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. 在“操作”列中单击 WebActivity 的“输出”图标，会看到服务返回的 access_token  。

   ![令牌输出](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. 在“操作”列中单击 CopyActivity 的“输入”图标，会看到 WebActivity 检索的 access_token 已传递到 CopyActivity 进行身份验证  。 

    ![令牌输入](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >若要避免以纯文本格式记录令牌，请在“Web”活动中启用“安全输出”，并在“复制”活动中启用“安全输入”。


## <a name="export-json-response-as-is"></a>按原样导出 JSON 响应

可以使用此 REST 连接器将 REST API JSON 响应按原样导出到各种基于文件的存储。 若要实现这种架构不可知的复制，请跳过数据集中的“结构”（也称为“架构”）节和复制活动中的架构映射  。

## <a name="schema-mapping"></a>架构映射

若要将数据从 REST 终结点复制到表格接收器，请参阅[架构映射](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
