---
title: Azure Application Insights 的應用程式對應 | Microsoft Docs
description: 使用應用程式對應監視複雜的應用程式拓撲
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: 0823dd5d880c778f9b7a231ac14f1cbba1940927
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657382"
---
# <a name="application-map-triage-distributed-applications"></a>應用程式對應：對分散式應用程式進行分級

應用程式對應可協助您找出分散式應用程式所有元件的效能瓶頸或失敗熱點。 對應上的每個節點各代表應用程式元件或其相依性；並具有健康情況 KPI 和警示狀態。 您可以從任何元件逐一點選至更詳細的診斷，例如 Application Insights 事件。 如果您的應用程式使用 Azure 服務，您也可以逐一點選 Azure 診斷，例如 SQL 資料庫建議程式的建議。

## <a name="what-is-a-component"></a>什麼是元件？

元件是分散式/微服務應用程式中可獨立部署的組件。 開發人員和作業小組能在程式碼層級檢視或存取這些應用程式元件所產生的遙測資料。 

* 元件不同於 SQL、EventHub 等小組/組織可能無法存取的「可觀察」外部相依性 (程式碼或遙測資料)。
* 元件能在任何數量的伺服器/角色/容器執行個體上執行。
* 元件可以是不同的 Application Insights 檢測金鑰 (即使訂用帳戶不同)，也可以是回報給單一 Application Insights 檢測金鑰的不同角色。 預覽對應體驗會顯示元件 (不論元件的設定方式為何)。

## <a name="composite-application-map"></a>複合應用程式對應

您可以查看跨相關應用程式元件多個層級的完整應用程式拓撲。 元件可以是不同的 Application Insights 資源，或是單一資源中的不同角色。 應用程式對應會尋找元件，方法是遵循已安裝 Application Insights SDK 之伺服器之間所發出的 HTTP 相依性呼叫。 

這項體驗一開始會漸進地探索元件。 首次載入應用程式映射時,將觸發一組查詢,以發現與此元件相關的元件。 在探索到應用程式中的元件時，左上角的按鈕會依探索到的元件數目進行更新。 

當您按一下 [更新對應元件] 時，系統便會使用目前為止所探索的所有元件來重新整理對應。 視應用程式的複雜度，可能需要數分鐘的時間載入。

如果所有元件都是單一 Application Insights 資源內的角色，則不需要進行此探索步驟。 這類應用程式一開始會載入所有元件。

![應用程式對應螢幕擷取畫面](media/app-map/app-map-001.png)

這項新體驗的重要目標之一，是要能夠以視覺化方式顯示含有數百個元件的複雜拓撲。

按一下任何元件，即可查看相關深入資訊，並前往該元件的效能和失敗分級體驗。

![飛出視窗](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>調查失敗

選取 [調查失敗]**** 以啟動 [失敗] 窗格。

![調查失敗按鈕的螢幕擷取畫面](media/app-map/investigate-failures.png)

![失敗體驗的螢幕擷取畫面](media/app-map/failures.png)

### <a name="investigate-performance"></a>調查效能

要解決性能問題,請選擇 **「調查性能**」。

![調查效能按鈕的螢幕擷取畫面](media/app-map/investigate-performance.png)

![效能體驗的螢幕擷取畫面](media/app-map/performance.png)

### <a name="go-to-details"></a>前往詳細資料

選擇 **"轉到詳細資訊**"以探索端到端事務體驗,這些體驗可以提供視圖,下至調用堆疊級別。

![移至詳細資料按鈕的螢幕擷取畫面](media/app-map/go-to-details.png)

![端對端交易詳細資料的螢幕擷取畫面](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>檢視紀錄(分析)

要進一步查詢與調查應用程式資料,請按一**個紀錄(分析)中的檢視**。

![在 Analytics 中檢視按鈕的螢幕擷取畫面](media/app-map/view-logs.png)

![分析體驗的屏幕截圖。 總結過去 12 小時內請求的平均回應持續時間的線圖。](media/app-map/log-analytics.png)

### <a name="alerts"></a>警示

若要檢視作用中警示和導致警示觸發的基礎規則，請選取 [警示]****。

![警示按鈕的螢幕擷取畫面](media/app-map/alerts.png)

![Analytics 體驗的螢幕擷取畫面](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>設定雲角色名稱

應用程式對應使用**雲角色名稱**屬性來標識地圖上的元件。 應用程式見解 SDK 會自動將雲端角色名稱屬性添加到元件發出的遙測數據中。 例如,SDK 會向雲角色名稱屬性添加網站名稱或服務角色名稱。 但是，有時候您可能會想覆寫預設值。 要覆蓋雲端角色名稱並更改應用程式映射中顯示的內容,請執行以下操作:

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

**編寫如下自定義遙測初始化程式。**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET應用:將初始化程式載入到活動遙測設定**

在應用程式的見解設定中 :

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

ASP.NET Web 應用程式的替代方法是在程式碼中實體化初始化程式,例如在 Global.aspx.cs 中:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> 使用`ApplicationInsights.config`或`TelemetryConfiguration.Active`使用 添加初始化程式對 ASP.NET 核心應用程式無效。 

**ASP.NET核心應用:將初始化程式載入遙測設定**

對於[ASP.NET核心](asp-net-core.md#adding-telemetryinitializers)應用程式,通過將其`TelemetryInitializer`添加到依賴項注入容器來完成添加新項,如下所示。 這是用`ConfigureServices`類`Startup.cs`的方法完成的。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

從應用程式見解 Java SDK 2.5.0 開始,`<RoleName>`您`ApplicationInsights.xml`可以通過添加到 檔案來指定雲角色名稱,例如,

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

如果您是使用 Spring Boot 搭配 Application Insights Spring Boot 入門版，只需設定您在 application.properties 檔案中應用程式的自訂名稱。

`spring.application.name=<name-of-app>`

Spring Boot 初學者將自動將雲角色名稱分配給您為spring.application.name屬性輸入的值。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Node.js 的替代方法

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>瞭解應用程式映射中文中的雲端角色名稱

至於如何考慮**雲角色名稱**,查看具有多個雲角色名稱的應用程式映射會很有説明:

![應用程式對應螢幕擷取畫面](media/app-map/cloud-rolename.png)

在上面的應用程式映射中,綠色框中的每個名稱都是此特定分散式應用程式不同方面的雲角色名稱值。 因此,對於這個程式,它的角色包括: `Authentication` `acmefrontend` `Inventory Management`, `Payment Processing Worker Role`, , , , , 

在本應用中,每個雲角色名稱也代表具有其自己的檢測密鑰的不同唯一應用程式見解資源。 由於此應用程式的所有者可以訪問這四個不同的應用程式見解資源,因此應用程式映射能夠將基礎關係的地圖拼接在一起。

對於[官方定義](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

或者,**雲端角色實例**對於**雲角色名稱**告訴您問題位於 Web 前端的某個位置的情況非常有用,但您可能正在多個負載均衡伺服器上運行 Web 前端,因此能夠透過 Kusto 查詢深入一層,並瞭解問題是否影響所有 Web 前端伺服器/實例,或者僅影響一個伺服器,這可能非常重要。

如果應用在容器化環境中運行,而只知道單個伺服器的資訊可能不足以查找給定問題,則可能需要覆蓋雲角色實例的值。

有關如何使用遙測初始化程式重寫雲角色名稱屬性的詳細資訊,請參閱[添加屬性:I遙測初始化器](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)。

## <a name="troubleshooting"></a>疑難排解

如果您無法讓應用程式對應如預期般運作，請嘗試以下步驟：

### <a name="general"></a>一般

1. 請確定您使用的是正式支援的 SDK。 不支援的 SDK 或社群 SDK 可能不支援相互關聯。

    有關受支援的 SDK 清單,請參閱[本文](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)。

2. 請將所有元件都升級為最新的 SDK 版本。

3. 如果您是將 C# 搭配 Azure Functions 使用，請升級至 [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions)。

4. 確認[雲角色名稱](#set-cloud-role-name)配置正確。

5. 如果缺少相依性，請確定相依性是否列在這份[自動收集的相依性](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) (英文) 清單中。 如果不在此清單中，仍可以使用[追蹤相依性呼叫](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency) (英文)，以手動方式進行追蹤。

### <a name="too-many-nodes-on-the-map"></a>地圖上的節點太多

應用程式對應為請求遙測中存在的每個唯一雲端角色名稱建構一個應用程式節點,以及依賴項遙測中類型、目標和雲端角色名稱的每個唯一組合的依賴項節點。 如果遙測中有超過 10,000 個節點,則應用程式映射將無法獲取所有節點和連結,因此地圖將不完整。 如果發生這種情況,查看地圖時將顯示一條警告消息。

此外,應用程式映射僅支援一次渲染的多達 1000 個獨立未分組節點。 應用程式映射通過將具有相同類型和調用方的依賴項分組在一起來降低視覺複雜性,但如果遙測具有太多唯一的雲角色名稱或過多的依賴項類型,則該分組將不足,並且地圖將無法呈現。

要解決此問題,您需要更改檢測以正確設置雲角色名稱、依賴項類型和依賴項目標欄位。

* 依賴項目標應表示依賴項的邏輯名稱。 在許多情況下,它等效於依賴項的伺服器或資源名稱。 例如,對於 HTTP 依賴項,它設置為主機名。 它不應包含從一個請求更改為另一個請求的唯一 ID 或參數。

* 依賴項類型應表示依賴項的邏輯類型。 例如,HTTP、SQL 或 Azure Blob 是典型的依賴關係類型。 它不應包含唯一 ID。

* 雲角色名稱的用途在[上一節中](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name)介紹。

## <a name="portal-feedback"></a>入口網站意見反應

若要提供意見反應，請使用意見反應選項。

![MapLink-1 影像](./media/app-map/14-updated.png)

## <a name="next-steps"></a>後續步驟

* 要瞭解有關相關性在應用程式見解中的工作方式,請參閱[遙測相關文章](correlation.md)。
* [端到端事務診斷體驗](transaction-diagnostics.md)將來自所有應用程式見解監控元件的伺服器端遙測關聯到單個視圖中。
* 有關ASP.NET核心和ASP.NET的高級關聯方案,請參閱[跟蹤自定義操作](custom-operations-tracking.md)一文。
