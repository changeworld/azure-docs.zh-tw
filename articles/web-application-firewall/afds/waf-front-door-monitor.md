---
title: Azure Web 應用程式防火牆監視和日誌記錄
description: 通過前門監控和日誌記錄瞭解 Web 應用程式防火牆 （WAF）
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284138"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web 應用程式防火牆監視和日誌記錄 

Azure Web 應用程式防火牆 （WAF） 監視和日誌記錄是通過日誌記錄和與 Azure 監視器和 Azure 監視器日誌集成提供的。

## <a name="azure-monitor"></a>Azure 監視器

具有前門日誌的 WAF 與[Azure 監視器](../../azure-monitor/overview.md)集成。 Azure 監視器允許您跟蹤診斷資訊，包括 WAF 警報和日誌。 您可以在 **"診斷"** 選項卡下的門戶前門資源中或通過 Azure 監視器服務直接配置 WAF 監視。

從 Azure 門戶轉到前門資源類型。 從左側的 **"監視**/**指標"** 選項卡中，可以添加**Web 應用程式防火牆請求計數**以跟蹤與 WAF 規則匹配的請求數。 可以基於操作類型和規則名稱創建自訂篩選器。

![WAFMetric](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>日誌和診斷

帶前門的 WAF 提供有關其檢測到的每個威脅的詳細報告。 記錄會與 Azure 診斷記錄整合，而且警示會以 JSON 格式來記錄。 這些記錄可以與 [Azure 監視器記錄](../../azure-monitor/insights/azure-networking-analytics.md)整合。

![瓦夫迪亞格](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

前門訪問日誌記錄轉發到客戶後端的所有請求。 前門Web應用程式防火牆日誌記錄任何與WAF規則匹配的請求。

以下依例查詢獲取阻止請求上的 WAF 日誌：

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

下面是 WAF 日誌中記錄的請求的示例：

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

以下依例查詢獲取 AccessLogs 條目：

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

下面是訪問日誌中記錄的請求的示例：

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

- 瞭解有關[前門](../../frontdoor/front-door-overview.md)的更多。
