---
title: 疑難排解-Azure Web 應用程式防火牆
description: 本文提供 Azure 應用程式閘道的 Web 應用程式防火牆 (WAF) 的疑難排解資訊
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 483d261a8cc107d01cfb7a405eac43667d7efcc6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131831"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>針對 Azure 應用程式閘道的 Web 應用程式防火牆 (WAF) 進行疑難排解

如果要求應該通過您的 Web 應用程式防火牆， (WAF) 遭到封鎖，您可以執行幾項作業。

首先，請確定您已閱讀 [WAF 總覽](ag-overview.md) 和 [WAF](application-gateway-waf-configuration.md) 設定檔。 此外，請確定您已啟用 [WAF 監視](../../application-gateway/application-gateway-diagnostics.md) 這些文章說明 WAF 功能、WAF 規則如何設定工作，以及如何存取 WAF 記錄。

OWASP 規則集設計成非常嚴格的現成可用，並使用 WAF 進行調整以符合應用程式或組織的特定需求。 在許多情況下，這完全是正常的，實際上是預期的情況是建立排除專案、自訂規則，甚至停用可能造成問題或誤報的規則。 每個網站和每個 URI 的原則可讓這些變更只影響特定網站/Uri，因此任何變更都不會影響可能不會遇到相同問題的其他網站。 

## <a name="understanding-waf-logs"></a>瞭解 WAF 記錄

WAF 記錄的目的是要顯示 WAF 符合或封鎖的每個要求。 它是所有符合或封鎖的已評估要求的總帳。 如果您注意到 WAF 封鎖了不應該 (誤報) 的要求，您可以做幾件事。 首先，縮小並尋找特定的要求。 查看記錄以尋找要求的特定 URI、時間戳記或交易識別碼。 當您找到相關聯的記錄專案時，就可以開始處理誤報。

例如，假設您有一個合法的流量，內含您想要透過 WAF 傳遞的字串 *1 = 1* 。 如果您嘗試要求，WAF 會封鎖在任何參數或欄位中包含 *1 = 1* 字串的流量。 這是通常與 SQL 插入式攻擊相關聯的字串。 您可以查看記錄，並查看要求的時間戳記和封鎖/符合的規則。

在下列範例中，您可以看到在相同要求 (使用 TransactionId 欄位) 時，會觸發四個規則。 第一個會指出它相符，因為使用者針對要求使用了數值/IP URL，這會將異常分數增加三，因為這是警告。 下一個相符的規則是942130，也就是您要尋找的規則。 您可以在欄位中看到 *1 = 1* `details.data` 。 這會進一步增加異常分數的三個，因為它也是警告。 一般而言，具有 **相符** 動作的每個規則都會增加異常分數，此時異常分數會是六。 如需詳細資訊，請參閱 [異常評分模式](ag-overview.md#anomaly-scoring-mode)。

最後兩個記錄專案會顯示要求遭到封鎖，因為異常分數夠高。 這些專案與其他兩個專案具有不同的動作。 它們會顯示它們實際上已 *封鎖* 要求。 這些規則是強制性的，且無法停用。 您不應該將它們視為規則，但是更能作為 WAF 內部的核心基礎結構。

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

## <a name="fixing-false-positives"></a>修正誤報

有了這項資訊，而規則942130的知識是符合 *1 = 1* 字串的知識，您可以執行一些動作來防止此問題封鎖您的流量：

- 使用排除清單

   如需排除清單的詳細資訊，請參閱 [WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) 設定。
- 停用規則。

### <a name="using-an-exclusion-list"></a>使用排除清單

若要明智地決定如何處理誤報，請務必讓自己熟悉應用程式所使用的技術。 例如，假設您的技術堆疊中沒有 SQL server，而且您收到與這些規則相關的誤報。 停用這些規則並不一定會降低您的安全性。

使用排除清單的其中一個優點是，只會停用要求的特定部分。 不過，這表示特定的排除適用于通過 WAF 的所有流量，因為它是全域設定。 例如，如果 *1 = 1* 是特定應用程式主體中的有效要求，而不是其他應用程式的有效要求，這可能會導致問題。 另一個優點是，如果符合特定條件，您可以選擇要排除的主體、標頭和 cookie，而不是排除整個要求。

有時候，某些情況下特定參數會以不直覺的方式傳遞至 WAF。 例如，在使用 Azure Active Directory 進行驗證時，會傳遞一個權杖。 此權杖（ *__RequestVerificationToken*）通常會傳入作為要求 Cookie。 不過，在某些情況下會停用 cookie，此權杖也會以要求屬性或 "arg" 的形式傳遞。 如果發生這種情況，您必須確定 *__RequestVerificationToken* 也會新增至排除清單，做為 **要求屬性的名稱** 。

![排除](../media/web-application-firewall-troubleshoot/exclusion-list.png)

在此範例中，您想要排除等於*text1*的**要求屬性名稱**。 這是很明顯的，因為您可以在防火牆記錄中看到屬性名稱： **資料：相符的資料： 1 = 1 在 ARGS： text1： 1 = 1 中找到**。 屬性為 **text1**。 您也可以使用其他幾種方式來尋找這個屬性名稱，請參閱 [尋找要求屬性名稱](#finding-request-attribute-names)。

![WAF 排除清單](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>停用規則

解決誤報的另一種方法，是停用 WAF 視為惡意的輸入所符合的規則。 由於您已剖析 WAF 記錄，並將規則縮小至942130，因此您可以在 Azure 入口網站中停用它。 請參閱 [透過 Azure 入口網站自訂 web 應用程式防火牆規則](application-gateway-customize-waf-rules-portal.md)。

停用規則的其中一個優點是，如果您知道包含特定條件的所有流量（通常會被封鎖）是有效的流量，您可以針對整個 WAF 停用該規則。 但是，如果它只是特定使用案例中的有效流量，您可以針對整個 WAF 停用該規則，藉此開啟弱點，因為這是全域設定。

如果您想要使用 Azure PowerShell，請參閱 [透過 PowerShell 自訂 web 應用程式防火牆規則](application-gateway-customize-waf-rules-powershell.md)。 如果您想要使用 Azure CLI，請參閱 [透過 Azure CLI 自訂 web 應用程式防火牆規則](application-gateway-customize-waf-rules-cli.md)。

![WAF 規則](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>尋找要求屬性名稱

在 [Fiddler](https://www.telerik.com/fiddler)的協助下，您可以檢查個別的要求，並判斷要呼叫網頁的特定欄位。 這有助於使用排除清單將某些欄位排除在檢查之外。

在此範例中，您可以看到輸入 *1 = 1* 字串的欄位稱為 **text1**。

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="進度 Telerik Fiddler Web 偵錯工具的螢幕擷取畫面。在 [原始] 索引標籤中，您可以在名稱 text1 之後看到 1 = 1。" border="false":::

這是您可以排除的欄位。 若要深入瞭解排除清單，請參閱 [Web 應用程式防火牆要求大小限制與排除清單](application-gateway-waf-configuration.md#waf-exclusion-lists)。 在此情況下，您可以藉由設定下列排除來排除評估：

![WAF 排除](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

您也可以檢查防火牆記錄來取得資訊，以查看您需要新增到排除清單的內容。 若要啟用記錄功能，請參閱 [應用程式閘道的後端健康情況、資源記錄和計量](../../application-gateway/application-gateway-diagnostics.md)。

檢查防火牆記錄檔，並針對您想要檢查的要求，查看檔案上的 PT1H.js。

在此範例中，您可以看到有四個規則具有相同的 TransactionID，而且全部都發生在相同的時間：

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

當您瞭解 CRS 規則的運作方式，以及 CRS 規則集3.0 適用于異常計分系統 (請參閱 [Azure 應用程式閘道的 Web 應用程式防火牆](ag-overview.md)) 您知道具有 **action：封鎖** 屬性的底部兩個規則會根據異常分數的總計進行封鎖。 要專注的規則是前兩個。

第一個專案會被記錄，因為使用者使用數值 IP 位址來流覽至應用程式閘道，在此情況下可以忽略。

第二個 (規則 942130) 是一種有趣的。 您可以在詳細資料中看到它符合模式 (1 = 1) ，而且欄位的名稱為 **text1**。 遵循相同先前的步驟，以排除**等於** **1 = 1**的**要求屬性名稱**。

## <a name="finding-request-header-names"></a>尋找要求標頭名稱

Fiddler 是一次可用的工具，可尋找要求標頭名稱。 在以下螢幕擷取畫面中，您可以看到此 GET 要求的標頭，其中包含 *內容類型*、 *使用者代理程式*等等。

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="進度 Telerik Fiddler Web 偵錯工具的螢幕擷取畫面。[原始] 索引標籤會列出要求標頭詳細資料，例如連接、內容類型和使用者代理程式。" border="false":::

另一個查看要求和回應標頭的方式，就是查看 Chrome 的開發人員工具。 您可以按 F12 鍵或按一下滑鼠右鍵 >**檢查**  ->  **開發人員工具**]，然後選取 [**網路**] 索引標籤。載入網頁，然後按一下您要檢查的要求。

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>尋找要求 cookie 名稱

如果要求包含 cookie，則可以選取 [ **cookie** ] 索引標籤以在 Fiddler 中加以查看。

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>限制全域參數以消除誤報

- 停用要求主體檢查

   藉由將 **檢查要求主體** 設定為 OFF，WAF 就不會評估所有流量的要求主體。 如果您知道要求本文對您的應用程式而言並不是惡意的，這可能會很有用。

   停用此選項，就不會檢查要求主體。 除非使用排除清單功能排除個別的標頭和 cookie，否則這些標頭和 cookie 都會保持檢查。

- 檔案大小限制

   藉由限制 WAF 的檔案大小，您就會限制您的 web 伺服器發生攻擊的可能性。 藉由允許上傳大型檔案，您後端的風險會增加。 將檔案大小限制為應用程式的一般使用案例，只是防止攻擊的另一種方式。

   > [!NOTE]
   > 如果您知道您的應用程式永遠不需要任何檔案上傳至指定的大小，您可以藉由設定限制來限制。

## <a name="firewall-metrics-waf_v1-only"></a>防火牆計量 (只 WAF_v1) 

針對 v1 Web 應用程式防火牆，下列計量現在可在入口網站中取得： 

1. Web 應用程式防火牆封鎖的要求計數已封鎖的要求數目
2. Web 應用程式防火牆封鎖規則會計算所有符合的規則 **，並** 封鎖要求
3. Web 應用程式防火牆規則分佈總計規則-評估期間符合的所有規則
     
若要啟用計量，請在入口網站中選取 [ **計量** ] 索引標籤，然後選取三個計量中的其中一個。

## <a name="next-steps"></a>後續步驟

瞭解 [如何在應用程式閘道上設定 web 應用程式防火牆](tutorial-restrict-web-traffic-powershell.md)。
