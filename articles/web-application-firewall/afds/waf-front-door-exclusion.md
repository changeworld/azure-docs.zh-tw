---
title: Azure Front Door 中的 Web 應用程式防火牆排除清單-Azure 入口網站
description: 本文提供 Azure 中的排除清單設定的相關資訊，包括 Azure 入口網站。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 943124982fe1f2ccf142bb9161ec8ada07e63df5
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444974"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web 應用程式防火牆 (WAF) 與 Front Door Service 排除清單 

有時候，Web 應用程式防火牆 (WAF) 可能會封鎖您想要允許應用程式使用的要求。 例如，Active Directory 插入用於驗證的權杖。 這些權杖可以包含特殊字元，而這些特殊字元可能會觸發 WAF 規則中的假肯定。 WAF 排除清單可讓您略過 WAF 評估的特定要求屬性。  您可以使用  [PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0)、 [AZURE CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add)、 [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)或 Azure 入口網站來設定排除清單。 下列範例顯示 Azure 入口網站設定。 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>使用 Azure 入口網站設定排除清單
**管理排除** 專案可從 WAF 入口網站的 **受控規則** 下存取

![管理排除 ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ 管理 exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 排除清單範例： ![ 管理 exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

此範例會排除 *使用者* 標頭欄位中的值。 有效的要求可能包含 *使用者* 欄位，該欄位包含會觸發 SQL 插入規則的字串。 在此情況下，您可以排除 *使用者* 參數，讓 WAF 規則不會評估欄位中的任何動作。

您可以依名稱將下列屬性新增到排除清單。 您使用的欄位值不會針對 WAF 規則進行評估，但會評估其名稱。 排除清單會移除域值的檢查。

* 要求標頭名稱
* 要求 cookie 名稱
* 查詢字串引數名稱
* 要求主體 post 參數名稱

您可以指定要精確比對要求標頭、本文、Cookie 或查詢字串屬性。  或者，您也可以選擇指定部分相符即可。 以下是支援的符合條件：

- **等於** ：此運算子適用於精確比對。 例如，若要選取名為 **>bearertoken** 的標頭，請使用 equals 運算子，並將選取器設定為 **>bearertoken** 。
- **開頭為** ：此運算子會比對開頭與指定之選取器值相符的所有欄位。
- **結尾為** ：此運算子會比對結尾與指定之選取器值相符的所有欄位。
- **包含** ：此運算子會比對包含與指定之選取器值相符的所有欄位。
- **等於 any** ：此運算子符合所有要求欄位。 * 是選取器的值。

標頭和 cookie 名稱不區分大小寫。

如果標頭值、cookie 值、post 引數值或查詢引數值產生某些規則的錯誤，您可以排除規則所考慮的要求部分：


|從 WAF 記錄 matchVariableName  |入口網站中的規則排除  |
|---------|---------|
|CookieValue： SOME_NAME        |要求 cookie 名稱等於 SOME_NAME|
|HeaderValue： SOME_NAME        |要求標頭名稱等於 SOME_NAME|
|PostParamValue： SOME_NAME     |要求主體 post 引數名稱等於 SOME_NAME|
|QueryParamValue： SOME_NAME    |查詢字串引數名稱等於 SOME_NAME|


我們目前只支援其 WAF 記錄中上述 matchVariableNames 的規則排除。 針對任何其他 matchVariableNames，您必須停用提供誤報的規則，或建立明確允許這些要求的自訂規則。 尤其是當 matchVariableName 是 CookieName、HeaderName、PostParamName 或 QueryParamName 時，這表示名稱本身會觸發規則。 規則排除現在不支援這些 matchVariableNames。


如果您排除名為 *FOO* 的要求本文，則規則不會在 WAF 記錄中顯示 POSTPARAMVALUE： FOO 作為 matchVariableName。 不過，您仍然可能會看到 matchVariableName InitialBodyContents 的規則符合 post param FOO 的值，因為 post 參數值是 InitialBodyContents 的一部分。

您可以將排除清單套用至受控規則集內的所有規則、特定規則群組的規則，或單一規則（如上述範例所示）。

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>根據 Web 應用程式防火牆記錄定義排除
 [Azure Web 應用程式防火牆監視和記錄](waf-front-door-monitor.md) 會顯示已封鎖要求的相符詳細資料。 如果標頭值、cookie 值、post 引數值或查詢引數值產生某些規則的誤報，您可以排除該部分的要求，使其無法被規則考慮。 下表顯示 WAF 記錄的範例值和對應的排除條件。

|從 WAF 記錄 matchVariableName    |入口網站中的規則排除|
|--------|------|
|CookieValue： SOME_NAME  |要求 cookie 名稱等於 SOME_NAME|
|HeaderValue： SOME_NAME  |要求標頭名稱等於 SOME_NAME|
|PostParamValue： SOME_NAME|  要求主體 post 引數名稱等於 SOME_NAME|
|QueryParamValue： SOME_NAME| 查詢字串引數名稱等於 SOME_NAME|


## <a name="next-steps"></a>後續步驟

設定 WAF 設定之後，請瞭解如何查看您的 WAF 記錄。 如需詳細資訊，請參閱 [Front Door 診斷](../afds/waf-front-door-monitor.md)。
