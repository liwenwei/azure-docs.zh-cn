---
title: 教程 - 部署多容器组 - 模板
description: 本教程介绍如何通过 Azure CLI 使用 Azure 资源管理器模板在 Azure 容器实例中部署包含多个容器的容器组。
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc
ms.openlocfilehash: cb085112c6e6458d897f52f19988e6301d4ae6e8
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259573"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>教程：使用资源管理器模板部署多容器组

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [资源管理器](container-instances-multi-container-group.md)

Azure 容器实例支持使用[容器组](container-instances-container-groups.md)将多个容器部署到单台主机上。 当生成应用程序 Sidecar 以用于日志记录、监视或某些其他配置（其中的服务需要第二个附加进程）时，容器组很有用。

在本教程中，按照以下步骤，使用 Azure CLI 部署 Azure 资源管理器模板，以运行简单的双容器 Sidecar 配置。 学习如何：

> [!div class="checklist"]
> * 配置多容器组模板
> * 部署容器组
> * 查看容器的日志

当你需要使用容器组部署其他 Azure 服务资源（例如 Azure 文件存储或虚拟网络）时，可以很容易地根据场景调整资源管理器模板。 

> [!NOTE]
> 多容器组当前仅限于 Linux 容器。 

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>配置模板

首先将以下 JSON 复制到名为 `azuredeploy.json` 的新文件。 在 Azure Cloud Shell 中，可以使用 Visual Studio Code 在工作目录中创建文件：

```
code azuredeploy.json
```

此资源管理器模板定义的容器组包含两个容器、一个公共 IP 地址和两个公开端口。 该组中的第一个容器运行面向 Internet 的 Web 应用程序。 第二个容器 sidecar 通过该组的本地网络向主要 Web 应用程序发出 HTTP 请求。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": 80
            },
            {
                "protocol": "tcp",
                "port": 8080
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

若要使用专用容器映像注册表，请采用以下格式向 JSON 文档添加对象。 有关此配置的示例实现，请参阅 [ACI 资源管理器模板引用][template-reference]文档。

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>部署模板

使用“[az group create][az-group-create]”命令创建资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az deployment group create][az-deployment-group-create] 命令部署模板。

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
```

将在几秒钟内收到来自 Azure 的初始响应。

## <a name="view-deployment-state"></a>查看部署状态

若要查看部署状态，请运行下面的 [az container show][az-container-show] 命令：

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

若要查看正在运行的应用程序，请在浏览器中转到它的 IP 地址。 例如，在此示例输出中，IP 为 `52.168.26.124`：

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>查看容器日志

使用 [az container logs][az-container-logs] 命令查看容器的日志输出。 `--container-name` 参数指定从中拉取日志的容器。 在此示例中，指定 `aci-tutorial-app` 容器。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

输出：

```bash
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

若要查看 Sidecar 容器的日志，请运行指定 `aci-tutorial-sidecar` 容器的类似命令。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

输出：

```bash
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

如你所见，sidecar 通过该组的本地网络定期向主 Web 应用程序发出 HTTP 请求，确保其正在运行。 如果此 Sidecar 示例收到的 HTTP 响应代码不是 `200 OK`，则可将其扩展以触发警报。

## <a name="next-steps"></a>后续步骤

在本教程中，你使用了 Azure 资源管理器模板在 Azure 容器实例中部署多容器组。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 配置多容器组模板
> * 部署容器组
> * 查看容器的日志

有关模板示例的详细信息，请参阅[适用于 Azure 容器实例的 Azure 资源管理器模板](container-instances-samples-rm.md)。

还可使用 [YAML 文件](container-instances-multi-container-yaml.md)指定多容器组。 由于 YAML 格式更简洁，因此，当部署仅包含容器实例时，使用 YAML 文件进行部署是很好的选择。


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
