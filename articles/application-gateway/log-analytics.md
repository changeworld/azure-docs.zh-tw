---
title: 使用 Azure Log Analytics 檢查 WAF 記錄
titleSuffix: Azure Application Gateway
description: 本文說明如何使用 Azure Log Analytics 來檢查應用程式閘道 Web 應用程式防火牆 (WAF) 記錄。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 881dbd02a6bf069fbe68108cc6eab0cb6b909a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362645"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-waf-logs"></a>使用 Log Analytics 來檢查應用程式閘道 Web 應用程式防火牆 (WAF) 記錄

一旦您的應用程式閘道 WAF 可運作，您就可以啟用記錄來檢查每個要求發生什麼事。 防火牆記錄可讓您深入瞭解 WAF 的評估、比對和封鎖。 使用 Log Analytics，您可以檢查防火牆記錄內的資料，以提供更深入的見解。 如需有關建立 Log Analytics 工作區的詳細資訊，請參閱 [Azure 入口網站中的建立 Log analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。 如需記錄查詢的詳細資訊，請參閱 [Azure 監視器中的記錄查詢概觀](../azure-monitor/log-query/log-query-overview.md)。

## <a name="import-waf-logs"></a>匯入 WAF 記錄

若要將您的防火牆記錄匯入至 Log Analytics，請參閱 [應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md#diagnostic-logging)。 當您的 Log Analytics 工作區中有防火牆記錄時，您可以查看資料、撰寫查詢、建立視覺效果，並將其新增至您的入口網站儀表板。

## <a name="explore-data-with-examples"></a>使用範例流覽資料

若要查看防火牆記錄檔中的原始資料，您可以執行下列查詢：

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

這看起來會類似下列查詢：

![Log Analytics 查詢](media/log-analytics/log-query.png)

您可以向下切入資料，並從這裡繪製圖形或建立視覺效果。 請參閱下列查詢作為起點：

### <a name="matchedblocked-requests-by-ip"></a>依 IP 的相符/封鎖要求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>依 URI 的符合/封鎖要求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>最符合的規則

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>前五個相符的規則群組

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>新增至您的儀表板

建立查詢之後，您可以將它新增至儀表板。  選取 log analytics 工作區右上角的 [ **釘選到儀表板** ]。 在先前的四個查詢釘選到範例儀表板之後，這就是您可以一目了然的資料：

![螢幕擷取畫面顯示 Azure 儀表板，您可以在其中新增查詢。](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>後續步驟

[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)
