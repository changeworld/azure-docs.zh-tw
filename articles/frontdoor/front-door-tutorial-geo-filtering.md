---
title: 教學課程 - 設定地區篩選 WAF 原則 - Azure Front Door
description: 在本教學課程中，您會了解如何建立地區篩選原則，並將此原則與現有的 Front Door 前端主機產生關聯。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 20aa038e15b1ae5734ad6f463c6f450368617119
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090020"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>教學課程：如何為 Front Door 設定地區篩選 WAF 原則
本教學課程會說明如何使用 Azure PowerShell 來建立簡單的地區篩選原則，並將此原則與現有的 Front Door 前端主機產生關聯。 此範例地區篩選原則範例會封鎖來自美國以外的所有其他國家/地區的要求。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 定義地區篩選比對條件。
> - 將地區篩選比對條件新增至規則。
> - 將規則新增至原則。
> - 將 WAF 原則連結至 Front Door 前端主機。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites
* 在開始設定地區篩選原則之前，請先設定您的 PowerShell 環境，並建立 Front Door 設定檔。
* 請遵循以下文章中所述的指示來建立 Front Door：[快速入門：建立 Front Door 設定檔](quickstart-create-front-door.md)中所述的指示來建立 Front Door 設定檔。

## <a name="define-geo-filtering-match-condition"></a>定義地區篩選比對條件

建立比對條件範例，使其會在建立比對條件時於參數上使用 [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)，而選取並非來自 "US" 的要求。 [此處](front-door-geo-filtering.md)會提供兩個字母的國家/區域代碼對國家/區域對應。

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>使用動作和優先順序在規則中新增地區篩選比對條件

使用 [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)，根據比對條件、動作和優先順序來建立 CustomRule 物件 `nonUSBlockRule`。  CustomRule 可以有多個 MatchCondition。  在此範例中，動作會設定為區塊，優先順序會設定為 1 (此為最高優先順序)。

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```
## <a name="add-rules-to-a-policy"></a>在原則中新增規則
使用 `Get-AzResourceGroup` 尋找包含 Front Door 設定檔的資源群組名稱。 接下來，使用 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)，在包含 Front Door 設定檔的指定資源群組中，建立包含 `nonUSBlockRule` 的 `geoPolicy` 原則物件。 您必須為地區篩選原則提供唯一的名稱。 

下列範例會使用資源群組名稱 FrontDoorQS_rg0，並假設您已使用以下文章中的指示來建立 Front Door 設定檔：[快速入門：建立 Front Door](quickstart-create-front-door.md) 一文中所提供的指示建立 Front Door 設定檔。 在下列範例中，將原則名稱 geoPolicyAllowUSOnly 取代為唯一的原則名稱。

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>將 WAF 原則連結至 Front Door 前端主機
將 WAF 原則物件連結至現有的 Front Door 前端主機，並更新 Front Door 屬性。 

若要這樣做，請先使用 [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor) 擷取您的 Front Door 物件。 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
接下來，使用 [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor) 將前端 WebApplicationFirewallPolicyLink 屬性設定為 `geoPolicy` 的 resourceId。

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> 只需要對 WebApplicationFirewallPolicyLink 屬性設定一次，即可將 WAF 原則連結至 Front Door 前端主機。 後續的原則更新會自動套用至前端主機。

## <a name="clean-up-resources"></a>清除資源

在上述步驟中，您已設定與 WAF 原則相關聯的地區篩選規則。 接著，您可以將原則連結至 Front Door 的前端主機。 如果您不再需要地區篩選規則或 WAF 原則，您必須先取消原則與前端主機的關聯，才能刪除 WAF 原則。

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="取消關聯 WAF 原則":::

## <a name="next-steps"></a>後續步驟

若要了解如何為您的 Front Door 設定 Web 應用程式防火牆，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [Web 應用程式防火牆和 Front Door](front-door-waf.md)
