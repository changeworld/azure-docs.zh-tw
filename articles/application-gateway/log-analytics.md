---
title: 使用 Azure 日誌分析檢查 WAF 日誌
titleSuffix: Azure Application Gateway
description: 本文介紹如何使用 Azure 日誌分析檢查應用程式閘道 Web 應用程式防火牆日誌
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9fe4462a71852e5f66268f798f6f0418f2dd39c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048111"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>使用 Log Analytics 檢查應用程式閘道 Web 應用程式防火牆記錄

應用程式閘道 WAF 運行後，您可以啟用日誌來檢查每個請求發生的情況。 防火牆日誌可以深入瞭解 WAF 正在評估、匹配和阻止的內容。 使用日誌分析，您可以檢查防火牆日誌中的資料，以提供更多見解。 有關創建日誌分析工作區的詳細資訊，請參閱[在 Azure 門戶中創建日誌分析工作區](../azure-monitor/learn/quick-create-workspace.md)。 有關日誌查詢的詳細資訊，請參閱 Azure[監視器 中的日誌查詢概述](../azure-monitor/log-query/log-query-overview.md)。

## <a name="import-waf-logs"></a>導入 WAF 日誌

要將防火牆日誌導入日誌分析，請參閱[應用程式閘道的後端運行狀況、診斷日誌和指標](application-gateway-diagnostics.md#diagnostic-logging)。 當您的日誌分析工作區中包含防火牆日誌時，您可以查看資料、編寫查詢、創建視覺化效果以及將它們添加到門戶儀表板。

## <a name="explore-data-with-examples"></a>使用示例流覽資料

要查看防火牆日誌中的原始資料，可以運行以下查詢：

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

這類似于以下查詢：

![日誌分析查詢](media/log-analytics/log-query.png)

您可以向下切入資料，並在此處繪製圖形或創建視覺化效果。 請參閱以下查詢作為起點：

### <a name="matchedblocked-requests-by-ip"></a>按 IP 匹配/阻止的請求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>URI 匹配/阻止的請求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>頂級匹配規則

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>匹配的規則組前五個

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>添加到儀表板

創建查詢後，可以將其添加到儀表板。  選擇日誌分析工作區右上角的 **"固定到儀表板**"。 將前四個查詢固定到示例儀表板，您可以一目了然地查看這些資料：

![儀表板](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>後續步驟

[應用程式閘道的後端運行狀況、診斷日誌和指標](application-gateway-diagnostics.md)
