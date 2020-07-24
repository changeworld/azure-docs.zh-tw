---
title: 使用 Azure Application Insights 進行使用量分析 | Microsoft Docs
description: 了解您的使用者，以及他們如何運用您的應用程式。
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 1acc803d66bef29a77266d4e38e1605ffd75f36f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014060"
---
# <a name="usage-analysis-with-application-insights"></a>使用 Application Insights 進行使用量分析

Web 或行動應用程式的哪些功能最受歡迎？ 您的使用者是否利用您的應用程式達到其目標呢？ 他們會在特定點退出，並在稍後返回嗎？  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 可協助您深入了解使用者如何使用應用程式。 每次您更新應用程式時，都可以評估它適用於使用者的程度。 您可以透過了解這些來制定下一個開發週期的相關資料導向決策。

## <a name="send-telemetry-from-your-app"></a>傳送來自您應用程式的遙測

若要獲得最佳體驗，請同時在您的應用程式伺服器程式碼和網頁中安裝 Application Insights。 您應用程式的用戶端和伺服器元件會將遙測資料傳送回 Azure 入口網站以供分析。

1. **伺服器程式碼：** 為您的 [ASP.NET](../../azure-monitor/app/asp-net.md)、[Azure](../../azure-monitor/app/app-insights-overview.md)、[Java](../../azure-monitor/app/java-get-started.md)、[Node.js](../../azure-monitor/app/nodejs.md) 或[其他](../../azure-monitor/app/platforms.md)應用程式安裝適當的模組。

    * *不想安裝伺服器程式碼嗎？請直接[建立 Azure Application Insights 資源](../../azure-monitor/app/create-new-resource.md )。*

2. **網頁程式碼：** 將下列指令碼新增至網頁的右 ``</head>`` 之前。 使用 Application Insights 資源的適當值來取代檢測金鑰：
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    若要深入了解用於監視網站的進階組態，請參閱 [JavaScript SDK 參考](./javascript.md) \(部分機器翻譯\) 一文。

3. **行動應用程式程式碼：** 使用 App Center SDK 從應用程式收集事件，然後將這些事件的複本傳送至 Application Insights，[按照此指南](../../azure-monitor/learn/mobile-center-quickstart.md)的指示進行分析。

4. **取得遙測資料：** 以偵錯模式執行您的專案幾分鐘，然後在 Application Insights 的 [概觀] 刀鋒視窗中尋找結果。

    發佈您的應用程式以監視應用程式的效能，並了解使用者如何利用您的應用程式。

## <a name="include-user-and-session-id-in-your-telemetry"></a>將使用者與工作階段識別碼加入您的遙測
若要追蹤使用者一段時間，Application Insights 需要識別他們的方式。 「事件工具」是唯一不需要使用者識別碼或工作階段識別碼的「使用量工具」。

使用[此流程](./usage-send-user-context.md)開始傳送使用者與工作階段識別碼。

## <a name="explore-usage-demographics-and-statistics"></a>探索使用量人口統計和統計資料
了解人們在使用您的應用程式時，他們最感興趣的網頁、使用者的所在位置，以及他們使用何種瀏覽器與作業系統。 

使用者與工作階段報告會依頁面或自訂事件來篩選資料，並透過諸如位置、環境及頁面等屬性，將這些頁面或自訂事件進行區隔。 您也可以新增自己的篩選條件。

![使用者](./media/usage-overview/users.png)  

右方情資指出資料集內的有趣模式。  

* **使用者**報告會在您所選擇的時間週期內，計算存取您網頁的唯一使用者數目。 對於 Web 應用程式，會使用 Cookie 來計算使用者。 如果有人使用不同的瀏覽器或用戶端電腦來存取您的網站，或清除其 Cookie，系統就會將他們計算為多次。
* **工作階段**報告會計算存取您網站之使用者工作階段的數目。 工作階段是使用者活動的一段時間，在閒置時間超過半小時後就會加以終止。

[進一步了解「使用者」、「工作階段」和「事件」工具](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>保留期 - 回來使用的使用者人數？

保留期可根據同群使用者在特定時間貯體期間執行的某些商務動作，協助您了解使用者返回使用其應用程式的頻率。 

- 了解相較於其他功能，哪些特定功能會讓使用者回來使用 
- 根據實際使用者資料的表單假設 
- 判斷保留期是否為您產品的問題 

![保留](./media/usage-overview/retention.png) 

在最上層的保留期控制項可讓您定義用來計算保留期的特定事件和時間範圍。 中間的圖表會依指定的時間範圍提供整體保留期百分比的視覺表示法。 底部圖表代表指定時間內的個別保留期。 此詳細資料等級會使用更詳細的資料粒度，讓您了解使用者在做什麼，以及可能會影響舊有使用者的因素。  

[深入了解保留期工具](usage-retention.md)

## <a name="custom-business-events"></a>自訂商務事件

若要清楚了解使用者會使用您的應用程式來做什麼，插入程式碼行來記錄自訂事件會很有用。 這些事件可以追蹤任何項目，從詳細的使用者動作 (例如按一下特定按鈕)，到更重要的商務事件 (例如購物或遊戲獲勝)。 

雖然在某些情況下，頁面檢視可以代表有用的事件，但一般而言它並不正確。 使用者可以開啟產品網頁而無需購買產品。 

您可以使用特定商務事件，透過網站將使用者的進度製作成圖表。 您可以找出他們對不同選項的偏好，以及它們退出或遇到困難之處。 您可以透過了解這些對開發待處理項目的優先順序做出明智決策。

事件可透過應用程式的用戶端來記錄下來：

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

或者，也可以透過伺服器端：

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

您可以將屬性值附加至這些事件，當您在入口網站中檢查它們時，就可以將事件進行篩選或分割。 此外，會將一組標準的屬性附加至每個事件，例如匿名使用者識別碼，這可讓您追蹤個別使用者的活動順序。

深入了解[自訂事件](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)和[屬性](../../azure-monitor/app/api-custom-events-metrics.md#properties)。

### <a name="slice-and-dice-events"></a>將事件進行交叉分析

在「使用者」、「工作階段」和「事件」工具中，您可以依使用者、事件名稱和屬性將自訂事件進行交叉分析。
![使用者](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>使用應用程式來設計遙測

當您在設計您應用程式的每項功能時，請考慮要如何利用使用者來測量它的成功。 決定您需要記錄哪些商務事件，並從開頭將這些事件的追蹤呼叫編碼至應用程式。

## <a name="a--b-testing"></a>A | B 測試
如果您不知道哪個功能的變數將更可能成功，請兩者都釋出，讓兩者都可供不同使用者存取。 測量每一個的成功，然後移至整合的版本。

針對此技術，您會將獨特的屬性值附加到每個應用程式版本所傳送的所有遙測。 您可以在作用中 TelemetryContext 中定義屬性來執行該動作。 這些預設屬性會加入到應用程式傳送的每個遙測訊息 - 不只是您的自訂訊息，還有標準遙測。

在 Application Insights 入口網站中，將資料依屬性值篩選並分割，以便比較不同版本。

若要這樣做，[請設定遙測初始設定式](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)：

**ASP.NET 應用程式**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

在 Web 應用程式初始設定式例如 Global.asax.cs 中：

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core 應用程式**

> [!NOTE]
> 使用 `ApplicationInsights.config` 或使用 `TelemetryConfiguration.Active` 新增初始設定式不適用於 ASP.NET Core 應用程式。 

針對 [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) 應用程式，加入新的 `TelemetryInitializer` 是藉由將其新增至相依性插入容器來完成，如下所示。 這會在 `Startup.cs` 類別的 `ConfigureServices` 方法中完成。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

所有新的 TelemetryClients 都會自動新增您指定的屬性值。 個別的遙測事件可以覆寫預設值。

## <a name="next-steps"></a>後續步驟
   - [使用者、工作階段、事件](usage-segmentation.md)
   - [漏斗圖](usage-funnels.md)
   - [保留](usage-retention.md)
   - [使用者流程](usage-flows.md)
   - [活頁簿](../../azure-monitor/platform/workbooks-overview.md)
   - [新增使用者內容](usage-send-user-context.md)
