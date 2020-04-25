---
title: 使用 Azure Log Analytics 來檢查應用程式閘道 Web 應用程式防火牆記錄
description: 本文說明如何使用 Azure Log Analytics 來檢查應用程式閘道 Web 應用程式防火牆記錄
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: 4d6b984fd451a835afaa51482f0158e9f315f8fc
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82130920"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>使用 Log Analytics 檢查應用程式閘道 Web 應用程式防火牆記錄

一旦您的應用程式閘道 WAF 可運作，您就可以啟用記錄來檢查每個要求所發生的情況。 防火牆記錄可讓您深入瞭解 WAF 的評估、比對和封鎖。 透過 Azure 監視器 Log Analytics，您可以檢查防火牆記錄內的資料，以提供更多深入資訊。 如需有關建立 Log Analytics 工作區的詳細資訊，請參閱[Azure 入口網站中的建立 Log analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。 如需記錄查詢的詳細資訊，請參閱[Azure 監視器中的記錄查詢總覽](../../azure-monitor/log-query/log-query-overview.md)。

## <a name="import-waf-logs"></a>匯入 WAF 記錄

若要將您的防火牆記錄匯入 Log Analytics，請參閱[應用程式閘道的後端健康情況、資源記錄和計量](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。 當您的 Log Analytics 工作區中有防火牆記錄時，您可以查看資料、撰寫查詢、建立視覺效果，並將其新增至您的入口網站儀表板。

## <a name="explore-data-with-examples"></a>使用範例流覽資料

若要查看防火牆記錄中的原始資料，您可以執行下列查詢：

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

這看起來會類似下列查詢：

![Log Analytics 查詢](../media/log-analytics/log-query.png)

您可以從這裡向下切入資料，並繪製圖形或建立視覺效果。 請參閱下列查詢做為起點：

### <a name="matchedblocked-requests-by-ip"></a>依 IP 的相符/已封鎖要求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>依 URI 的相符/已封鎖要求

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

建立查詢之後，您就可以將它新增至儀表板。  選取 log analytics 工作區右上方的 [**釘選到儀表板**]。 過去四個釘選到範例儀表板的查詢，這就是您可以一目了然的資料：

![儀表板](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>後續步驟

[應用程式閘道的後端健康情況、資源記錄和計量](../../application-gateway/application-gateway-diagnostics.md)