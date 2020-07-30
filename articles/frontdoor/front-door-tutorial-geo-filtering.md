---
title: 教學課程 - 設定地區篩選 WAF 原則 - Azure Front Door
description: 在本教學課程中，您會了解如何建立地區篩選原則，並將此原則與現有的 Front Door 前端主機產生關聯
services: frontdoor
documentationcenter: ''
author: teresayao
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: tyao
ms.openlocfilehash: 2ce55ee50a2b302712973b99dc6503f1e6bbc425
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074786"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>如何為 Front Door 設定地區篩選 WAF 原則
本教學課程會說明如何使用 Azure PowerShell 來建立簡單的地區篩選原則，並將此原則與現有的 Front Door 前端主機產生關聯。 此範例地區篩選原則範例會封鎖來自美國以外的所有其他國家/地區的要求。

如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites
在開始設定地區篩選原則之前，請先設定您的 PowerShell 環境，並建立 Front Door 設定檔。
### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境
Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)，並將其用於任何 PowerShell 工作階段。 請遵循頁面上的指示，使用 Azure 認證進行登入，並安裝 Az PowerShell 模組。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用互動式登入對話方塊連線至 Azure
```
Install-Module -Name Az
Connect-AzAccount
```
請確定您已安裝目前的 PowerShellGet 版本。 執行下列命令，然後重新開啟 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>安裝 Az.FrontDoor 模組 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>建立 Front Door 設定檔
請遵循[快速入門：建立 Front Door 設定檔](quickstart-create-front-door.md)中所述的指示來建立 Front Door 設定檔。

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

下列範例會使用資源群組名稱 myResourceGroupFD1，並假設您已使用[快速入門：建立 Front Door](quickstart-create-front-door.md) 一文中所提供的指示建立 Front Door 設定檔。 在下列範例中，將原則名稱 geoPolicyAllowUSOnly 取代為唯一的原則名稱。

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>將 WAF 原則連結至 Front Door 前端主機
將 WAF 原則物件連結至現有的 Front Door 前端主機，並更新 Front Door 屬性。 

若要這樣做，請先使用 [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor) 擷取您的 Front Door 物件。 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

接下來，使用 [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor) 將前端 WebApplicationFirewallPolicyLink 屬性設定為 `geoPolicy` 的 resourceId。

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> 只需要對 WebApplicationFirewallPolicyLink 屬性設定一次，即可將 WAF 原則連結至 Front Door 前端主機。 後續的原則更新會自動套用至前端主機。

## <a name="next-steps"></a>後續步驟
- 了解 [Azure Web 應用程式防火牆](waf-overview.md)。
- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
