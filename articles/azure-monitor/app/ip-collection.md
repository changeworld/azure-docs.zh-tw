---
title: Azure 應用程式 Insights IP 位址集合 |Microsoft Docs
description: 瞭解如何使用 Azure 應用程式 Insights 處理 IP 位址和地理位置
ms.topic: conceptual
ms.date: 09/11/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 28a7fa50a06dc8b80c7d8dd284cd88ebe4645da6
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371646"
---
# <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 位址處理

本文說明如何在 Application Insights 中進行地理位置查閱和 IP 位址處理，以及如何修改預設行為。

## <a name="default-behavior"></a>預設行為

預設會暫時收集 IP 位址，但不會儲存在 Application Insights 中。 基本程序如下：

IP 位址會傳送至 Application Insights 作為遙測資料的一部分。 到達 Azure 中的內嵌端點時，會使用 IP 位址，[從 MaxMind 使用 GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)執行地理位置查閱。 此查閱的結果是用來填入下欄欄位： `client_City` `client_StateOrProvince` 、、 `client_CountryOrRegion` 。 此時，IP 位址會被捨棄，並 `0.0.0.0` 寫入 `client_IP` 欄位中。

* 瀏覽器遙測：我們會暫時收集寄件者的 IP 位址。 IP 位址是由內嵌端點所計算。
* 伺服器遙測： Application Insights 模組會暫時收集用戶端 IP 位址。 如果已設定 `X-Forwarded-For`，則不會收集該位址。

此行為是設計用來協助避免不必要的個人資料集合。 如果可能，建議您避免收集個人資料。 

## <a name="overriding-default-behavior"></a>覆寫預設行為

雖然預設行為是將個人資料的收集降到最低，但我們仍提供收集和儲存 IP 位址資料的彈性。 在選擇儲存任何個人資料（例如 IP 位址）之前，我們強烈建議您確認這不會違反任何可能受限於的合規性需求或當地法規。 若要深入瞭解 Application Insights 中的個人資料處理，請參閱[個人資料的指引](../platform/personal-data-mgmt.md)。

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

如果您只需要修改單一 Application Insights 資源的行為，完成此動作最簡單的方式是透過 Azure 入口網站。  

1. 前往您的 Application Insights 資源 >**設定**  >  **匯出範本** 

    ![匯出範本](media/ip-collection/export-template.png)

2. 選取**部署**

    ![以紅色反白顯示的 [部署] 按鈕](media/ip-collection/deploy.png)

3. 選取 [**編輯範本**]。 （如果您的範本具有不會出現在此範例範本中的其他屬性或資源，請務必小心，以確保所有資源都會接受範本部署做為增量變更/更新）。

    ![編輯範本](media/ip-collection/edit-template.png)

4. 對您資源的 json 進行下列變更，然後按一下 [**儲存**]：

    ![螢幕擷取畫面會在 "IbizaAIExtension" 後面加上一個逗號，並在下方新增包含 "DisableIpMasking" 的新行： true](media/ip-collection/save.png)

    > [!WARNING]
    > 如果您遇到錯誤，指出： **_資源群組所在的位置不受範本中的一或多個資源支援。請選擇不同的資源群組。_** 暫時從下拉式清單中選取不同的資源群組，然後重新選取原始的資源群組以解決錯誤。

5. 選取 [**我同意**  >  **購買**]。 

    ![編輯範本](media/ip-collection/purchase.png)

    在此情況下，我們只會更新現有 Application Insights 資源的設定，而不會購買任何新的。

6. 部署完成後，就會記錄新的遙測資料。

    如果您再次選取並編輯範本，您只會看到預設範本，而且看不到新加入的屬性及其相關聯的值。 如果您看不到 IP 位址資料，而且想要確認 `"DisableIpMasking": true` 已設定。 執行下列 PowerShell：（將取代 `Fabrikam-dev` 為適當的資源和資源組名。）
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    結果會傳回屬性清單。 其中一個屬性應為 [讀取] `DisableIpMasking: true` 。 如果您在使用 Azure Resource Manager 部署新的屬性之前執行 PowerShell，此屬性將不存在。

### <a name="rest-api"></a>Rest API

要進行相同修改的[REST API](/rest/api/azure/)承載如下所示：

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

如果您需要比 `DisableIpMasking` 記錄所有或部分 ip 位址更有彈性的替代方案，您可以使用[遙測初始化運算式](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)，將所有或部分 ip 複製到自訂欄位。 

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
> 如果您無法存取，請 `ISupportProperties` 檢查並確定您執行的是最新穩定版本的 APPLICATION INSIGHTS SDK。 `ISupportProperties`適用于高基數值，而較 `GlobalProperties` 適用于較低的基數值，例如區功能變數名稱稱、環境名稱等等。 

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

您可以使用與 ASP.NET ASP.NET Core 相同的方式建立遙測初始化運算式，但若要啟用初始化運算式，請使用下列範例來進行參考：

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

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

### <a name="client-side-javascript"></a>用戶端 JavaScript

與伺服器端 Sdk 不同的是，用戶端 JAVAscript SDK 不會計算 IP 位址。 根據預設，用戶端遙測的 IP 位址計算會在遙測抵達時，于 Azure 中的內嵌端點執行。 這表示如果您要將用戶端資料傳送至 proxy，然後轉送至內嵌端點，則 IP 位址計算可能會顯示 proxy 的 IP 位址，而不是用戶端。 如果沒有使用 proxy，這就不會是問題。

如果您想要直接在用戶端上計算 IP 位址，您需要新增自己的自訂邏輯來執行這項計算，並使用結果來設定 `ai.location.ip` 標記。 當 `ai.location.ip` 設定時，內嵌端點不會執行 IP 位址計算，並會接受所提供的 ip 位址並用於執行地區查閱。 在此案例中，預設仍會將 IP 位址清空。 

若要保留從自訂邏輯計算出來的整個 IP 位址，您可以使用遙測初始化運算式，將您在中提供的 IP 位址資料複製 `ai.location.ip` 到個別的自訂欄位。 但同樣地，與伺服器端 Sdk 不同的是，不依賴協力廠商程式庫或您自己的自訂用戶端 IP 集合邏輯，用戶端 SDK 將不會為您計算 IP。    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>查看遙測初始化運算式的結果

如果您接著針對您的網站觸發新流量，並等候大約2-5 分鐘以確保它有時間內嵌，您可以執行 Kusto 查詢，以查看 IP 位址集合是否正常運作：

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

新收集的 IP 位址應該會出現在資料 `customDimensions_client-ip` 行中。 預設資料 `client-ip` 行仍會有所有4個八位，或是只顯示前三個八位，視您在元件層級設定 IP 位址集合的方式而定。 如果您要在執行遙測初始化運算式之後于本機進行測試，而且您所看到的值 `customDimensions_client-ip` 是， `::1` 這是預期的行為。 `::1`代表 IPv6 中的回送位址。 它相當於 `127.0.01` IPv4 中的，而且是您從 localhost 進行測試時所看到的結果。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 Application Insights 中的[個人資料收集](../platform/personal-data-mgmt.md)。

* 深入瞭解 Application Insights 中的[IP 位址集合](https://apmtips.com/posts/2016-07-05-client-ip-address/)如何運作。 （這是我們的其中一位工程師撰寫的舊版外部 blog 文章。 它會比較舊目前的預設行為，其中會將 IP 位址記錄為 `0.0.0.0` ，但會在內建的機制上更深入瞭解 `ClientIpHeaderTelemetryInitializer` ）。
