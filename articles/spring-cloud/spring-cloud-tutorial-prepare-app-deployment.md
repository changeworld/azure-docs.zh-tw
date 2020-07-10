---
title: 如何準備 JAVA 春季應用程式以在 Azure 春季雲端中進行部署
description: 瞭解如何準備要部署至 Azure 春季雲端的 JAVA 春季應用程式。
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 1840979fc1380f1e0d14f3a0a74eef5fa6df6538
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86141863"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>準備 Java Spring 應用程式以部署到 Azure Spring Cloud

本主題會示範如何準備現有的 Java Spring 應用程式以部署到 Azure Spring Cloud。 若能夠正確設定，Azure Spring Cloud 將能提供豐富的服務以監視、調整及更新您的 Java Spring Cloud 應用程式。

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
2.3 | Hoxton.SR5

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
### <a name="dependencies-for-spring-boot-version-23"></a>春季開機版本2.3 的相依性

針對春季開機版本2.3，請將下列相依性新增至應用程式 POM 檔案。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR5</version>
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
2.3 | Hoxton.SR5 | 2.3

請在 pom.xml 檔案中包含下列其中一個相依性。 選取其 Azure Spring Cloud 版本與您所擁有版本相符的相依性。

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Azure Spring Cloud 2.1 版的相依性

針對 Spring Boot 2.1 版，請將下列相依性新增至應用程式 POM 檔案。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Azure Spring Cloud 2.2 版的相依性

針對 Spring Boot 2.2 版，請將下列相依性新增至應用程式 POM 檔案。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.1</version>
</dependency>
```

針對春季開機版本2.3，請將下列相依性新增至應用程式 POM 檔案。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.3.0</version>
</dependency>
```

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>啟用 Azure Spring Cloud 功能的其他建議相依性

若要啟用從容器登錄到分散式追蹤的 Azure Spring Cloud 內建功能，您也需要在應用程式中包含下列相依性。 如果您不需要特定應用程式的對應功能，可以捨棄其中一些相依性。

### <a name="service-registry"></a>服務登錄

若要使用受控的 Azure Service Registry 服務，請在 pom.xml 檔案中包含 `spring-cloud-starter-netflix-eureka-client` 相依性，如下所示：

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Service Registry 伺服器的端點會自動搭配您的應用程式插入為環境變數。 應用程式接著可以向 Service Registry 伺服器註冊自己，並探索其他相依的微服務。

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient 註釋

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

### <a name="distributed-configuration"></a>分散式設定

若要啟用分散式設定，請在 pom.xml 檔案的 [相依性] 區段中包含下列 `spring-cloud-config-client` 相依性：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> 請勿在啟動程序設定中指定 `spring.cloud.config.enabled=false`。 否則，應用程式會停止使用 Config Server。

### <a name="metrics"></a>計量

請在 pom.xml 檔案的 [相依性] 區段中包含 `spring-boot-starter-actuator` 相依性，如下所示：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 系統會定期從 JMX 端點提取計量。 您可以使用 Azure 入口網站將計量視覺化。

 > [!WARNING]
 > 請 `spring.jmx.enabled=true` 在您的 configuration 屬性中指定。 否則，將無法在 Azure 入口網站中視覺化計量。

### <a name="distributed-tracing"></a>分散式追蹤

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

 您也需要啟用 Azure Application Insights 執行個體，以搭配您的 Azure Spring Cloud 服務執行個體運作。 如需如何搭配 Azure 春季雲端使用 Application Insights 的相關資訊，請參閱[分散式追蹤的相關檔](spring-cloud-tutorial-distributed-tracing.md)。

## <a name="see-also"></a>另請參閱
* [分析應用程式記錄和計量](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [設定您的 Config Server](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [搭配使用分散式追蹤與 Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Spring 快速入門指南](https://spring.io/quickstart)
* [Spring Boot 文件](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>後續步驟

在此主題中，您已了解如何設定 Java Spring 應用程式以部署至 Azure Spring Cloud。 若要瞭解如何設定設定伺服器實例，請參閱[設定 Config server 實例](spring-cloud-tutorial-config-server.md)。

GitHub 上可用的其他範例：[Azure Spring Cloud 範例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)。
