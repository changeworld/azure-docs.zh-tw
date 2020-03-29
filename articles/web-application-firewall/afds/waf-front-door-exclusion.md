---
title: Azure 前門中的 Web 應用程式防火牆排除清單 - Azure 門戶
description: 本文提供有關 Azure Front 與 Azure 門戶一起配置排除清單配置的資訊。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925926"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>包含前門服務排除清單的 Web 應用程式防火牆 （WAF） 

有時，Web 應用程式防火牆 （WAF） 可能會阻止您希望允許的應用程式的請求。 例如，Active Directory 插入用於身份驗證的權杖。 這些權杖可以包含特殊字元，這些字元可能會觸發 WAF 規則中的誤報。 WAF 排除清單可讓您略過 WAF 評估的特定要求屬性。  可以使用[PowserShell、Azure](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0) [CLI、Rest](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add) [API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)或 Azure 門戶配置排除清單。 下面的示例顯示了 Azure 門戶配置。 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>使用 Azure 門戶配置排除清單
根據**託管規則**可從 WAF 門戶訪問**管理排除**項

![管理排除](../media/waf-front-door-exclusion/exclusion1.png)
![管理管理exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 排除清單示例：![管理exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

此示例排除*使用者*標頭欄位中的值。 有效的請求可能包括包含觸發 SQL 注入規則的字串*的使用者*欄位。 在這種情況下，您可以排除*使用者*參數，以便 WAF 規則不會計算欄位中的任何內容。

以下屬性可以按名稱添加到排除清單中。 不會根據 WAF 規則計算您使用的欄位的值，但會計算它們的名稱。 排除清單將刪除對欄位值的檢查。

* 請求標頭名稱
* 請求 Cookie 名稱
* 查詢字串 args 名稱
* 請求正文帖子 args 名稱

您可以指定要精確比對要求標頭、本文、Cookie 或查詢字串屬性。  或者，您也可以選擇指定部分相符即可。 以下運算子是支援的匹配條件：

- **等於**：此運算子適用於精確比對。 例如，要選擇名為**bearerToken**的標頭，請使用等於運算子，選擇器設置為**無記名標記**。
- **開頭為**：此運算子會比對開頭與指定之選取器值相符的所有欄位。
- **結尾為**：此運算子會比對結尾與指定之選取器值相符的所有欄位。
- **包含**：此運算子會比對包含與指定之選取器值相符的所有欄位。
- **等於任何**：此運算子匹配所有請求欄位。 * 是選擇器值。

標頭和 Cookie 名稱不區分大小寫。

可以將排除清單應用於託管規則集中的所有規則、特定規則組的規則或單個規則，如上例所示。 

## <a name="next-steps"></a>後續步驟

配置 WAF 設置後，瞭解如何查看 WAF 日誌。 有關詳細資訊，請參閱[前門診斷](../afds/waf-front-door-monitor.md)。
