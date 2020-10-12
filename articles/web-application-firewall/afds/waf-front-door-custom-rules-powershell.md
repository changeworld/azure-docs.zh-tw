---
title: 設定 Azure Front Door & 預設規則集的 WAF 自訂規則
description: 瞭解如何設定 WAF 原則，其中包含現有 Front Door 端點的自訂和受控規則。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 40f0195ced375da09891eb577bd81f36b2f37cba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005543"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>使用 Azure PowerShell 來設定 Web 應用程式防火牆原則

Azure Web 應用程式防火牆 (WAF) 原則會定義要求抵達 Front Door 時所需的檢查。
本文說明如何設定 WAF 原則，其中包含某些自訂規則和啟用 Azure 受控預設規則集。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

在您開始設定速率限制原則之前，請先設定 PowerShell 環境並建立 Front Door 設定檔。

### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境

Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)，並將其用於任何 PowerShell 工作階段。 遵循頁面上的指示，使用您的 Azure 認證登入，並安裝 Az PowerShell 模組。

#### <a name="sign-in-to-azure"></a>登入 Azure

```
Connect-AzAccount

```
在安裝 Front Door 模組之前，請確定您已安裝最新版的 PowerShellGet。 執行下列命令，然後重新開啟 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>安裝 Az.FrontDoor 模組 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>建立 Front Door 設定檔

遵循[快速入門：建立 Front Door 設定檔](../../frontdoor/quickstart-create-front-door.md)中所述的指示，建立 Front Door 設定檔

## <a name="custom-rule-based-on-http-parameters"></a>以 HTTP 參數為基礎的自訂規則

下列範例示範如何使用 [AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)，以兩個相符條件設定自訂規則。 要求是來自指定的網站，如訪客所定義，而查詢字串不包含「密碼」。 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>以 HTTP 要求方法為基礎的自訂規則

使用 [AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) 建立封鎖「PUT」方法的規則，如下所示：

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>根據大小條件約束建立自訂規則

下列範例會建立一個規則，以使用 Azure PowerShell 的 Url 來封鎖超過100個字元的要求：
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>新增受管理的預設規則集

下列範例會使用 Azure PowerShell 建立受控預設規則集：
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>設定安全性原則

使用 `Get-AzResourceGroup` 尋找包含 Front Door 設定檔的資源群組名稱。 接下來，使用包含 Front Door 設定檔的指定資源群組中的 [AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) ，在先前步驟中設定具有已建立規則的安全性原則。

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>將原則連結至 Front Door 前端主機

將安全性原則物件連結至現有的 Front Door 前端主機，並更新 Front Door 屬性。 首先，使用 [為](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)來取出 Front Door 物件。
接下來，使用[為](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)將前端*WebApplicationFirewallPolicyLink*屬性設定為上一個步驟中所建立 "$MyWAFPolicy $" 的*resourceId* 。 

下列範例會使用資源組名 *myResourceGroupFD1* ，並假設您已使用 [快速入門：建立 Front Door](../../frontdoor/quickstart-create-front-door.md) 文章中提供的指示來建立 Front Door 設定檔。 此外，在下列範例中，請將 $frontDoorName 取代為您 Front Door 設定檔的名稱。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 您只需要設定 *WebApplicationFirewallPolicyLink* 屬性一次，即可將安全性原則連結至 Front Door 前端。 後續的原則更新會自動套用至前端。

## <a name="next-steps"></a>接下來的步驟

- 深入瞭解 [Front Door](../../frontdoor/front-door-overview.md) 
- 深入瞭解 [WAF 與 Front Door](afds-overview.md)
