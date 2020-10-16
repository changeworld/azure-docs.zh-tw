---
title: Azure Front Door 中的 Web 應用程式防火牆排除清單-Azure 入口網站
description: 本文提供 Azure 中的排除清單設定的相關資訊，包括 Azure 入口網站。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/05/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 73372f3c38e12d0d4ac972a569da36a04ad533da
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125810"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web 應用程式防火牆 (WAF) 與 Front Door Service 排除清單 

有時候，Web 應用程式防火牆 (WAF) 可能會封鎖您想要允許應用程式使用的要求。 例如，Active Directory 插入用於驗證的權杖。 這些權杖可以包含特殊字元，而這些特殊字元可能會觸發 WAF 規則中的假肯定。 WAF 排除清單可讓您略過 WAF 評估的特定要求屬性。  您可以使用  [PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0)、 [AZURE CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add)、 [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)或 Azure 入口網站來設定排除清單。 下列範例顯示 Azure 入口網站設定。 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>使用 Azure 入口網站設定排除清單
**管理排除**專案可從 WAF 入口網站的**受控規則**下存取

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

- **等於**：此運算子適用於精確比對。 例如，若要選取名為 **>bearertoken**的標頭，請使用 equals 運算子，並將選取器設定為 **>bearertoken**。
- **開頭為**：此運算子會比對開頭與指定之選取器值相符的所有欄位。
- **結尾為**：此運算子會比對結尾與指定之選取器值相符的所有欄位。
- **包含**：此運算子會比對包含與指定之選取器值相符的所有欄位。
- **等於 any**：此運算子符合所有要求欄位。 * 是選取器的值。

標頭和 cookie 名稱不區分大小寫。

您可以將排除清單套用至受控規則集內的所有規則、特定規則群組的規則，或單一規則（如上述範例所示）。 

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>根據 Web 應用程式防火牆記錄定義排除
 [Azure Web 應用程式防火牆監視和記錄](waf-front-door-monitor.md) 會顯示已封鎖要求的相符詳細資料。 如果標頭值、cookie 值、post 引數值或查詢引數值產生某些規則的誤報，您可以排除該部分的要求，使其無法被規則考慮。 下表顯示 WAF 記錄的範例值和對應的排除條件。

|從 WAF 記錄 matchVariableName    |入口網站中的規則排除|
|--------|------|
|CookieValue： SOME_NAME  |要求 cookie 名稱等於 SOME_NAME|
|HeaderValue： SOME_NAME  |要求標頭名稱等於 SOME_NAME|
|PostParamValue： SOME_NAME|  要求主體 post 引數名稱等於 SOME_NAME|
|QueryParamValue： SOME_NAME| 查詢字串引數名稱等於 SOME_NAME|


## <a name="next-steps"></a>下一步

設定 WAF 設定之後，請瞭解如何查看您的 WAF 記錄。 如需詳細資訊，請參閱 [Front Door 診斷](../afds/waf-front-door-monitor.md)。
