---
title: TextBox UI 元素
description: 介绍了 Azure 门户的 Microsoft.Common.TextBox UI 元素。 用于添加无格式文本。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c89bc434d9d67144a95b5c2f23e7664078fe7825
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87474302"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI 元素

一个可用来编辑无格式文本的控件。

## <a name="ui-sample"></a>UI 示例

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>架构

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "placeholder": "",
    "constraints": {
        "required": true,
        "validations": [
            {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
            },
            {
                "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
                "message": "Must start with 'contoso'."
            }
        ]
    },
    "visible": true
}
```

## <a name="sample-output"></a>示例输出

```json
"contoso123"
```

## <a name="remarks"></a>备注

- 如果 `constraints.required` 设置为 true，则文本框必须包含值才能成功通过验证  。 默认值是 **false**秒。
- `validations`属性是一个数组，您可以在其中添加条件以检查文本框中提供的值。
- `regex`属性为 JavaScript 正则表达式模式。 如果指定，则文本框的值必须与模式完全匹配才能成功通过验证。 默认值为 **null**。
- `isValid`属性包含计算结果为 true 或 false 的表达式。 在表达式中，定义确定文本框是否有效的条件。
- `message`属性是当文本框的值未通过验证时要显示的字符串。
- 当 `required` 设置为 **false** 时可以为 `regex` 指定值。 在这种情况下，文本框并非必须具有值才能成功通过验证。 如果指定了一个值，则它必须与正则表达式模式匹配。
- `placeholder`属性是用户开始编辑时消失的帮助文本。 如果 `placeholder` `defaultValue` 同时定义了和，则将 `defaultValue` 优先使用并显示。

## <a name="example"></a>示例

下面的示例使用带有[ArmApiControl](microsoft-solutions-armapicontrol.md)控件的文本框来检查资源名称的可用性。

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
