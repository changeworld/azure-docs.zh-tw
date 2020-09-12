---
title: 搭配使用分散式追蹤與 Azure Spring Cloud
description: 了解如何透過 Azure Application Insights 使用 Spring Cloud 的分散式追蹤
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 1ff76c38031ac367bf81f6d152642a4d9a209bb7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89293994"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>搭配使用分散式追蹤與 Azure Spring Cloud

透過 Azure Spring Cloud 中的分散式追蹤工具，您可以輕鬆地針對複雜問題進行偵錯和監視。 Azure Spring Cloud 可整合 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 與 Azure 的 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 這項整合可讓您從 Azure 入口網站獲得強大的分散式追蹤功能。

在本文中，您將了解如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中啟用分散式追蹤。
> * 將 Spring Cloud Sleuth 新增至您的應用程式。
> * 檢視微服務應用程式的相依性對應。
> * 搭配不同的篩選搜尋追蹤資料。

## <a name="prerequisites"></a>必要條件

若要遵循這些程序，您需要已佈建好且正在執行的 Azure Spring Cloud 服務。 請完成[關於如何透過 Azure CLI 來部署應用程式的快速入門](spring-cloud-quickstart.md)，以佈建和執行 Azure Spring Cloud 服務。
    
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
