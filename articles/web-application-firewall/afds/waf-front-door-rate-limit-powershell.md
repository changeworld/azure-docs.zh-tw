---
title: 為前門配置 WAF 速率限制規則 - Azure 電源外殼
description: 瞭解如何為現有前門終結點配置速率限制規則。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649359"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>使用 Azure PowerShell 配置 Web 應用程式防火牆速率限制規則
Azure 前門的 Azure Web 應用程式防火牆 （WAF） 速率限制規則控制在一分鐘內從用戶端允許的請求數。
本文演示如何配置 WAF 速率限制規則，該規則控制從用戶端允許的請求數到使用 Azure PowerShell 在 URL 中包含 */促銷*的 Web 應用程式的請求數。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites
在開始設置速率限制策略之前，請設置 PowerShell 環境並創建前門設定檔。
### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境
Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，並將其用於任何 PowerShell 工作階段。 按照頁面上的說明操作，使用 Azure 憑據登錄，然後安裝 Az PowerShell 模組。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用登錄的互動式對話方塊連接到 Azure
```
Connect-AzAccount

```
安裝前門模組之前，請確保安裝了當前版本的 PowerShellGet。 運行以下命令並重新打開 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>安裝 Az.FrontDoor 模組 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>建立 Front Door 設定檔
按照["快速入門：創建前門設定檔](../../frontdoor/quickstart-create-front-door.md)"中所述的說明創建前門設定檔

## <a name="define-url-match-conditions"></a>定義 URL 匹配條件
使用[New-AzFrontDoorWafMatch條件物件](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)定義 URL 匹配條件（URL 包含 /促銷）。
以下示例將 */促銷*匹配為*RequestUri*變數的值：

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>創建自訂速率限制規則
使用[新阿茲門沃夫自訂規則物件](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)設置速率限制。 在下面的示例中，限制設置為 1000。 從任何用戶端到促銷頁面在一分鐘內超過 1000 的請求將被阻止，直到下一分鐘開始。

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>配置安全性原則

使用 `Get-AzureRmResourceGroup` 尋找包含 Front Door 設定檔的資源群組名稱。 接下來，使用包含前門設定檔的指定資源組中[的 New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)配置具有自訂速率限制規則的安全性原則。

下面的示例使用資源組名稱*myResourceGroupFD1，* 假定您已使用["快速入門：創建前門"](../../frontdoor/quickstart-create-front-door.md)一文中提供的說明創建了前門設定檔。

 使用[新阿茲門沃夫政策](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>將策略連結到前門前端主機
將安全性原則物件連結到現有的前門前端主機並更新前門屬性。 首先使用[Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)命令檢索前門物件。
接下來，將前端 Web*應用程式防火牆策略連結*屬性設置為使用[Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令在上一步中創建的"$ratePolicy"*的資源 Id。* 

下面的示例使用資源組名稱*myResourceGroupFD1，* 假定您已使用["快速入門：創建前門"](../../frontdoor/quickstart-create-front-door.md)一文中提供的說明創建了前門設定檔。 此外，在下面的示例中，將$frontDoorName替換為前門設定檔的名稱。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 您只需設置一次*Web 應用程式防火牆策略連結*屬性，就將安全性原則連結到前門前端。 後續策略更新將自動應用於前端。

## <a name="next-steps"></a>後續步驟

- 瞭解有關[前門](../../frontdoor/front-door-overview.md)的更多。 


