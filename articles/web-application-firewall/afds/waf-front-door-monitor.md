---
title: Azure Web 應用程式防火牆監視和記錄
description: 瞭解具有 FrontDoor 監視和記錄功能的 Web 應用程式防火牆（WAF）
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: b4f666415a96307b89022c6caf6af90581f294f3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115358"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web 應用程式防火牆監視和記錄 

Azure Web 應用程式防火牆（WAF）監視和記錄是透過記錄和與 Azure 監視器和 Azure 監視器記錄檔的整合提供。

## <a name="azure-monitor"></a>Azure 監視器

具有 FrontDoor 記錄的 WAF 會與[Azure 監視器](../../azure-monitor/overview.md)整合。 Azure 監視器可讓您追蹤診斷資訊，包括 WAF 警示和記錄。 您可以在入口網站的 [**診斷**] 索引標籤底下，或直接透過 Azure 監視器服務，設定 WAF 監視。

從 Azure 入口網站，移至 [Front 門板] [資源類型]。 從左側的 [**監視**/**計量**] 索引標籤中，您可以新增**WebApplicationFirewallRequestCount**以追蹤符合 WAF 規則的要求數目。 您可以根據動作類型和規則名稱來建立自訂篩選器。

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>記錄和診斷

WAF 與 Front 門會針對它偵測到的每個威脅提供詳細的報告。 記錄會與 Azure 診斷記錄整合，而且警示會以 JSON 格式來記錄。 這些記錄可以與 [Azure 監視器記錄](../../azure-monitor/insights/azure-networking-analytics.md)整合。

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog 會記錄轉送至客戶後端的所有要求。 FrontdoorWebApplicationFirewallLog 會記錄符合 WAF 規則的任何要求。

下列範例查詢會取得已封鎖要求的 WAF 記錄：

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

以下是 WAF 記錄中已記錄要求的範例：

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

下列範例查詢會取得 AccessLogs 專案：

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

以下是存取記錄檔中所記錄的要求範例：

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Front](../../frontdoor/front-door-overview.md)。
