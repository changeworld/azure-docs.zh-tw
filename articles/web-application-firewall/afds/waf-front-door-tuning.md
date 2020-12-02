---
title: '針對 Azure Front Door 調整 Web 應用程式防火牆 (WAF) '
description: 在本文中，您將瞭解如何調整 Front Door 的 WAF。
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: 4c710792dd7966fad76b33954fdf7c2253cf18f0
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488233"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>針對 Azure Front Door 調整 Web 應用程式防火牆 (WAF) 
 
Azure 管理的預設規則集是根據 [ (CRS) 的 OWASP 核心規則集 ](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) ，且設計為嚴格地退出。 通常預期需要調整 WAF 規則，以符合使用 WAF 的應用程式或組織的特定需求。 這通常是藉由定義規則排除專案、建立自訂規則，甚至是停用可能造成問題或誤報的規則來達成。 如果要求應該通過您的 Web 應用程式防火牆， (WAF) 遭到封鎖，您可以執行幾項作業。

首先，請確定您已閱讀 [FRONT DOOR WAF 總覽](afds-overview.md) 和 Front Door 檔的 [WAF 原則](waf-front-door-create-portal.md) 。 此外，請確定您已啟用 [WAF 監視和記錄](waf-front-door-monitor.md)。 這些文章說明 WAF 函式、WAF 規則如何設定工作，以及如何存取 WAF 記錄。
 
## <a name="understanding-waf-logs"></a>瞭解 WAF 記錄
 
WAF 記錄的目的是要顯示 WAF 符合或封鎖的每個要求。 它是所有符合或封鎖的已評估要求的集合。 如果您注意到 WAF 封鎖了不應該 (誤報) 的要求，您可以做幾件事。 首先，縮小並尋找特定的要求。 如有需要，您可以 [設定自訂回應訊息](./waf-front-door-configure-custom-response-code.md) 來包含 `trackingReference` 欄位，以便輕鬆地識別事件並對該特定值執行記錄查詢。 查看記錄以尋找要求的特定 URI、時間戳記或用戶端 IP。 當您找到相關的記錄檔專案時，您可以開始對誤報進行動作。 
 
例如，假設您有一個合法的流量，內含 `1=1` 您想要透過 WAF 傳遞的字串。 以下是要求的樣子：

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

如果您嘗試要求，WAF 會封鎖在任何參數或欄位中包含 *1 = 1* 字串的流量。 這是通常與 SQL 插入式攻擊相關聯的字串。 您可以查看記錄，並查看要求的時間戳記和封鎖/符合的規則。
 
在下列範例中，我們會探索 `FrontdoorWebApplicationFirewallLog` 由於規則相符而產生的記錄。
 
在 [requestUri] 欄位中，您可以看到明確提出的要求 `/api/Feedbacks/` 。 接下來，我們會 `942110` 在 [ruleName] 欄位中找到規則識別碼。 知道規則識別碼之後，您可以移至 [OWASP ModSecurity Core 規則集官方存放庫](https://github.com/coreruleset/coreruleset) ，並依該 [規則識別碼](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) 進行搜尋，以檢查其程式碼，並瞭解此規則符合的確切內容。 
 
然後，藉由檢查 `action` 欄位，我們會看到此規則設定為在比對時封鎖要求，而且我們會確認要求確實遭到 WAF 封鎖，因為 `policyMode` 已設定為 `prevention` 。 
 
現在，讓我們來檢查欄位中的資訊 `details` 。 這是您可以在其中查看 `matchVariableName` 和資訊的位置 `matchVariableValue` 。 我們瞭解此規則是因為有人在 web 應用程式的欄位中輸入 *1 = 1* 而觸發 `comment` 。
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
檢查存取記錄以擴充您對特定 WAF 事件的知識也有價值。 以下我們將探討以 `FrontdoorAccessLog` 回應上述事件所產生的記錄。
 
您可以看到這些是以相同的值為基礎的相關記錄 `trackingReference` 。 在提供一般見解的各種欄位（例如 `userAgent` 和）之間， `clientIP` 我們會注意 `httpStatusCode` 和 `httpStatusDetails` 欄位。 在這裡，我們可以確認用戶端已收到 HTTP 403 回應，這會確認此要求已被拒絕和封鎖。 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>解決誤報
 
若要明智地決定如何處理誤報，請務必讓自己熟悉應用程式所使用的技術。 例如，假設您的技術堆疊中沒有 SQL server，而且您收到與這些規則相關的誤報。 停用這些規則並不一定會降低您的安全性。 

有了這項資訊，以及規則942110與範例中的字串相符的知識，就 `1=1` 可以執行一些動作來防止此合法的要求遭到封鎖：
 
* 使用排除清單
  * 如需排除清單的詳細資訊，請參閱 [Front Door Service 排除清單的 Web 應用程式防火牆 (WAF) ](waf-front-door-exclusion.md) 。 
* 變更 WAF 動作
  * 如需有關要求符合規則條件時可以採取哪些動作的詳細資訊，請參閱 [WAF 動作](afds-overview.md#waf-actions) 。
* 使用自訂規則
  * 如需有關自訂規則的詳細資訊，請參閱 [具有 Azure Front Door 的 Web 應用程式防火牆自訂規則](waf-front-door-custom-rules.md) 。
* 停用規則 

> [!TIP]
> 當選取允許透過 WAF 進行合法要求的方法時，請儘量縮小此範圍。 例如，最好使用排除清單，而不是完全停用規則。

### <a name="using-exclusion-lists"></a>使用排除清單

使用排除清單的其中一個優點是，系統將不會再檢查您選取要排除的相符變數是否有該指定的要求。 也就是說，您可以選擇特定的要求標頭、要求 cookie、查詢字串引數，或在符合特定條件時排除的要求本文 post 引數，而不是排除整個要求的檢查。 仍會正常檢查要求的其他非指定變數。
 
請務必考慮排除專案是全域設定。 這表示設定的排除會套用至所有通過您 WAF 的流量，而不只是特定的 web 應用程式或 URI。 例如，如果 *1 = 1* 是特定 web 應用程式主體中的有效要求，但對於相同 WAF 原則下的其他應用程式而言，這可能是個問題。 如果對不同的應用程式使用不同的排除清單有意義，請考慮針對每個應用程式使用不同的 WAF 原則，並將其套用至每個應用程式的前端。
 
設定受控規則的排除清單時，您可以選擇排除規則集內的所有規則、規則群組內的所有規則，或個別規則。 您可以使用 [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-4.7.0&viewFallbackFrom=azps-3.5.0)、 [AZURE CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add)、 [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)或 Azure 入口網站來設定排除清單。

* 規則層級的排除專案
  * 在規則層級套用排除項，表示指定的排除專案將不會針對該個別規則進行分析，但仍會由規則集中的所有其他規則進行分析。 這是排除專案的最細微層級，而且可以用來根據您在針對事件進行疑難排解時，在 WAF 記錄中找到的資訊來微調受控規則集。
* 規則群組層級的排除專案
  * 在規則群組層級套用排除專案，表示將不會針對該特定一組規則類型來分析指定的排除專案。 例如，選取 [ *SQLI* ] 作為排除的規則群組，表示已定義的要求排除專案將不會由任何 SQLI 特定規則檢查，但仍會由其他群組中的規則（例如 *PHP*、 *RFI* 或 *XSS*）進行檢查。 當我們確定應用程式不容易遭受特定的攻擊類型時，這種類型的排除可能很有用。 例如，沒有任何 SQL database 的應用程式可能會排除所有的 *SQLI* 規則，而不會對其安全性層級造成危害。
* 規則集層級的排除專案 
  * 在規則集層級套用排除專案，表示將不會針對該規則集中可用的任何安全性規則來分析指定的排除專案。 這是完整的排除，因此應謹慎使用。

在此範例中，我們會以最細微的層級執行排除 (將排除套用至單一規則) ，而且我們想要排除包含的比對變數 **要求主體 post 參數名稱** `comment` 。 這是很明顯的，因為您可以在防火牆記錄檔中看到相符的變數詳細資料： `"matchVariableName": "PostParamValue:comment"` 。 屬性為 `comment` 。 您也可以使用其他幾種方式來尋找這個屬性名稱，請參閱 [尋找要求屬性名稱](#finding-request-attribute-names)。

![排除規則](../media/waf-front-door-tuning/exclusion-rules.png)

![特定規則的規則排除](../media/waf-front-door-tuning/exclusion-rule.png)

有時候，某些情況下特定參數會以不直覺的方式傳遞至 WAF。 例如，在使用 Azure Active Directory 進行驗證時，會傳遞一個權杖。 此權杖 `__RequestVerificationToken` 通常會傳入作為要求 cookie。 不過，在某些情況下會停用 cookie，此權杖也會傳入作為要求 post 引數。 基於這個理由，為了解決 Azure AD token 誤報，您必須確定 `__RequestVerificationToken` 已在和的排除清單中加入 `RequestCookieNames` `RequestBodyPostArgsNames` 。

功能變數名稱 (*選擇器* 的排除專案) 表示 WAF 將不會再評估該值。 不過，功能變數名稱本身會繼續進行評估，在罕見的情況下，它可能會比對 WAF 規則並觸發動作。

![規則集的規則排除](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>變更 WAF 動作

處理 WAF 規則行為的另一種方式是選擇要求符合規則條件時所要採取的動作。 可用的動作包括： [ [允許]、[封鎖]、[記錄] 和](afds-overview.md#waf-actions)[重新導向]。

在此範例中，我們在規則942110上將預設動作 *區塊* 變更為 *記錄* 動作。 這會導致 WAF 記錄要求，並繼續針對其餘較低優先順序的規則來評估相同的要求。

![WAF 動作](../media/waf-front-door-tuning/actions.png)

執行相同的要求之後，我們可以回頭參考記錄檔，我們會看到此要求與規則識別碼942110相符，而且 `action_s` 欄位現在會指出 *記錄* 而非 *區塊*。 接著展開記錄查詢以包含 `trackingReference_s` 資訊，並查看此要求的其他情況。

![顯示多個規則相符專案的記錄](../media/waf-front-door-tuning/actions-log.png)

有趣的是，在處理規則識別碼942110之後，我們會看到不同的 SQLI 規則相符。 與規則識別碼942310相符的相同要求，這次會觸發預設的動作 *區塊* 。

在 WAF 微調或疑難排解期間使用 *記錄* 檔動作的另一個優點是，您可以識別特定規則群組內的多個規則是否符合和封鎖指定的要求。 然後，您可以在適當的層級（亦即，在規則或規則群組層級）建立排除專案。 

### <a name="using-custom-rules"></a>使用自訂規則

一旦識別出造成 WAF 規則相符的原因之後，您就可以使用自訂規則來調整 WAF 回應事件的方式。 自訂規則會在受控規則之前處理，它們可以包含一個以上的條件，而且其動作可以是 [允許、拒絕、記錄或重新導向](afds-overview.md#waf-actions)。 當規則符合時，WAF 引擎會停止處理。 這表示不會再執行較低優先順序的其他自訂規則和受控規則。

在下列範例中，我們建立了具有兩個條件的自訂規則。 第一個條件是尋找 `comment` 要求主體中的值。 第二個條件是尋找 `/api/Feedbacks/` 要求 URI 中的值。

使用自訂規則可讓您在微調 WAF 規則和處理誤報時，最細微。 在此情況下，我們不只會根據 `comment` 要求主體值採取動作，該值可能會存在於多個網站或相同 WAF 原則下的應用程式。 藉由在特定的要求 URI 中加入另一個條件 `/api/Feedbacks/` ，我們可以確保此自訂規則真的適用于我們通過的明確使用案例。這可確保 WAF 引擎仍會檢查和防止相同的攻擊（如果針對不同的情況執行的話）。

![Log](../media/waf-front-door-tuning/custom-rule.png)

探索記錄檔時，您可以看到 `ruleName_s` 欄位包含提供給我們所建立自訂規則的名稱： `redirectcomment` 。 在 `action_s` 此欄位中，您可以看到此事件已取得重新 *導向* 動作。 在 `details_matches_s` 欄位中，我們可以看到兩個條件的詳細資料都相符。

### <a name="disabling-rules"></a>停用規則

解決誤報的另一種方法，是停用 WAF 視為惡意的輸入所符合的規則。 由於您已剖析 WAF 記錄，並將規則縮小至942110，因此您可以在 Azure 入口網站中停用它。 請參閱 [使用 Azure 入口網站自訂 Web 應用程式防火牆規則](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules)。
 
當您確定所有符合該特定條件的要求都是合法的要求，或當您確定規則只適用于您的環境 (例如，因為您有非 SQL 後端) ，而停用 SQL 插入式規則時，停用規則是一項優點。 
 
不過，停用規則是全域設定，適用于所有與 WAF 原則相關聯的前端主機。 當您選擇停用規則時，您可能會在沒有保護的情況下公開弱點，或偵測到任何與 WAF 原則相關聯的前端主機。
 
如果您想要使用 Azure PowerShell 停用受控規則，請參閱 [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject?preserve-view=true&view=azps-4.7.0) 目的檔集。 如果您想要使用 Azure CLI，請參閱 [`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override?preserve-view=true&view=azure-cli-latest) 檔。

![WAF 規則](../media/waf-front-door-tuning/waf-rules.png)

## <a name="finding-request-fields"></a>尋找要求欄位

使用瀏覽器 proxy （例如 [Fiddler](https://www.telerik.com/fiddler)），您可以檢查個別的要求，並判斷要呼叫網頁的特定欄位。 當我們需要在 WAF 中使用排除清單將某些欄位排除在檢查中時，這會很有説明。

### <a name="finding-request-attribute-names"></a>尋找要求屬性名稱
 
在此範例中，您可以看到 `1=1` 呼叫輸入字串的欄位 `comment` 。 這項資料是在 POST 要求的主體中傳遞。

![顯示主體的 Fiddler 要求](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

這是您可以排除的欄位。 若要深入瞭解排除清單，請參閱 [Web 應用程式防火牆排除清單](./waf-front-door-exclusion.md)。 在此情況下，您可以藉由設定下列排除來排除評估：

![排除規則](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

您也可以檢查防火牆記錄來取得資訊，以查看您需要新增到排除清單的內容。 若要啟用記錄功能，請參閱 [Azure Front Door 中的監視計量和記錄](./waf-front-door-monitor.md)。

檢查檔案中的防火牆記錄檔中 `PT1H.json` 是否有您想要檢查的要求。 `PT1H.json` 檔案會在 `FrontDoorWebApplicationFirewallLog` 儲存和診斷記錄的儲存體帳戶容器中提供 `FrontDoorAccessLog` 。

在此範例中，您可以看到封鎖要求 (具有相同交易參考) 的規則，並在相同的時間發生：

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

瞭解 Azure 受控規則集的運作方式 (請參閱 [Azure Front Door 上的 Web 應用程式防火牆](afds-overview.md)) 您知道具有 *action： Block* 屬性的規則會根據要求主體中相符的資料進行封鎖。 您可以在詳細資料中看到它符合模式 (`1=1`) ，並將欄位命名為 `comment` 。 遵循相同先前的步驟，以排除包含的要求主體 post 參數名稱 `comment` 。

### <a name="finding-request-header-names"></a>尋找要求標頭名稱

Fiddler 是一次可用的工具，可尋找要求標頭名稱。 在以下螢幕擷取畫面中，您可以看到此 GET 要求的標頭，其中包含內容類型、使用者代理程式等等。 您也可以使用要求標頭，在 WAF 中建立排除專案和自訂規則。

![顯示標頭的 Fiddler 要求](../media/waf-front-door-tuning/fiddler-request-header-name.png)

另一個查看要求和回應標頭的方式，就是在瀏覽器的開發人員工具（例如 Edge 或 Chrome）內查看。 您可以按 F12 鍵或按一下滑鼠右鍵 >**檢查**  ->  **開發人員工具**]，然後選取 [**網路**] 索引標籤。載入網頁，然後按一下您要檢查的要求。

![網路檢查要求](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>尋找要求 cookie 名稱

如果要求包含 cookie，則可以選取 [Cookie] 索引標籤以在 Fiddler 中加以查看。 您也可以使用 Cookie 資訊，在 WAF 中建立排除專案或自訂規則。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Web 應用程式防火牆](../overview.md)。
- 了解如何[建立 Front Door](../../frontdoor/quickstart-create-front-door.md)。