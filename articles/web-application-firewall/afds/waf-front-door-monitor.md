---
title: Azure Web 應用程式防火牆監視和記錄
description: 瞭解具有 FrontDoor 監視和記錄功能的 Web 應用程式防火牆（WAF）
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808960"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web 應用程式防火牆監視和記錄

Azure Web 應用程式防火牆（WAF）監視和記錄是透過記錄和與 Azure 監視器和 Azure 監視器記錄檔的整合提供。

## <a name="azure-monitor"></a>Azure 監視器

具有 FrontDoor 記錄的 WAF 會與[Azure 監視器](../../azure-monitor/overview.md)整合。 Azure 監視器可讓您追蹤診斷資訊，包括 WAF 警示和記錄。 您可以在入口網站的 [**診斷**] 索引標籤底下，或直接透過 Azure 監視器服務，設定 WAF 監視。

從 Azure 入口網站，移至 [Front 門板] [資源類型]。 從左側的 [**監視** / **計量**] 索引標籤中，您可以新增**WebApplicationFirewallRequestCount**以追蹤符合 WAF 規則的要求數目。 您可以根據動作類型和規則名稱來建立自訂篩選器。

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics":::

## <a name="logs-and-diagnostics"></a>記錄和診斷

WAF 與 Front 門會針對它偵測到的每個威脅提供詳細的報告。 記錄會與 Azure 診斷記錄整合，而且警示會以 JSON 格式來記錄。 這些記錄可以與 [Azure 監視器記錄](../../azure-monitor/insights/azure-networking-analytics.md)整合。

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md)會記錄所有要求。 FrontdoorWebApplicationFirewallLog 會記錄符合具有下列架構之 WAF 規則的任何要求：

| 屬性  | 說明 |
| ------------- | ------------- |
|動作|對要求採取的動作|
| ClientIp | 發出要求之用戶端的 IP 位址。 如果要求中有 X 轉送的標頭，則會從標頭欄位中挑選用戶端 IP。 |
| ClientPort | 提出要求之用戶端的 IP 埠。 |
| 詳細資料|相符要求的其他詳細資料 |
|| matchVariableName：符合要求的 HTTP 參數名稱，例如標頭名稱|
|| matchVariableValue：觸發相符的值|
| 主機 | 相符要求的主機標頭 |
| 原則 | 要求符合的 WAF 原則名稱。 |
| PolicyMode | WAF 原則的作業模式。 可能的值為「防護」和「偵測」 |
| RequestUri | 相符要求的完整 URI。 |
| RuleName | 要求符合的 WAF 規則名稱。 |
| SocketIp | WAF 看到的來源 IP 位址。 此 IP 位址是以 TCP 會話為基礎，與任何要求標頭無關。|
| TrackingReference | 這項唯一的參考字串可識別 Front Door 所提供的要求，也會以 X-Azure Ref 標頭的形式傳送給用戶端， 這是在特定要求的存取記錄中搜尋詳細資料時的必要項目。 |

下列查詢範例會傳回已封鎖要求的 WAF 記錄：

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

以下是 WAF 記錄中已記錄要求的範例：

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

下列範例查詢會傳回 AccessLogs 專案：

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

以下是存取記錄檔中所記錄的要求範例：

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Front](../../frontdoor/front-door-overview.md)。
