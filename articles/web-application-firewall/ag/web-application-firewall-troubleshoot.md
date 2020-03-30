---
title: 故障排除 - Azure Web 應用程式防火牆
description: 本文為 Azure 應用程式閘道的 Web 應用程式防火牆 （WAF） 提供故障排除資訊
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046200"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>為 Azure 應用程式閘道解決 Web 應用程式防火牆 （WAF） 的故障

如果阻止應通過 Web 應用程式防火牆 （WAF） 的請求，您可以執行一些操作。

首先，請確保您已閱讀 WAF[概述](ag-overview.md)和[WAF 配置](application-gateway-waf-configuration.md)文檔。 此外，請確保您已啟用 WAF[監視](../../application-gateway/application-gateway-diagnostics.md)這些文章可解釋 WAF 如何工作、WAF 規則集的工作原理以及如何訪問 WAF 日誌。

## <a name="understanding-waf-logs"></a>瞭解 WAF 日誌

WAF 日誌的目的是顯示由 WAF 匹配或阻止的每個請求。 它是匹配或阻止的所有已評估請求的分類帳。 如果您注意到 WAF 阻止了它不應阻止的請求（誤報），則可以執行一些操作。 首先，縮小範圍，並找到具體請求。 查看日誌以查找請求的特定 URI、時間戳記或事務 ID。 當您找到關聯的日誌條目時，可以開始對誤報執行操作。

例如，假設您有一個包含要通過 WAF 的字串*1=1*的合法流量。 如果嘗試該請求，WAF 會阻止包含任何參數或欄位中*1+1*字串的流量。 這是通常與 SQL 注入攻擊關聯的字串。 您可以查看日誌並查看請求的時間戳記以及阻止/匹配的規則。

在下面的示例中，您可以看到在同一請求期間觸發了四個規則（使用事務 Id 欄位）。 第一個說它匹配，因為使用者使用數位/IP URL 的請求，這將異常分數增加三個，因為它是一個警告。 匹配的下一個規則是 942130，這是您要查找的規則。 您可以在`details.data`欄位中看到*1=1。* 這進一步增加了異常分數三個再次，因為它也是一個警告。 通常，具有"**匹配**"操作的每個規則都會增加異常分數，此時異常分數將為 6。 有關詳細資訊，請參閱[異常評分模式](ag-overview.md#anomaly-scoring-mode)。

最後兩個日誌條目顯示請求被阻止，因為異常分數足夠高。 這些條目的操作與其他兩個不同。 它們表明他們實際上*阻止了*請求。 這些規則是強制性的，不能禁用。 它們不應被視為規則，而應該更多地被視為 WAF 內部的核心基礎結構。

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>修復誤報

有了這些資訊，並且知道規則 942130 是匹配*1=1*字串的，您可以執行一些操作來阻止它阻塞流量：

- 使用排除清單

   有關排除清單的詳細資訊，請參閱[WAF 配置](application-gateway-waf-configuration.md#waf-exclusion-lists)。
- 禁用規則。

### <a name="using-an-exclusion-list"></a>使用排除清單

要做出處理誤報的明智決策，請務必熟悉應用程式使用的技術。 例如，假設技術堆疊中沒有 SQL 伺服器，並且您收到與這些規則相關的誤報。 禁用這些規則並不一定會削弱您的安全性。

使用排除清單的一個好處是僅禁用請求的特定部分。 但是，這意味著特定排除適用于通過 WAF 的所有流量，因為它是全域設置。 例如，如果*1=1*是正文中某個應用的有效請求，但對其他應用不有效請求，則這可能導致問題。 另一個好處是，您可以選擇身體、標頭和 Cookie，以便滿足特定條件，而不是排除整個請求。

有時，特定參數以可能不直觀的方式傳遞到 WAF 中。 例如，使用 Azure 活動目錄進行身份驗證時，將傳遞權杖。 這個權杖 *，__RequestVerificationToken，* 通常被傳遞到作為請求Cookie。 但是，在某些情況下禁用 Cookie 時，此權杖也會作為請求屬性或"arg"傳遞。 如果發生這種情況，您需要確保 *__RequestVerificationToken*也作為**請求屬性名稱**添加到排除清單中。

![排除](../media/web-application-firewall-troubleshoot/exclusion-list.png)

在此示例中，要排除等於*text1*的**Request 屬性名稱**。 這是顯而易見的，因為您可以在防火牆日誌中看到屬性名稱：**資料：匹配資料：在 ARGS 中找到 1=1：text1：1=1**。 屬性為**text1**。 您還可以通過其他幾種方式找到此屬性名稱，請參閱[查找請求屬性名稱](#finding-request-attribute-names)。

![WAF 排除清單](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>禁用規則

繞過誤報的另一種方法是禁用 WAF 認為是惡意的輸入上匹配的規則。 由於已解析 WAF 日誌並將規則縮小到 942130，因此可以在 Azure 門戶中禁用它。 請參閱[通過 Azure 門戶自訂 Web 應用程式防火牆規則](application-gateway-customize-waf-rules-portal.md)。

禁用規則的一個好處是，如果您知道包含通常被阻止的特定條件的所有流量都是有效的流量，則可以為整個 WAF 禁用該規則。 但是，如果它只是特定用例中的有效流量，則可以通過禁用整個 WAF 的規則來打開漏洞，因為它是全域設置。

如果要使用 Azure PowerShell，請參閱[通過 PowerShell 自訂 Web 應用程式防火牆規則](application-gateway-customize-waf-rules-powershell.md)。 如果要使用 Azure CLI，請參閱[通過 Azure CLI 自訂 Web 應用程式防火牆規則](application-gateway-customize-waf-rules-cli.md)。

![WAF 規則](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>查找請求屬性名稱

在[Fiddler](https://www.telerik.com/fiddler)的説明下，您可以檢查單個請求並確定調用網頁的特定欄位。 這有助於使用排除清單從檢查中排除某些欄位。

在此示例中，您可以看到輸入*1=1*字串的欄位稱為**text1**。

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

這是一個可以排除的欄位。 要瞭解有關排除清單的更多內容，請參閱[Web 應用程式防火牆請求大小限制和排除清單](application-gateway-waf-configuration.md#waf-exclusion-lists)。 在這種情況下，可以通過配置以下排除項來排除評估：

![WAF 排除](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

您還可以檢查防火牆日誌以獲取資訊以查看需要添加到排除清單中的內容。 要啟用日誌記錄，請參閱[應用程式閘道的後端運行狀況、診斷日誌和指標](../../application-gateway/application-gateway-diagnostics.md)。

檢查防火牆日誌，並查看要檢查的請求發生的小時 PT1H.json 檔。

在此示例中，您可以看到有四個具有相同事務 ID 的規則，並且它們都同時發生：

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

瞭解 CRS 規則如何工作，並且 CRS 規則集 3.0 與異常評分系統（請參閱 Azure[應用程式閘道的 Web 應用程式防火牆](ag-overview.md)）一起工作，您知道具有操作的後兩個規則 **：阻止**屬性根據總異常分數被阻止。 需要關注的規則是前兩名。

記錄第一個條目是因為使用者使用數位 IP 位址導航到應用程式閘道，在這種情況下，可以忽略該閘道。

第二個（規則942130）是有趣的。 您可以在詳細資訊中看到它與模式 （1=1） 匹配，並且該欄位被命名為**text1**。 按照相同的前一步來排除**等於** **1=1****的請求屬性名稱**。

## <a name="finding-request-header-names"></a>查找請求標頭名稱

Fiddler 是再次查找請求標頭名稱的有用工具。 在下面的螢幕截圖中，您可以看到此 GET 請求的標題，其中包括*內容類型*、*使用者代理*等。

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

查看請求和回應標頭的另一種方法是查看 Chrome 的開發人員工具。 您可以按 F12 或按右鍵 ->**檢查** -> **開發人員工具**，然後選擇 **"網路**"選項卡。載入網頁，然後按一下要檢查的請求。

![鉻 F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>查找請求 Cookie 名稱

如果請求包含 Cookie，則可以選擇 **"Cookie"** 選項卡在 Fiddler 中查看它們。

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>限制全域參數以消除誤報

- 禁用請求正文檢查

   通過將 **"檢查"請求正文**設置為"關閉"，WAF 將不會評估所有流量的請求正文。 如果您知道請求正文對應用程式不是惡意的，這可能很有用。

   通過禁用此選項，將僅檢查請求正文。 標頭和 Cookie 將繼續進行檢查，除非使用排除清單功能排除單個標頭和 Cookie。

- 檔案大小限制

   通過限制 WAF 的檔案大小，您可以限制 Web 服務器遭受攻擊的可能性。 通過允許上傳大型檔，後端不堪重負的風險會增加。 將檔案大小限制為應用程式的正常用例只是防止攻擊的另一種方法。

   > [!NOTE]
   > 如果您知道應用將永遠不會需要任何超出給定大小的檔上載，則可以通過設置限制來限制它。

## <a name="firewall-metrics-waf_v1-only"></a>防火牆指標（僅限WAF_v1）

對於 v1 Web 應用程式防火牆，門戶中現在提供以下指標： 

1. Web 應用程式防火牆阻止請求計數被阻止的請求數
2. Web 應用程式防火牆阻止規則計數所有匹配**和**請求被阻止的規則
3. Web 應用程式防火牆總規則分發評估期間匹配的所有規則
     
要啟用指標，請選擇門戶中的 **"指標"** 選項卡，然後選擇三個指標之一。

## <a name="next-steps"></a>後續步驟

請參閱[如何在應用程式閘道上配置 Web 應用程式防火牆](tutorial-restrict-web-traffic-powershell.md)。
