---
title: 了解如何使用 Azure 应用配置的快速入门
description: 将 Azure 应用配置与 Java Spring 应用结合使用的快速入门。
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.custom: devx-track-java
ms.author: lcozzens
ms.openlocfilehash: 93a2fd89e21dbf4edee29a27bd18f63f2b835aae
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121215"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>快速入门：使用 Azure 应用配置创建 Java Spring 应用

在本快速入门中，会将 Azure 应用程序配置合并到 Java Spring 应用程序中，以集中存储和管理与代码分离的应用程序设置。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- 受支持的 [Java 开发工具包 (JDK)](https://docs.microsoft.com/java/azure/jdk)，版本为 8。
- [Apache Maven](https://maven.apache.org/download.cgi) 版本 3.0 或更高版本。

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. 选择“配置资源管理器” > “+ 创建” > “键-值”来添加以下键值对    ：

    | 密钥 | 值 |
    |---|---|
    | /application/config.message | 你好 |

    暂时将“标签”和“内容类型”保留为空   。

7. 选择“应用”。 

## <a name="create-a-spring-boot-app"></a>创建 Spring Boot 应用

使用 [Spring Initializr](https://start.spring.io/) 创建一个新 Spring Boot 项目。

1. 浏览到 <https://start.spring.io/>。

1. 指定以下选项：

   - 使用 **Java** 生成一个 **Maven** 项目。
   - 指定一个其值大于或等于 2.0 的 Spring Boot  版本。
   - 指定应用程序的“组”和“项目”名称。  
   - 添加 **Spring Web** 依赖项。

1. 指定上述选项后，选择“生成项目”  。 出现提示时，将项目下载到本地计算机中的路径。

## <a name="connect-to-an-app-configuration-store"></a>连接到应用程序配置存储区

1. 从本地系统提取文件后，即可使用简单的 Spring Boot 应用程序进行编辑。 在应用的根目录中找到 pom.xml 文件  。

1. 在文本编辑器中打开 *pom.xml* 文件，将 Spring Cloud Azure Config Starter 添加到 `<dependencies>` 列表：

    **Spring Cloud 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.5</version>
    </dependency>
    ```

    **Spring Cloud 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.7</version>
    </dependency>
    ```

1. 在应用的包目录中创建名为 MessageProperties.java 的新 Java 文件  。 添加以下行：

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. 在应用的包目录中创建新的名为 HelloController.java 的 Java 文件  。 添加以下行：

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. 打开主应用程序 Java 文件，并添加 `@EnableConfigurationProperties`以启用此功能。

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. 在应用的资源目录下创建名为 `bootstrap.properties` 的新文件，并将以下行添加到该文件中。 使用应用程序配置存储区的相应属性替换示例值。

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. 设置名为“APP_CONFIGURATION_CONNECTION_STRING”的环境变量，并将其设置为应用配置存储的访问密钥  。 在命令行中，运行以下命令并重启命令提示符，以使更改生效：

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    如果使用 Windows PowerShell，请运行以下命令：

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    如果使用 macOS 或 Linux，则请运行以下命令：

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 使用 Maven 生成 Spring Boot 应用程序，然后运行该程序，例如：

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. 应用程序运行以后，请使用 curl 测试该应用程序，例如  ：

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    可看到在应用程序配置存储区中输入的消息。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建新的应用程序配置存储区，并将其用于 Java Spring 应用。 有关详细信息，请参阅 [Azure 上的 Spring](https://docs.microsoft.com/java/azure/spring-framework/)。 若要了解如何使 Java Spring 应用能够动态刷新配置设置，请继续学习下一个教程。

> [!div class="nextstepaction"]
> [启用动态配置](./enable-dynamic-configuration-java-spring-app.md)
