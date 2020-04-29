---
title: Azure Application Insights 的應用程式對應 | Microsoft Docs
description: 使用應用程式對應監視複雜的應用程式拓撲
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: 7c5c9173704535b1e34ffde5867bd512e3e02ed8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80989522"
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

這項體驗一開始會漸進地探索元件。 當您第一次載入應用程式對應時，會觸發一組查詢，以探索與此元件相關的元件。 在探索到應用程式中的元件時，左上角的按鈕會依探索到的元件數目進行更新。 

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

若要針對效能問題進行疑難排解，請選取 [**調查效能**]。

![調查效能按鈕的螢幕擷取畫面](media/app-map/investigate-performance.png)

![效能體驗的螢幕擷取畫面](media/app-map/performance.png)

### <a name="go-to-details"></a>前往詳細資料

選取 [**移至詳細資料**] 以探索端對端交易體驗，其可提供向下到呼叫堆疊層級的查看。

![移至詳細資料按鈕的螢幕擷取畫面](media/app-map/go-to-details.png)

![端對端交易詳細資料的螢幕擷取畫面](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>查看記錄（分析）

若要進一步查詢及調查應用程式資料，請按一下 **[記錄（分析）] 中的 [查看**]。

![在 Analytics 中檢視按鈕的螢幕擷取畫面](media/app-map/view-logs.png)

![分析體驗的螢幕擷取畫面。 摘要說明過去12小時內要求的平均回應持續時間的折線圖。](media/app-map/log-analytics.png)

### <a name="alerts"></a>警示

若要檢視作用中警示和導致警示觸發的基礎規則，請選取 [警示]****。

![警示按鈕的螢幕擷取畫面](media/app-map/alerts.png)

![Analytics 體驗的螢幕擷取畫面](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>設定雲端角色名稱

應用程式對應會使用 [**雲端角色名稱**] 屬性來識別地圖上的元件。 Application Insights SDK 會自動將 [雲端角色名稱] 屬性新增至元件所發出的遙測。 例如，SDK 會將網站名稱或服務角色名稱新增至 [雲端角色名稱] 屬性。 但是，有時候您可能會想覆寫預設值。 若要覆寫雲端角色名稱，並變更應用程式對應上顯示的內容：

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

**撰寫自訂 TelemetryInitializer，如下所示。**

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

**ASP.NET apps：將初始化運算式載入到使用中的 TelemetryConfiguration**

在 ApplicationInsights 中：

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

ASP.NET Web 應用程式的替代方法是在程式碼中具現化初始化運算式，例如在 Global.aspx.cs 中：

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
> 使用`ApplicationInsights.config`或 using `TelemetryConfiguration.Active`加入初始化運算式對 ASP.NET Core 應用程式而言是不正確。 

**ASP.NET Core 應用程式：將初始化運算式載入至 TelemetryConfiguration**

針對[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers)應用程式，加入新`TelemetryInitializer`的是藉由將它新增至相依性插入容器來完成，如下所示。 這會在您`ConfigureServices` `Startup.cs`的類別的方法中完成。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**JAVA 代理程式**

針對[JAVA 代理程式 3.0](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) ，雲端角色名稱設定如下：

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

您也可以使用環境變數```APPLICATIONINSIGHTS_ROLE_NAME```來設定雲端角色名稱。

**Java SDK**

如果您使用的是 SDK，從 Application Insights JAVA SDK 2.5.0 開始，您可以藉由新增`<RoleName>`至您`ApplicationInsights.xml`的檔案來指定雲端角色名稱，例如

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

春季開機 starter 會自動將雲端角色名稱指派給您為 spring.application.name 屬性輸入的值。

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

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>瞭解應用程式對應內容中的雲端角色名稱

就像如何思考**雲端角色名稱**一樣，查看具有多個雲端角色名稱的應用程式對應可能會很有説明：

![應用程式對應螢幕擷取畫面](media/app-map/cloud-rolename.png)

在 [應用程式對應] 中，每個綠色方塊中的名稱都是此特定分散式應用程式不同層面的 [雲端角色名稱] 值。 因此，在此應用程式中，其`Authentication`角色`acmefrontend`包含`Inventory Management`：、 `Payment Processing Worker Role`、、a。 

在此應用程式的案例中，每個雲端角色名稱也代表不同的唯一 Application Insights 資源，以及自己的檢測金鑰。 因為此應用程式的擁有者可以存取這四個不同的 Application Insights 資源，所以應用程式對應能夠將基礎關聯性的對應結合在一起。

若為[官方定義](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)：

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

或者，**雲端角色實例**對於**雲端角色名稱**指出問題是在 web 前端某處的案例很有説明，但您可能會在多個負載平衡伺服器上執行 web 前端，讓您能夠透過 Kusto 查詢更深入地深入探索，並知道問題是否會影響所有 web 前端伺服器/實例，或是只是一個非常重要的情況。

如果您的應用程式是在容器化環境中執行，您可能想要覆寫雲端角色實例的值，而這種情況下，只要知道個別伺服器可能沒有足夠的資訊來找出指定的問題。

如需如何使用遙測初始化運算式覆寫雲端角色名稱屬性的詳細資訊，請參閱[新增屬性： ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)。

## <a name="troubleshooting"></a>疑難排解

如果您無法讓應用程式對應如預期般運作，請嘗試以下步驟：

### <a name="general"></a>一般

1. 請確定您使用的是正式支援的 SDK。 不支援的 SDK 或社群 SDK 可能不支援相互關聯。

    如需支援的 Sdk 清單，請參閱這[篇文章](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)。

2. 請將所有元件都升級為最新的 SDK 版本。

3. 如果您是將 C# 搭配 Azure Functions 使用，請升級至 [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions)。

4. 確認已正確設定[雲端角色名稱](#set-cloud-role-name)。

5. 如果缺少相依性，請確定相依性是否列在這份[自動收集的相依性](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) (英文) 清單中。 如果不在此清單中，仍可以使用[追蹤相依性呼叫](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency) (英文)，以手動方式進行追蹤。

### <a name="too-many-nodes-on-the-map"></a>對應上有太多節點

應用程式對應會針對您的要求遙測中出現的每個唯一雲端角色名稱，以及相依性遙測中類型、目標和雲端角色名稱的每個唯一組合，來構造應用程式節點。 如果您的遙測中有超過10000個節點，應用程式對應將無法提取所有節點和連結，因此您的對應將會是不完整的。 如果發生這種情況，則在觀看對應時，將會出現警告訊息。

此外，應用程式對應僅支援最多1000個個別轉譯的已取消群組節點。 應用程式對應會將具有相同類型和呼叫端的相依性分組在一起，以降低視覺複雜度，但如果您的遙測有太多唯一的雲端角色名稱或太多相依性類型，則該群組將會不足，而且該對應將無法呈現。

若要修正此問題，您必須變更檢測，以適當地設定 [雲端角色名稱]、[相依性類型] 和 [相依性目標] 欄位。

* 相依性目標應該代表相依性的邏輯名稱。 在許多情況下，它相當於相依性的伺服器或資源名稱。 例如，在 HTTP 相依性的情況下，它會設定為主機名。 它不應該包含唯一的識別碼，或從一個要求變更為另一個的參數。

* 相依性類型應代表相依性的邏輯類型。 例如，HTTP、SQL 或 Azure Blob 都是典型的相依性類型。 它不應該包含唯一的識別碼。

* [上一節](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name)將說明雲端角色名稱的用途。

## <a name="portal-feedback"></a>入口網站意見反應

若要提供意見反應，請使用意見反應選項。

![MapLink-1 影像](./media/app-map/14-updated.png)

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解相互關聯的運作方式 Application Insights 參閱[遙測相互關聯一文](correlation.md)。
* [端對端交易診斷體驗](transaction-diagnostics.md)會將伺服器端的遙測資料從您的所有 Application Insights 監視元件，相互關聯至單一視圖。
* 如需 ASP.NET Core 和 ASP.NET 中的先進相互關聯案例，請參閱[追蹤自訂作業](custom-operations-tracking.md)一文。
