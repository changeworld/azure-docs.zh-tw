---
title: 如何 - 準備 Java Spring 應用程式以部署在 Azure 春雲中
description: 在本主題中,準備一個 Java Spring 應用程式以部署到 Azure Spring Cloud。
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 16cee333d52765755b732c4de4dd8a6e092a130d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731170"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>準備 Java Spring 應用程式以部署到 Azure Spring Cloud

本主題演示如何準備現有的 JAVA Spring 應用程式以部署到 Azure Spring 雲。 若能夠正確設定，Azure Spring Cloud 將能提供豐富的服務以監視、調整及更新您的 Java Spring Cloud 應用程式。

其他範例說明在已設定 POM 檔案時，如何將應用程式部署至 Azure Spring Cloud。 
* [使用 Azure 入口網站啟動應用程式](spring-cloud-quickstart-launch-app-portal.md)
* [使用 Azure CLI 啟動應用程式](spring-cloud-quickstart-launch-app-cli.md)

本文說明必要的相依性，以及如何將其新增至 POM 檔案。

## <a name="java-runtime-version"></a>Java 執行階段版本

只有 Spring/Java 應用程式可以在 Azure Spring Cloud 中執行。

Azure Spring Cloud 支援 Java 8 和 Java 11。 裝載環境會包含適用於 Azure 的最新版 Azul Zulu OpenJDK。 如需適用於 Azure 的 Azul Zulu OpenJDK 詳細資訊，請參閱[安裝 JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)。

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 和 Spring Cloud 版本

若要準備現有的 Spring Boot 應用程式部署至 Azure Spring Cloud，請在應用程式 POM 檔案中納入 Spring Boot 和 Spring Cloud 相依性，如下列各節所示。

Azure Spring Cloud 僅支援 Spring Boot 應用程式 (Spring Boot 2.1 版或 2.2 版)。 下表列出支援的 Spring Boot 和 Spring Cloud 組合：

Spring Boot 版本 | Spring Cloud 版本
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Spring Boot 2.1 版的相依性

針對 Spring Boot 2.1 版，請將下列相依性新增至應用程式 POM 檔案。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Spring Boot 2.2 版的相依性

針對 Spring Boot 2.2 版，請將下列相依性新增至應用程式 POM 檔案。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud 用戶端相依性

Azure Spring Cloud 會裝載和管理 Spring Cloud 元件。 這些元件包含 Spring Cloud Service Registry 和 Spring Cloud Config Server。 在您的相依性中包含 Azure Spring Cloud 用戶端程式庫，以允許和您 Azure Spring Cloud 服務執行個體之間的通訊。

下表列出適用於應用程式且使用 Spring Boot 和 Spring Cloud 的正確 Azure Spring Cloud 版本。

Spring Boot 版本 | Spring Cloud 版本 | Azure Spring Cloud 版本
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2.2 | Hoxton.RELEASE | 2.2

請在 pom.xml 檔案中包含下列其中一個相依性。 選取其 Azure Spring Cloud 版本與您所擁有版本相符的相依性。

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Azure Spring Cloud 2.1 版的相依性

針對 Spring Boot 2.1 版，請將下列相依性新增至應用程式 POM 檔案。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Azure Spring Cloud 2.2 版的相依性

針對 Spring Boot 2.2 版，請將下列相依性新增至應用程式 POM 檔案。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>其他必要的相依性

若要啟用 Azure Spring Cloud 的內建功能，您的應用程式必須包含下列相依性。 如果有包含，便能確保您的應用程式能針對每個元件正確自我設定。

### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient 註釋

將下列註釋新增至應用程式原始程式碼。
```java
@EnableDiscoveryClient
```
例如，請查看先前範例中的 piggymetrics 應用程式：
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="service-registry-dependency"></a>Service Registry 相依性

若要使用受控的 Azure Service Registry 服務，請在 pom.xml 檔案中包含 `spring-cloud-starter-netflix-eureka-client` 相依性，如下所示：

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Service Registry 伺服器的端點會自動搭配您的應用程式插入為環境變數。 應用程式接著可以向 Service Registry 伺服器註冊自己，並探索其他相依的微服務。

### <a name="distributed-configuration-dependency"></a>分散式設定相依性

若要啟用分散式設定，請在 pom.xml 檔案的 [相依性] 區段中包含下列 `spring-cloud-config-client` 相依性：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> 請勿在啟動程序設定中指定 `spring.cloud.config.enabled=false`。 否則，應用程式會停止使用 Config Server。

### <a name="metrics-dependency"></a>計量相關性

請在 pom.xml 檔案的 [相依性] 區段中包含 `spring-boot-starter-actuator` 相依性，如下所示：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 系統會定期從 JMX 端點提取計量。 您可以使用 Azure 入口網站將計量視覺化。

### <a name="distributed-tracing-dependency"></a>分散式追蹤相依性

請在 pom.xml 檔案的 [相依性] 區段中包含下列 `spring-cloud-starter-sleuth` 和 `spring-cloud-starter-zipkin` 相依性：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 您也需要啟用 Azure Application Insights 執行個體，以搭配您的 Azure Spring Cloud 服務執行個體運作。 請閱讀[關於分散式追蹤的教學課程](spring-cloud-tutorial-distributed-tracing.md)，以了解如何搭配使用 Application Insights 與 Azure Spring Cloud。

## <a name="see-also"></a>另請參閱
* [分析應用程式記錄和計量](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [設定您的 Config Server](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [搭配使用分散式追蹤與 Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Spring 快速入門指南](https://spring.io/quickstart)
* [Spring Boot 文件](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>後續步驟

在本主題中,您將瞭解如何配置 Java Spring 應用程式以部署到 Azure Spring Cloud。 要瞭解如何設置 Config Server 實例,請參閱以下文章。

> [!div class="nextstepaction"]
> [了解如何設定 Config Server 執行個體](spring-cloud-tutorial-config-server.md)

GitHub 上可用的其他範例：[Azure Spring Cloud 範例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)。
