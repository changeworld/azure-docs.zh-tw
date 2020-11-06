---
title: 如何準備應用程式以在 Azure 春季雲端中進行部署
description: 瞭解如何準備應用程式以部署到 Azure 春季雲端。
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 9e613331760a1715c3821bdc7dbbf0469e8bfd97
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337605"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>準備要在 Azure 中部署的應用程式（春季雲端）

::: zone pivot="programming-language-csharp"
Azure 春季 Cloud 提供健全的服務來裝載、監視、調整及更新 Steeltoe 應用程式。 本文說明如何準備現有的 Steeltoe 應用程式，以部署至 Azure 春季雲端。 

本文說明在 Azure 春季雲端中執行 .NET Core Steeltoe 應用程式所需的相依性、設定和程式碼。 如需如何將應用程式部署到 Azure 春季雲端的詳細資訊，請參閱 [部署您的第一個 Azure 春季 cloud 應用程式](spring-cloud-quickstart.md)。

>[!Note]
> Steeltoe 對於 Azure Spring Cloud 的支援目前是以公開預覽版的形式來提供。 公開預覽版供應項目可讓客戶在其正式發行前，先試驗新功能。  公開預覽功能和服務不適用於生產環境。  如需預覽期間的支援詳細資訊，請參閱[常見問題集](https://azure.microsoft.com/support/faq/)或提出[支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)。

##  <a name="supported-versions"></a>支援的版本

Azure 春季雲端支援：

* .NET Core 3.1
* Steeltoe 2.4 和3。0

## <a name="dependencies"></a>相依性

若為 Steeltoe 2.4，請將最新的 [SpringCloud](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) 新增至專案檔：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

若為 Steeltoe 3.0，請將最新的 [SpringCloud](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) 新增至專案檔：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>更新 Program.cs

在 `Program.Main` 方法中，呼叫 `UseAzureSpringCloudService` 方法。

若為 Steeltoe 2.4.4，請 `UseAzureSpringCloudService` 在呼叫之後 `ConfigureWebHostDefaults` 和之後呼叫 `AddConfigServer` ：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

針對 Steeltoe 3.0.0，請 `UseAzureSpringCloudService` 在 `ConfigureWebHostDefaults` 任何 Steeltoe 設定程式碼之前和之前呼叫：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>啟用 Eureka 伺服器服務探索

當應用程式在 Azure 春季雲端中執行時所使用的設定來源，會設定 `spring.application.name` 為與要部署專案之 Azure 春季雲端應用程式相同的名稱。

例如，如果您將名為的 .NET 專案部署 `EurekaDataProvider` 至名為的 Azure 春季雲端應用程式，則該應用程式的 `planet-weather-provider` *appSettings.js* 應包含下列 JSON：

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>使用服務探索

若要使用 Eureka Server 服務探索呼叫服務，請將 HTTP 要求設定為 `http://<app_name>` `app_name` `spring.application.name` 目標應用程式的值。 例如，下列程式碼會呼叫 `planet-weather-provider` 服務：

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
本主題會示範如何準備現有的 Java Spring 應用程式以部署到 Azure Spring Cloud。 若能夠正確設定，Azure Spring Cloud 將能提供豐富的服務以監視、調整及更新您的 Java Spring Cloud 應用程式。

執行此範例之前，您可以嘗試[基本快速入門](spring-cloud-quickstart.md)。

其他範例說明在已設定 POM 檔案時，如何將應用程式部署至 Azure Spring Cloud。 
* [啟動您的第一個應用程式](spring-cloud-quickstart.md)
* [建立並執行微服務](spring-cloud-quickstart-sample-app-introduction.md)

本文說明必要的相依性，以及如何將其新增至 POM 檔案。

## <a name="java-runtime-version"></a>Java 執行階段版本

只有 Spring/Java 應用程式可以在 Azure Spring Cloud 中執行。

Azure Spring Cloud 支援 Java 8 和 Java 11。 裝載環境會包含適用於 Azure 的最新版 Azul Zulu OpenJDK。 如需適用於 Azure 的 Azul Zulu OpenJDK 詳細資訊，請參閱[安裝 JDK](/azure/developer/java/fundamentals/java-jdk-install)。

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 和 Spring Cloud 版本

若要準備現有的 Spring Boot 應用程式部署至 Azure Spring Cloud，請在應用程式 POM 檔案中納入 Spring Boot 和 Spring Cloud 相依性，如下列各節所示。

Azure Spring Cloud 僅支援 Spring Boot 應用程式 (Spring Boot 2.1 版或 2.2 版)。 下表列出支援的 Spring Boot 和 Spring Cloud 組合：

Spring Boot 版本 | Spring Cloud 版本
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.SR8
2.3 | Hoxton.SR8

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
                <version>Greenwich.RELEASE</version>
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
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### <a name="dependencies-for-spring-boot-version-23"></a>彈簧開機版本2.3 的相依性

針對彈簧開機版本2.3，請將下列相依性新增至應用程式 POM 檔案。

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
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud 用戶端相依性

Azure Spring Cloud 會裝載和管理 Spring Cloud 元件。 這些元件包含 Spring Cloud Service Registry 和 Spring Cloud Config Server。 建議使用彈簧開機2.2 或2.3。 針對彈簧開機2.1，您必須在您的相依性中包含 Azure 春季 Cloud 用戶端程式庫，以允許與您的 Azure 春季雲端服務實例進行通訊。

下表列出適用於應用程式且使用 Spring Boot 和 Spring Cloud 的正確 Azure Spring Cloud 版本。

Spring Boot 版本 | Spring Cloud 版本 | Azure 春季 Cloud 用戶端入門版
---|---|---
2.1.x | Greenwich.RELEASE | 2.1.2
2.2. x | Hoxton.SR8 | 不需要
2.3. x | Hoxton.SR8 | 不需要

如果您使用的是春季開機2.1，請在 pom.xml 檔案中包含下列 dependenciy。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```
> [!WARNING]
> 請勿 `server.port` 在您的設定中指定。 Azure 春季雲端會將此設定覆寫為固定的埠號碼。 也請遵循這項設定，而不要在您的程式碼中指定伺服器埠。

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
 > 請 `spring.jmx.enabled=true` 在您的 configuration 屬性中指定。 否則，計量無法在 Azure 入口網站中視覺化。

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

 您也需要啟用 Azure Application Insights 執行個體，以搭配您的 Azure Spring Cloud 服務執行個體運作。 如需有關如何搭配 Azure 春季雲端使用 Application Insights 的詳細資訊，請參閱 [分散式追蹤的相關檔](spring-cloud-tutorial-distributed-tracing.md)。

## <a name="see-also"></a>另請參閱
* [分析應用程式記錄和計量](./diagnostic-services.md)
* [設定您的 Config Server](./spring-cloud-tutorial-config-server.md)
* [搭配使用分散式追蹤與 Azure Spring Cloud](./spring-cloud-tutorial-distributed-tracing.md)
* [Spring 快速入門指南](https://spring.io/quickstart)
* [Spring Boot 文件](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>後續步驟

在此主題中，您已了解如何設定 Java Spring 應用程式以部署至 Azure Spring Cloud。 若要瞭解如何設定 Config Server 實例，請參閱 [設定設定伺服器實例](spring-cloud-tutorial-config-server.md)。

GitHub 上可用的其他範例：[Azure Spring Cloud 範例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)。
::: zone-end