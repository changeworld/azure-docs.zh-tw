---
title: 設定 Front 門板的 WAF 速率限制規則-Azure PowerShell
description: 瞭解如何設定現有 Front 入口端點的速率限制規則。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 29f50b2cf9523b9266de2f73607b0099f32852e1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005407"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>使用 Azure PowerShell 設定 Web 應用程式防火牆速率限制規則
Azure Front 門板的 Azure Web 應用程式防火牆（WAF）速率限制規則會控制用戶端在一分鐘內允許的要求數。
本文說明如何設定 WAF 速率限制規則，以控制從用戶端到使用 Azure PowerShell 的 URL 中包含 */promo*的 web 應用程式所允許的要求數目。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件
開始設定速率限制原則之前，請先設定您的 PowerShell 環境，並建立 Front profile。
### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境
Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)，並將其用於任何 PowerShell 工作階段。 依照頁面上的指示，使用您的 Azure 認證登入，並安裝 Az PowerShell module。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用互動式登入對話方塊連線至 Azure
```
Connect-AzAccount

```
安裝 Front 門板模組之前，請確定已安裝最新版本的 PowerShellGet。 執行下列命令，然後重新開啟 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>安裝 Az.FrontDoor 模組 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>建立 Front Door 設定檔
遵循[快速入門：建立 Front 門板設定檔](../../frontdoor/quickstart-create-front-door.md)中所述的指示，建立 front 設定檔

## <a name="define-url-match-conditions"></a>定義 url 比對條件
使用[AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)定義 url 比對條件（url 包含/promo）。
下列範例會將 */promo*與*RequestUri*變數的值進行比對：

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>建立自訂速率限制規則
使用[AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)設定速率限制。 在下列範例中，限制設定為1000。 在一分鐘內，從任何用戶端到促銷1000頁面的要求都會遭到封鎖，直到下一分鐘開始為止。

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>設定安全性原則

使用 `Get-AzureRmResourceGroup` 尋找包含 Front Door 設定檔的資源群組名稱。 接下來，使用包含 Front 設定檔之指定資源群組中的[AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) ，以自訂速率限制規則設定安全性原則。

下列範例會使用資源組名*myResourceGroupFD1* ，並假設您已使用[快速入門：建立 front 門](../../frontdoor/quickstart-create-front-door.md)文章中提供的指示來建立 front 設定檔。

 使用[新的-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>將原則連結到 Front 前端主機
將安全性原則物件連結到現有的 Front 前端主機，並更新 Front 門板屬性。 請先使用[AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)命令來抓取 Front 物件。
接下來，使用[AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令，將前端*WebApplicationFirewallPolicyLink*屬性設定為上一個步驟中所建立之「$ratePolicy」的*resourceId* 。 

下列範例會使用資源組名*myResourceGroupFD1* ，並假設您已使用[快速入門：建立 front 門](../../frontdoor/quickstart-create-front-door.md)文章中提供的指示來建立 front 設定檔。 此外，在下列範例中，將 $frontDoorName 取代為您 Front 設定檔的名稱。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 您只需要設定*WebApplicationFirewallPolicyLink*屬性一次，即可將安全性原則連結到 front 前端。 後續的原則更新會自動套用至前端。

## <a name="next-steps"></a>接下來的步驟

- 深入瞭解[Front](../../frontdoor/front-door-overview.md)。 


