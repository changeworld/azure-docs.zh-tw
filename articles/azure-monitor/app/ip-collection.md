---
title: Azure 應用程式 Insights IP 位址集合 |Microsoft Docs
description: 瞭解如何使用 Azure 應用程式 Insights 處理 IP 位址和地理位置
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6131105ef78a8559b0fb95043a87e562e887ebfd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333302"
---
# <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 位址處理

本文說明地理位置查閱和 IP 位址處理在 Application Insights 中的運作方式，以及如何修改預設行為。

## <a name="default-behavior"></a>預設行為

預設會暫時收集 IP 位址，但不會儲存在 Application Insights 中。 基本程序如下：

將遙測資料傳送至 Azure 時，IP 位址會用來 [從 MaxMind 使用 GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)進行地理位置查閱。 此查閱的結果會用來填入欄位 `client_City` 、 `client_StateOrProvince` 和 `client_CountryOrRegion` 。 接著會捨棄位址，並 `0.0.0.0` 將其寫入 `client_IP` 欄位。

* 瀏覽器遙測：我們會暫時收集寄件者的 IP 位址。 IP 位址是由內嵌端點所計算。
* 伺服器遙測： Application Insights 遙測模組會暫時收集用戶端 IP 位址。 設定標頭時，不會在本機收集 IP 位址 `X-Forwarded-For` 。

此行為是設計來協助避免不必要的個人資料收集。 建議您盡可能避免收集個人資料。 

## <a name="overriding-default-behavior"></a>覆寫預設行為

預設值是不要收集 IP 位址。 我們仍提供覆寫此行為的彈性。 不過，我們建議您確認集合不會違反任何合規性需求或當地法規。 

若要深入瞭解 Application Insights 中的個人資料處理，請參閱 [個人資料的指引](../platform/personal-data-mgmt.md)。

## <a name="storing-ip-address-data"></a>儲存 IP 位址資料

若要啟用 IP 收集和儲存， `DisableIpMasking` Application Insights 元件的屬性必須設定為 `true` 。 這個屬性可以透過 Azure Resource Manager 範本或藉由呼叫 REST API 來設定。 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>入口網站 

如果您只需要修改單一 Application Insights 資源的行為，請使用 Azure 入口網站。 

1. 前往您的 Application Insights 資源 >**自動化**  >  **匯出範本** 

2. 選取 **部署**

    ![以紅色反白顯示 [部署] 文字的按鈕](media/ip-collection/deploy.png)

3. 選取 [ **編輯範本**]。

    ![以紅色反白顯示「編輯」文字的按鈕](media/ip-collection/edit-template.png)

4. 對您資源的 json 進行下列變更，然後選取 [ **儲存**]：

    ![螢幕擷取畫面：在 "IbizaAIExtension" 後面加上逗號，並在下方新增具有 "DisableIpMasking" 的新行： true](media/ip-collection/save.png)

    > [!WARNING]
    > 如果您遇到錯誤指出： **_資源群組位於範本中的一或多個資源不支援的位置。請選擇不同的資源群組。_** 從下拉式清單中暫時選取不同的資源群組，然後重新選取原始資源群組以解決錯誤。

5. 選取 [**我同意**  >  **購買**]。 

    ![已核取 [我同意上方所述的條款及條件] 的核取方塊，並以紅色醒目提示 "Purchase" 一字的按鈕，以紅色反白顯示。](media/ip-collection/purchase.png)

    在此情況下，實際上不會購買任何新的。 我們只會更新現有 Application Insights 資源的設定。

6. 部署完成後，將會記錄新的遙測資料。

    如果您再次選取和編輯範本，您將只會看到預設範本，而不會顯示新加入的屬性。 如果您看不到 IP 位址資料，而且想要確認 `"DisableIpMasking": true` 已設定，請執行下列 PowerShell： 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    結果會傳回屬性的清單。 其中一個屬性應讀取 `DisableIpMasking: true` 。 如果您在使用 Azure Resource Manager 部署新的屬性之前執行 PowerShell，屬性將不存在。

### <a name="rest-api"></a>Rest API

進行相同修改的 [REST API](/rest/api/azure/) 裝載如下所示：

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>遙測初始設定式

如果您需要比更具彈性的替代方案 `DisableIpMasking` ，您可以使用 [遙測初始化運算式](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) ，將所有或部分 IP 位址複製到自訂欄位。 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> 如果您無法存取 `ISupportProperties` ，請檢查並確定您正在執行 APPLICATION INSIGHTS SDK 的最新穩定版本。 `ISupportProperties` 適用于高基數值，但 `GlobalProperties` 更適合用於較低的基數值，例如區功能變數名稱稱、環境名稱等等。 

### <a name="enable-telemetry-initializer-for-aspnet"></a>啟用 ASP.NET 的遙測初始化運算式

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>啟用 ASP.NET Core 的遙測初始化運算式

您可以使用與 ASP.NET 相同的 ASP.NET Core 方式來建立遙測初始化運算式，但若要啟用初始化運算式，請使用下列參考範例：

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[用戶端 JavaScript](#tab/javascript)

### <a name="client-side-javascript"></a>用戶端 JavaScript

與伺服器端 Sdk 不同的是，用戶端 JavaScript SDK 不會計算 IP 位址。 根據預設，用戶端遙測的 IP 位址計算會發生在 Azure 的內嵌端點上。 

如果您想要直接在用戶端上計算 IP 位址，您需要新增自己的自訂邏輯，並使用結果來設定 `ai.location.ip` 標記。 當 `ai.location.ip` 設定時，內嵌端點不會執行 IP 位址計算，而提供的 ip 位址會用於地理位置查閱。 在此案例中，預設仍會將 IP 位址清空。 

若要保留從自訂邏輯計算的整個 IP 位址，您可以使用遙測初始化運算式，將您所提供的 IP 位址資料複製 `ai.location.ip` 到個別的自訂欄位。 但同樣地，與伺服器端 Sdk 不同，不需要依賴協力廠商程式庫或您自己的自訂集合邏輯，用戶端 SDK 也不會為您計算位址。    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

如果用戶端資料在轉送至內嵌端點之前會先通過 proxy，則 IP 位址計算可能會顯示 proxy 的 IP 位址，而不是用戶端的 IP 位址。 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>查看遙測初始化運算式的結果

如果您將新流量傳送至您的網站，並等候幾分鐘的時間。 然後，您可以執行查詢來確認收集正在運作：

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

新收集的 IP 位址會出現在資料 `customDimensions_client-ip` 行中。 預設資料 `client-ip` 行仍然會有全部四個八位。 

如果是從 localhost 進行測試，而的值為 `customDimensions_client-ip` `::1` ，則這個值是預期的行為。 `::1` 代表 IPv6 中的回送位址。 它相當於 `127.0.01` IPv4 中的。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 Application Insights 中的 [個人資料收集](../platform/personal-data-mgmt.md) 。

* 深入瞭解 Application Insights 中的 [IP 位址集合](https://apmtips.com/posts/2016-07-05-client-ip-address/) 如何運作。  (本文是由我們的其中一位工程師所撰寫的舊版外部 blog 文章。 它會比目前的預設行為，也就是將 IP 位址記錄為 `0.0.0.0` ，但更深入瞭解內建的機制 `ClientIpHeaderTelemetryInitializer` 。 ) 
