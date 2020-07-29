---
title: Azure Front 門板中的 Web 應用程式防火牆排除清單-Azure 入口網站
description: 本文提供在 Azure 前面使用 Azure 入口網站排除清單設定的相關資訊。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77925926"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>具有 Front 服務排除清單的 Web 應用程式防火牆（WAF） 

有時候 Web 應用程式防火牆（WAF）可能會封鎖您想要允許應用程式使用的要求。 例如，Active Directory 插入用於驗證的權杖。 這些權杖可以包含特殊字元，可能會從 WAF 規則引發假的正值。 WAF 排除清單可讓您略過 WAF 評估的特定要求屬性。  您可以使用[PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0)、 [AZURE CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add)、 [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)或 Azure 入口網站來設定排除清單。 下列範例顯示 Azure 入口網站設定。 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>使用 Azure 入口網站設定排除清單
**管理排除**專案可從 WAF 入口網站的 [**受控規則**] 底下存取

![管理排除 ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ 管理 exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 排除清單範例： ![ 管理 exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

這個範例會排除 [*使用者*標頭] 欄位中的值。 有效的要求可能包含*使用者*欄位，其中包含的字串會觸發 SQL 插入式規則。 在此情況下，您可以排除*user*參數，讓 WAF 規則不會評估欄位中的任何專案。

下列屬性可以依名稱新增至排除清單。 您使用的欄位值不會針對 WAF 規則進行評估，但會評估其名稱。 排除清單會移除域值的檢查。

* 要求標頭名稱
* 要求 cookie 名稱
* 查詢字串引數名稱
* 要求主體張貼引數名稱

您可以指定要精確比對要求標頭、本文、Cookie 或查詢字串屬性。  或者，您也可以選擇指定部分相符即可。 下列運算子是支援的比對準則：

- **等於**：此運算子適用於精確比對。 例如，若要選取名為**bearerToken**的標頭，請使用 equals 運算子，並將選取器設定為**bearerToken**。
- **開頭為**：此運算子會比對開頭與指定之選取器值相符的所有欄位。
- **結尾為**：此運算子會比對結尾與指定之選取器值相符的所有欄位。
- **包含**：此運算子會比對包含與指定之選取器值相符的所有欄位。
- **Equals any**：此運算子符合所有要求欄位。 * 是選取器的值。

標頭和 cookie 名稱不區分大小寫。

您可以將排除清單套用至受管理規則集內的所有規則、特定規則群組的規則，或套用至單一規則，如先前範例所示。 

## <a name="next-steps"></a>後續步驟

設定 WAF 設定之後，請瞭解如何查看 WAF 記錄。 如需詳細資訊，請參閱[Front 門板 diagnostics](../afds/waf-front-door-monitor.md)。
