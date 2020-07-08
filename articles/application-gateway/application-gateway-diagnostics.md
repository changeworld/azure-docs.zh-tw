---
title: 後端健康情況和診斷記錄
titleSuffix: Azure Application Gateway
description: 了解如何啟用和管理應用程式閘道的存取記錄和效能記錄
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/22/2019
ms.author: victorh
ms.openlocfilehash: 6829efa007e9e67866bdc0efbca4d095155c35e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82889692"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>應用程式閘道的後端健康情況和診斷記錄

您可以透過下列方式監視 Azure 應用程式閘道資源：

* [後端健康情況](#back-end-health)：應用程式閘道提供透過 Azure 入口網站和 PowerShell 監視後端集區中伺服器健康情況的功能。 您也可以透過效能診斷記錄找到後端集區的健康情況。

* [記錄](#diagnostic-logging)：記錄能夠儲存效能、存取和其他資料，或從資源取用記錄以便進行監視。

* [計量](application-gateway-metrics.md)：應用程式閘道有數個計量，可協助您確認系統是否如預期般執行。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>後端健康情況

應用程式閘道提供透過入口網站、PowerShell 及命令列界面 (CLI) 監視後端集區中個別成員健康情況的功能。 您也可以透過效能診斷記錄找到彙總的後端集區健康情況摘要。

後端健康情況報表會將應用程式閘道健康情況探查的輸出反映到後端執行個體。 當探查成功且後端可以接收流量，則視為狀況良好。 否則，視為狀況不良。

> [!IMPORTANT]
> 如果應用程式閘道子網上有網路安全性群組（NSG），請開啟 v1 Sku 的埠範圍65503-65534，並針對輸入流量在應用程式閘道子網上的 v2 Sku 65200-65535。 Azure 基礎結構通訊需要此連接埠範圍。 它們受到 Azure 憑證的保護 (鎖定)。 若沒有適當的憑證，外部實體 (包括這些閘道的客戶) 將無法對這些端點起始任何變更。


### <a name="view-back-end-health-through-the-portal"></a>透過入口網站檢視後端健康情況

入口網站中會自動提供後端的健康情況。 在現有的應用程式閘道上，選取 [**監視**  >  **後端健康**情況]。

後端集區中的每個成員均會列於此頁面中 (無論是 NIC、IP 或 FQDN)。 後端集區名稱、連接埠、後端 HTTP 設定名稱和健康情況均會顯示。 健全狀況狀態的有效值為 [狀況**良好**]、[狀況**不良**] 和 [**未知**]。

> [!NOTE]
> 如果您看到後端健康情況為 [未知]****，請確定 NSG 規則、使用者定義的路由 (UDR) 或虛擬網路中的自訂 DNS 並未封鎖對後端的存取。

![後端健康情況][10]

### <a name="view-back-end-health-through-powershell"></a>透過 PowerShell 檢視後端健康情況

下列 PowerShell 程式碼示範如何使用 `Get-AzApplicationGatewayBackendHealth` Cmdlet 檢視後端健康情況：

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>透過 Azure CLI 檢視後端健康情況

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>結果

以下是回應範例：

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>診斷記錄

您可以在 Azure 中使用不同類型的記錄來管理和針對應用程式閘道進行疑難排解。 您可以透過入口網站存取其中一些記錄。 可以從 Azure Blob 儲存體擷取所有記錄，並且在不同的工具中進行檢視 (例如 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)、Excel 和 Power BI)。 您可以從下列清單進一步了解不同類型的記錄：

* **活動記錄**：您可以使用 [Azure 活動記錄](../monitoring-and-diagnostics/insights-debugging-with-events.md) (之前稱為「作業記錄和稽核記錄」) 來檢視提交至您的 Azure 訂用帳戶的所有作業及其狀態。 預設會收集活動記錄，您可在 Azure 入口網站中檢視它們。
* **存取記錄**：您可以使用此記錄來查看應用程式閘道存取模式，並分析重要資訊。 這包括呼叫端的 IP、要求的 URL、回應延遲、傳回碼，以及傳入和傳出的位元組。每60秒會收集一次存取記錄檔。 此記錄檔包含每個應用程式閘道執行個體的一筆記錄。 應用程式閘道執行個體是由 instanceId 屬性識別。
* **效能記錄**：您可以使用此記錄來檢視應用程式閘道執行個體的執行情況。 此記錄會擷取每個執行個體的效能資訊，包括提供的要求總數、輸送量 (以位元組為單位)、提供的總要求數、失敗的要求計數、狀況良好和狀況不良的後端執行個體計數。 每隔 60 秒會收集一次效能記錄。 效能記錄僅適用于 v1 SKU。 針對 v2 SKU，請使用效能資料的[計量](application-gateway-metrics.md)。
* **防火牆記錄**：您可以使用此記錄，檢視透過應用程式閘道的偵測或防止模式 (依 Web 應用程式防火牆的設定) 所記錄的要求。 每60秒會收集一次防火牆記錄。 

> [!NOTE]
> 記錄僅適用於在 Azure Resource Manager 部署模型中部署的資源。 您無法將記錄使用於傳統部署模型中的資源。 若要深入了解這兩個模型，請參閱[了解 Resource Manager 部署和傳統部署](../azure-resource-manager/management/deployment-models.md)一文。

您有三個選項可用來排序您的記錄：

* **儲存體帳戶**：如果記錄會儲存一段較長的持續期間，並在需要時加以檢閱，則最好針對記錄使用儲存體帳戶。
* **事件中樞**：事件中樞是與其他安全性資訊和事件管理（SIEM）工具整合的絕佳選項，可在您的資源上取得警示。
* **Azure 監視器記錄**： Azure 監視器記錄最適合用來進行應用程式的一般即時監視，或查看趨勢。

### <a name="enable-logging-through-powershell"></a>透過 PowerShell 啟用記錄功能

每個 Resource Manager 資源都會自動啟用活動記錄功能。 您必須啟用存取和效能記錄功能，才能開始收集可透過這些記錄取得的資料。 使用下列步驟啟用記錄：

1. 請記下您的儲存體帳戶的資源識別碼 (記錄資料的儲存之處)。 此值的形式為：/subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> 。 您可以使用訂用帳戶中的所有儲存體帳戶。 您可以使用 Azure 入口網站來尋找此資訊。

    ![入口網站：儲存體帳戶的資源識別碼](./media/application-gateway-diagnostics/diagnostics1.png)

2. 請記下您的應用程式閘道的資源識別碼 (將為其啟用記錄功能)。 此值的形式為：/subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /providers/Microsoft.Network/applicationGateways/ \<application gateway name\> 。 您可以使用入口網站來尋找此資訊。

    ![入口網站：應用程式閘道的資源識別碼](./media/application-gateway-diagnostics/diagnostics2.png)

3. 使用下列 Powershell Cmdlet 啟用診斷記錄功能：

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>活動記錄不需要個別的儲存體帳戶。 將儲存體用於記錄存取和效能會產生服務費用。

### <a name="enable-logging-through-the-azure-portal"></a>透過 Azure 入口網站啟用記錄功能

1. 在 [Azure 入口網站中，尋找您的資源，然後選取 [**診斷設定**]。

   應用程式閘道有三個記錄：

   * 存取記錄檔
   * 效能記錄檔
   * 防火牆記錄檔

2. 若要開始收集資料，請選取 [**開啟診斷**]。

   ![開啟診斷][1]

3. [診斷設定]**** 頁面中提供診斷記錄的設定。 在此範例中，Log Analytics 會儲存記錄。 您也可以使用事件中樞和儲存體帳戶來儲存診斷記錄。

   ![啟動設定程序][2]

5. 輸入設定的名稱，確認設定，然後選取 [**儲存**]。

### <a name="activity-log"></a>活動記錄檔

根據預設，Azure 會產生活動記錄。 記錄會在 Azure 的事件記錄存放區中保留 90 天。 閱讀[查看事件和活動記錄](../monitoring-and-diagnostics/insights-debugging-with-events.md)一文，以深入瞭解這些記錄。

### <a name="access-log"></a>存取記錄檔

只有當您如上述步驟所述，在每個應用程式閘道上啟用存取記錄，才會產生存取記錄。 資料會儲存在您啟用記錄功能時指定的儲存體帳戶中。 應用程式閘道的每個存取都會以 JSON 格式記錄，如下列 v1 範例所示：

|值  |說明  |
|---------|---------|
|instanceId     | 處理要求的應用程式閘道執行個體。        |
|clientIP     | 要求的原始 IP。        |
|clientPort     | 要求的原始連接埠。       |
|httpMethod     | 要求使用的 HTTP 方法。       |
|requestUri     | 接收之要求的 URI。        |
|RequestQuery     | **Server-Routed**：傳送要求的後端集區執行個體。</br>**X-AzureApplicationGateway-LOG-ID**：要求所使用的相互關聯識別碼。 它可以用來針對後端伺服器上的流量問題進行疑難排解。 </br>**SERVER-STATUS**：應用程式閘道從後端收到的 HTTP 回應碼。       |
|UserAgent     | HTTP 要求標頭中的使用者代理程式。        |
|httpStatus     | 應用程式閘道傳回用戶端的 HTTP 狀態碼。       |
|httpVersion     | 要求的 HTTP 版本。        |
|receivedBytes     | 接收的封包大小，單位為位元組。        |
|sentBytes| 傳送的封包大小，單位為位元組。|
|timeTaken| 處理要求並傳送其回應所花費的時間長度，單位為毫秒。 算法是從應用程式閘道收到 HTTP 要求的回應第一個位元組的時間，到回應傳送作業完成時的時間間隔。 請務必注意，timeTaken 欄位通常包含要求和回應封包在網路上傳輸的時間。 |
|sslEnabled| 與後端集區的通訊是否使用 TLS/SSL。 有效值為 on 和 off。|
|主機| 要求已傳送到後端伺服器的主機名稱。 如果即將覆寫後端主機名稱，此名稱將會反映這一點。|
|originalHost| 應用程式閘道從用戶端接收要求的主機名稱。|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
對於應用程式閘道和 WAF v2，記錄檔會顯示更多詳細資訊：

|值  |說明  |
|---------|---------|
|instanceId     | 處理要求的應用程式閘道執行個體。        |
|clientIP     | 要求的原始 IP。        |
|clientPort     | 要求的原始連接埠。       |
|httpMethod     | 要求使用的 HTTP 方法。       |
|requestUri     | 接收之要求的 URI。        |
|UserAgent     | HTTP 要求標頭中的使用者代理程式。        |
|httpStatus     | 應用程式閘道傳回用戶端的 HTTP 狀態碼。       |
|httpVersion     | 要求的 HTTP 版本。        |
|receivedBytes     | 接收的封包大小，單位為位元組。        |
|sentBytes| 傳送的封包大小，單位為位元組。|
|timeTaken| 處理要求所花費的時間長度（以**秒為單位**），以及要傳送的回應。 算法是從應用程式閘道收到 HTTP 要求的回應第一個位元組的時間，到回應傳送作業完成時的時間間隔。 請務必注意，timeTaken 欄位通常包含要求和回應封包在網路上傳輸的時間。 |
|sslEnabled| 與後端集區的通訊是否使用 TLS。 有效值為 on 和 off。|
|sslCipher| 用於 TLS 通訊的加密套件（如果已啟用 TLS）。|
|sslProtocol| 使用的 SSL/TLS 通訊協定（如果已啟用 TLS）。|
|serverRouted| 應用程式閘道將要求路由至的後端伺服器。|
|serverStatus| 後端伺服器的 HTTP 狀態碼。|
|serverResponseLatency| 後端伺服器回應的延遲。|
|主機| 要求的主機標頭中所列的位址。|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>效能記錄檔

只有當您如上述步驟所述，在每個應用程式閘道上啟用效能記錄，才會產生效能記錄。 資料會儲存在您啟用記錄功能時指定的儲存體帳戶中。 產生效能記錄資料的時間間隔為 1 分鐘。 它僅適用于 v1 SKU。 針對 v2 SKU，請使用效能資料的[計量](application-gateway-metrics.md)。 會記錄下列資料：


|值  |說明  |
|---------|---------|
|instanceId     |  將產生此應用程式閘道執行個體的效能資料。 應用程式閘道若有多個執行個體，則是一個執行個體一行資料。        |
|healthyHostCount     | 後端集區中狀況良好主機的數目。        |
|unHealthyHostCount     | 後端集區中狀況不良主機的數目。        |
|requestCount     | 處理的要求數目。        |
|latency | 從執行個體到處理要求的後端之間的要求平均延遲，單位為毫秒。 |
|failedRequestCount| 失敗的要求數目。|
|throughput| 自最後一個記錄以來的平均輸送量，測量單位為每秒位元組。|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> 延遲的計算是從收到 HTTP 要求的第一個位元組時算起，到送出 HTTP 回應的最後一個位元組時結束。 是應用程式閘道處理時間，加上網路傳送到後端的時間，再加上後端處理要求所花時間的總和。

### <a name="firewall-log"></a>防火牆記錄檔

只有當您如上述步驟所述，在每個應用程式閘道上啟用防火牆記錄，才會產生防火牆記錄。 此記錄也需要在應用程式閘道上設定該 Web 應用程式防火牆。 資料會儲存在您啟用記錄功能時指定的儲存體帳戶中。 會記錄下列資料：


|值  |說明  |
|---------|---------|
|instanceId     | 將產生此應用程式閘道執行個體的防火牆資料。 應用程式閘道若有多個執行個體，則是一個執行個體一行資料。         |
|clientIp     |   要求的原始 IP。      |
|clientPort     |  要求的原始連接埠。       |
|requestUri     | 接收之要求的 URL。       |
|ruleSetType     | 規則集類型。 可用的值是 OWASP。        |
|ruleSetVersion     | 規則集版本。 可用值為 2.2.9 和 3.0。     |
|ruleId     | 觸發事件的規則識別碼。        |
|message     | 方便使用的觸發事件訊息。 詳細資料區段中會提供詳細資料。        |
|動作     |  對要求採取的動作。 可用的值會相符並遭到封鎖。      |
|site     | 將產生此網站的記錄。 目前只列出 Global，因為規則為全域。|
|詳細資料     | 觸發事件的詳細資料。        |
|details.message     | 規則的描述。        |
|details.data     | 在符合規則之要求中找到的特定資料。         |
|details.file     | 包含規則的組態檔。        |
|details.line     | 觸發事件的組態檔中的行號。       |
|hostname   | 應用程式閘道的主機名稱或 IP 位址。    |
|transactionId  | 指定交易的唯一識別碼，可協助將同一個要求中發生的多個規則違規組成群組。   |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
    "hostname": "40.90.218.100",
    "transactionId": "AYAcUqAcAcAcAcAcASAcAcAc"
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>檢視和分析活動記錄檔

您可以使用下列任何方法，檢視和分析活動記錄資料：

* **Azure 工具**：透過 Azure PowerShell、Azure CLI、Azure REST API 或 Azure 入口網站，從活動記錄擷取資訊。 [活動作業與 Resource Manager](../azure-resource-manager/management/view-activity-logs.md) 一文會詳述每個方法的逐步指示。
* **Power BI**：如果您還沒有[Power BI](https://powerbi.microsoft.com/pricing)帳戶，可以免費試用。 藉由使用[Power BI 範本應用程式](https://docs.microsoft.com/power-bi/service-template-apps-overview)，您可以分析您的資料。

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>檢視及分析存取、效能和防火牆記錄

[Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)可以從您的 Blob 儲存體帳戶收集計數器和事件記錄。 它也納入了視覺效果和強大的搜尋功能來分析您的記錄。

您也可以連接到儲存體帳戶並擷取存取和效能記錄的 JSON 記錄項目。 下載 JSON 檔案後，可以將它們轉換成 CSV，並在 Excel、PowerBI 或任何其他資料視覺化工具中檢視它們。

> [!TIP]
> 如果您熟悉 Visual Studio 以及在 C# 中變更常數和變數值的基本概念，您可以使用 GitHub 所提供的[記錄檔轉換器工具 (英文)](https://github.com/Azure-Samples/networking-dotnet-log-converter)。
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>透過 GoAccess 分析存取記錄

我們已發佈會安裝並執行常用 [GoAccess](https://goaccess.io/) 記錄分析器的 Resource Manager 範本，該分析器適用於應用程式閘道存取記錄。 GoAccess 提供實用的 HTTP 流量統計資料，例如非重複訪客、要求的檔案、主機、作業系統、瀏覽器、HTTP 狀態碼等等。 如需詳細資訊，請參閱 [GitHub 中 Resource Manager 範本資料夾中的讀我檔案](https://aka.ms/appgwgoaccessreadme)。

## <a name="next-steps"></a>後續步驟

* 利用 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)將計數器和事件記錄視覺化。
* [以 Power BI 的 blog 文章將您的 Azure 活動記錄視覺化](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/)。
* [在 Power BI 和其他工具中檢視和分析 Azure 活動記錄](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)部落格文章。

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
