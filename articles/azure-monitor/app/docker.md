---
title: 在 Azure Application Insights 中監視 Docker 應用程式 | Microsoft Docs
description: Docker 效能計數器、事件和例外狀況可以與來自容器化應用程式的遙測一起顯示在 Application Insights 上。
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 272b4e8af7b1ed3d01d8af0979b56954585f795d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90977584"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>在 Application Insights (中監視 Docker 應用程式已淘汰) 

> [!NOTE]
> 此解決方案已被取代。 若要深入了解我們目前對於容器監視所做的投資，建議查看[適用於容器的 Azure 監視器](../insights/container-insights-overview.md)。

[Docker](https://www.docker.com/) 容器的週期事件和效能計數器可以在 Application Insights 上繪製成圖表。 在您的主機的容器中安裝 [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) 映像，它會顯示主機及其他映像的效能計數器。

使用 Docker，您可以在完成所有相依性的輕量級容器中散發應用程式。 它們會在執行 Docker 引擎的任何主機電腦上執行。

當您在 Docker 主機上執行 [Application Insights 映像 (英文)](https://hub.docker.com/r/microsoft/applicationinsights/) 時，可為您提供下列好處：

* 主機上執行之所有容器的相關週期遙測 - 啟動、停止等。
* 所有容器的效能計數器。 CPU、記憶體、網路使用量等。
* 如果您在於容器中執行的應用程式中[安裝了 Application Insights SDK for Java](./java-get-started.md)，則這些應用程式的所有遙測將會有可識別容器和主機電腦的額外屬性。 例如，如果您在多部主機上執行某個應用程式的多個執行個體，您可以輕鬆地依主機來篩選應用程式遙測。

## <a name="set-up-your-application-insights-resource"></a>設定您的 Application Insights 資源

1. 登入 [Microsoft Azure 入口網站](https://azure.com) 並開啟您應用程式的 Application Insights 資源;或 [建立一個新的](./create-new-resource.md)。 
   
    *我應該使用哪種資源？* 如果您在主機上執行的應用程式是由其他人所開發，則您需要[建立新的 Application Insights 資源](./create-new-resource.md)。 這是您檢視及分析遙測的位置 (針對應用程式類型選取 [一般])。
   
    但如果您是應用程式的開發人員，我們希望您 [將 Application Insights SDK 加入](./java-get-started.md) 每個應用程式中。 如果這些應用程式其實全部都是單一商務應用程式的元件，則您可能會設定所有應用程式將遙測資料傳送至一個資源，再使用該相同的資源來顯示 Docker 週期和效能資料。 
   
    第三種情況是您已開發大部分應用程式，但想要使用不同的資源來顯示其遙測。 在這種情況下，您可能也需要為 Docker 資料建立不同的資源。

2. 按一下 [程式集] **** 下拉式清單，然後複製檢測金鑰。 您將會使用這個項目以告知 SDK 傳送遙測的位置。

保持該瀏覽器視窗就緒，因為您稍後即會返回以查看您的遙測。

## <a name="run-the-application-insights-monitor-on-your-host"></a>在您的主機上執行 Application Insights 監視器

當您有其他位置可顯示遙測之後，您可以設定收集和傳送遙測的容器化應用程式。

1. 連接到您的 Docker 主機。
2. 在這個命令中編輯您的檢測金鑰，然後執行命令：
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

每部 Docker 主機只需要一個 Application Insights 映像。 如果您的應用程式部署在多部 Docker 主機上，請在每部主機上重複執行命令。

## <a name="update-your-app"></a>更新應用程式
如果使用 [Application Insights SDK for Java](./java-get-started.md) 檢測您的應用程式，請將下行新增到您專案之 ApplicationInsights.xml 檔案中的 `<TelemetryInitializers>` 元素底下：

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

這會將 Docker 資訊（例如容器和主機識別碼）新增至從您的應用程式傳送的每個遙測專案。

## <a name="view-your-telemetry"></a>檢視遙測
返回 Azure 入口網站中的 Application Insights 資源。

點選 [Docker] 磚。

您很快就會看到來自 Docker 應用程式的資料抵達，尤其是如果您在 Docker 引擎上有其他容器執行。

### <a name="docker-container-events"></a>Docker 容器事件
![螢幕擷取畫面顯示選取 [搜尋]、[診斷搜尋結果] 視窗，其中有箭號指向自訂事件的視窗和自訂資料。](./media/docker/13.png)

若要調查個別事件，請按一下 [搜尋](./diagnostic-search.md)。 搜尋和篩選以尋找您想要的事件。 按一下任何事件以查看詳細資料。

### <a name="exceptions-by-container-name"></a>依據容器名稱的例外狀況
![螢幕擷取畫面顯示已選取圖表的計量瀏覽器頁面，並在右側開啟 [圖表詳細資料] 視窗。](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>已加入應用程式遙測中的 Docker 內容
從使用 AI SDK 檢測之應用程式送出的要求遙測，會以 Docker 內容資訊進行擴充。

## <a name="q--a"></a>問答集
*Application Insights 可以給我哪些無法從 Docker 取得的功能？*

* 依據容器和映像的效能計數器的詳細分解圖。
* 將容器和應用程式資料整合在一個儀表板中。
* [匯出遙測](export-telemetry.md) 以進行資料庫、Power BI 或其他儀表板的進一步分析。

*如何從應用程式本身取得遙測？*

* 在應用程式中安裝 Application Insights SDK。 針對下列項目深入了解：[Java Web 應用程式](./java-get-started.md)、[Windows Web 應用程式](./asp-net.md)。

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>後續步驟

* [適用於 Java 的 Application Insights](./java-get-started.md)
* [適用於 Node.js 的 Application Insights](./nodejs.md)
* [適用於 ASP.NET 的 Application Insights](./asp-net.md)

