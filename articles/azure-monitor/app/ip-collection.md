---
title: Azure 應用程式見解 IP 位址集合 |微軟文檔
description: 瞭解使用 Azure 應用程式見解如何處理 IP 位址和地理位置
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656512"
---
# <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 位址處理

本文介紹了地理位置查找和 IP 位址處理在應用程式見解中如何發生以及如何修改預設行為。

## <a name="default-behavior"></a>預設行為

預設情況下，IP 位址會臨時收集，但不會存儲在應用程式見解中。 基本程序如下：

IP 位址作為遙測資料的一部分發送到應用程式見解。 到達 Azure 中的引入終結點後，IP 位址用於使用[MaxMind 中的 GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)執行地理位置查找。 此查找的結果用於填充以下欄位`client_City`。 `client_CountryOrRegion` `client_StateOrProvince` 此時，IP 位址將被丟棄並`0.0.0.0`寫入`client_IP`該欄位。

* 瀏覽器遙測：我們臨時收集寄件者的 IP 位址。 IP 位址由引入終結點計算。
* 伺服器遙測：應用程式見解模組臨時收集用戶端 IP 位址。 如果已設定 `X-Forwarded-For`，則不會收集該位址。

此行為是設計，以説明避免不必要的個人資料收集。 只要有可能，我們建議避免收集個人資料。 

## <a name="overriding-default-behavior"></a>重寫預設行為

雖然預設行為是儘量減少個人資料的收集，但我們仍提供收集和存儲 IP 位址資料的靈活性。 在選擇存儲任何個人資料（如 IP 位址）之前，我們強烈建議驗證這是否違反您可能受遵守的任何合規性要求或當地法規。 要在應用見解中瞭解有關個人資料處理的更多詳細資訊，請參閱[個人資料指南](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)。

## <a name="storing-ip-address-data"></a>存儲 IP 位址資料

為了啟用 IP 收集和存儲，必須將`DisableIpMasking`應用程式見解元件的屬性設置為`true`。 此屬性可以通過 Azure 資源管理器範本或調用 REST API 進行設置。 

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

如果只需要修改單個應用程式見解資源的行為，則最簡單的方法是通過 Azure 門戶實現此目的。  

1. 轉到應用程式見解資源>**設置** > **匯出範本** 

    ![匯出範本](media/ip-collection/export-template.png)

2. 選擇 **"部署"**

    ![以紅色突出顯示的部署按鈕](media/ip-collection/deploy.png)

3. 選擇**編輯範本**。 （如果範本具有此示例範本中未顯示的其他屬性或資源，請謹慎行事，以確保所有資源都將接受範本部署作為增量更改/更新。

    ![編輯範本](media/ip-collection/edit-template.png)

4. 對資源的 json 進行以下更改，然後按一下"**保存**" ：

    ![螢幕截圖在"IbizaAI 擴展"後添加逗號，並在下方添加一條帶有"禁用 IpMs"的新行：true](media/ip-collection/save.png)

    > [!WARNING]
    > 如果遇到一個錯誤，指出：**_資源組位於範本中一個或多個資源不支援的位置。請選擇其他資源組。_** 從下拉清單中臨時選擇不同的資源組，然後重新選擇原始資源組以解決錯誤。

5. 選擇**我同意** > **購買**。 

    ![編輯範本](media/ip-collection/purchase.png)

    在這種情況下，沒有購買任何新內容，我們只是更新現有應用程式見解資源的配置。

6. 部署完成後，將記錄新的遙測資料。

    如果再次選擇和編輯範本，則只會看到預設範本，並且看不到新添加的屬性及其關聯的值。 如果您沒有看到 IP 位址資料，並且希望確認`"DisableIpMasking": true`已設置。 運行以下 PowerShell：（使用`Fabrikam-dev`相應的資源和資源組名稱替換。
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    因此，將返回屬性清單。 其中一個屬性應讀取`DisableIpMasking: true`。 如果在使用 Azure 資源管理器部署新屬性之前運行 PowerShell，則該屬性將不存在。

### <a name="rest-api"></a>Rest API

進行相同修改的[Rest API](https://docs.microsoft.com/rest/api/azure/)負載如下所示：

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

如果需要比`DisableIpMasking`記錄全部或部分 IP 位址更靈活的替代方法，則可以使用[遙測初始化程式](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer)將全部或部分 IP 複製到自訂欄位。 

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET核心

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
> 如果您無法訪問`ISupportProperties`，請檢查並確保您正在運行應用程式見解 SDK 的最新穩定版本。 `ISupportProperties`用於高基數值，而`GlobalProperties`更適合於低基數值，如區功能變數名稱稱、環境名稱等。 

### <a name="enable-telemetry-initializer-for-aspnet"></a>為ASP.NET啟用遙測初始化程式

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

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>為ASP.NET核心啟用遙測初始化程式

您可以像ASP.NET一樣為ASP.NET酷睿創建遙測初始化器，但要啟用初始化程式，請使用以下示例作為參考：

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

與伺服器端 SDK 不同，用戶端 JAVAscript SDK 不計算 IP 位址。 預設情況下，用戶端遙測的 IP 位址計算在遙測到達時在 Azure 中的引入終結點執行。 這意味著，如果要將用戶端資料發送到代理，然後轉發到引入終結點，則 IP 位址計算可能會顯示代理的 IP 位址，而不是用戶端。 如果未使用代理，這應該不是問題。

如果要直接在用戶端上計算 IP 位址，則需要添加自己的自訂邏輯來執行此計算，並使用結果設置`ai.location.ip`標記。 設置`ai.location.ip`後，引入終結點不會執行 IP 位址計算，並且提供 IP 位址將受尊重並用於執行地理查找。 在這種情況下，預設情況下仍將將 IP 位址歸零。 

要保留從自訂邏輯計算的整個 IP 位址，可以使用遙測初始化程式將您提供的`ai.location.ip`IP 位址資料複製到單獨的自訂欄位。 但是，與伺服器端 SDK 不同，用戶端 SDK 不依賴于協力廠商庫或您自己的自訂用戶端 IP 收集邏輯，用戶端 SDK 不會為您計算 IP。    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>查看遙測初始化器的結果

如果隨後針對網站觸發新流量並等待大約 2-5 分鐘以確保其有時間被引入，則可以運行 Kusto 查詢以查看 IP 位址收集是否正常工作：

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

新收集的 IP 位址應顯示在列`customDimensions_client-ip`中。 預設`client-ip`列仍將具有所有 4 個八位位元組，或者僅顯示前三個八位位元組，具體取決於您在元件級別配置 IP 位址集合的方式。 如果在實現遙測初始化程式後在本地進行測試，並且看到的值`customDimensions_client-ip`是`::1`預期行為。 `::1`表示 IPv6 中的迴路位址。 它等效`127.0.01`于 IPv4 中，是您在從本地主機進行測試時將看到的結果。

## <a name="next-steps"></a>後續步驟

* 在應用程式見解中瞭解有關[個人資料收集](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)的更多詳細資訊。

* 詳細瞭解應用程式見解中的[IP 位址收集](https://apmtips.com/blog/2016/07/05/client-ip-address/)的工作原理。 （這是由我們的工程師之一撰寫的較舊的外部博客文章。 它早于將 IP 位址記錄為`0.0.0.0`的當前預設行為，但它對內置`ClientIpHeaderTelemetryInitializer`機制的影響更深。
