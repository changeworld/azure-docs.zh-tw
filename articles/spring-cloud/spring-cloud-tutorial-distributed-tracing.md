---
title: 搭配使用分散式追蹤與 Azure Spring Cloud
description: 了解如何透過 Azure Application Insights 使用 Spring Cloud 的分散式追蹤
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 97926d5bdf3123ae50714d36ad0234872f67aa96
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908301"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>搭配使用分散式追蹤與 Azure Spring Cloud

透過 Azure Spring Cloud 中的分散式追蹤工具，您可以輕鬆地針對複雜問題進行偵錯和監視。 Azure Spring Cloud 可整合 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 與 Azure 的 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 這項整合可讓您從 Azure 入口網站獲得強大的分散式追蹤功能。

::: zone pivot="programming-language-csharp"
在本文中，您將瞭解如何讓 .NET Core Steeltoe 應用程式使用分散式追蹤。

## <a name="prerequisites"></a>必要條件

若要遵循這些程式，您需要 Steeltoe 應用程式，該應用程式已 [準備好部署至 Azure 春季雲端](spring-cloud-tutorial-prepare-app-deployment.md)。

## <a name="dependencies"></a>相依性

安裝下列 NuGet 套件

* [Steeltoe. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe. ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

## <a name="update-startupcs"></a>更新 Startup.cs

1. 在 `ConfigureServices` 方法中，呼叫 `AddDistributedTracing` 和 `AddZipkinExporter` 方法。

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

1. 在 `Configure` 方法中，呼叫 `UseTracingExporter` 方法。

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseTracingExporter();
   }
   ```

## <a name="update-configuration"></a>更新設定

將下列設定新增至在 Azure 春季雲端中執行應用程式時將使用的設定來源：

1. 將 `management.tracing.alwaysSample` 設定為 True。

2. 如果您想要查看 Eureka 伺服器、設定伺服器和使用者應用程式之間傳送的追蹤範圍：設定 `management.tracing.egressIgnorePattern` 為 "/api/v2/spans |/v2/apps/.*/permissions |/eureka/.*|/oauth/.*".

例如， *appsettings.js* 會包含下列屬性：
 
```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

如需 .NET Core Steeltoe 應用程式中分散式追蹤的詳細資訊，請參閱 Steeltoe 檔中的 [分散式追蹤](https://steeltoe.io/docs/3/tracing/distributed-tracing) 。
::: zone-end
::: zone pivot="programming-language-java"
在本文中，您將學會如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中啟用分散式追蹤。
> * 將 Spring Cloud Sleuth 新增至您的應用程式。
> * 檢視微服務應用程式的相依性對應。
> * 搭配不同的篩選搜尋追蹤資料。

## <a name="prerequisites"></a>必要條件

若要遵循這些程序，您需要已佈建好且正在執行的 Azure Spring Cloud 服務。 完成 [部署您的第一個 Azure 春季 cloud 應用程式](spring-cloud-quickstart.md) 快速入門，以布建和執行 Azure 春季雲端服務。

## <a name="add-dependencies"></a>新增相依性

1. 將下面這行新增至 application.properties 檔案：

   ```xml
   spring.zipkin.sender.type = web
   ```

   完成此變更後，Zipkin 傳送者便能傳送至 Web。

1. 如果您已遵循我們[針對準備 Azure Spring Cloud 應用程式的指南](spring-cloud-tutorial-prepare-app-deployment.md)，則請略過此步驟。 否則，請移至您的本機開發環境，並編輯您的 pom.xml 檔案，以包含下列 Spring Cloud Sleuth 相依性：

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. 再次針對您的 Azure Spring Cloud 服務進行建置及部署以反映這些變更。

## <a name="modify-the-sample-rate"></a>修改採樣速率

您可以修改採樣速率來變更收集遙測的速率。 例如，如果您想要和往常一樣取樣一半，請開啟 application.properties 檔案，然後變更下面這行：

```xml
spring.sleuth.sampler.probability=0.5
```

如果您已建置並部署應用程式，則可以修改採樣速率。 若要這麼做，請在 Azure CLI 或 Azure 入口網站中將前面這行新增為環境變數。
::: zone-end

## <a name="enable-application-insights"></a>啟用 Application Insights

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務頁面。
1. 在 [監視] 頁面上，選取 [分散式追蹤]。
1. 選取 [編輯設定] 以編輯或新增新的設定。
1. 建立新的 Application Insights 查詢或選取現有查詢。
1. 選擇您想要監視的記錄類別，並指定保留期間 (以天為單位)。
1. 選取 [套用] 來套用新的追蹤。

## <a name="view-the-application-map"></a>檢視應用程式對應

返回 [分散式追蹤] 頁面，並選取 [檢視應用程式對應]。 檢閱您應用程式及監視設定的視覺效果呈現。 若要了解如何使用應用程式對應，請參閱[應用程式對應：將分散式應用程式分級](https://docs.microsoft.com/azure/azure-monitor/app/app-map)。

## <a name="use-search"></a>使用搜尋

使用搜尋功能來查詢其他特定的遙測項目。 在 [分散式追蹤] 頁面上，選取 [搜尋]。 如需如何使用搜尋功能的詳細資訊，請參閱 [在 Application Insights 中使用搜尋](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search)。

## <a name="use-application-insights"></a>使用 Application Insights

除了應用程式對應和搜尋功能之外，Application Insights 也提供監視功能。 在 Azure 入口網站中搜尋您應用程式的名稱，然後開啟 Application Insights 頁面以尋找監視資訊。 如需如何使用這些工具的詳細指引，請參閱 [Azure 監視器記錄查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

## <a name="disable-application-insights"></a>停用 Application Insights

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務頁面。
1. 在 [監視] 上，選取 [分散式追蹤]。
1. 選取 [停用] 以停用 Application Insights。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何在 Azure Spring Cloud 中啟用及理解分散式追蹤。 若要了解如何將服務繫結至應用程式，請參閱[將 Azure Cosmos DB 資料庫繫結至 Azure Spring Cloud 應用程式](spring-cloud-tutorial-bind-cosmos.md)。
