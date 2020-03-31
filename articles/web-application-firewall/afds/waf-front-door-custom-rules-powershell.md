---
title: 配置 WAF 自訂規則& Azure 前門的預設規則集
description: 瞭解如何配置 WAF 策略包含現有前門終結點的自訂和託管規則。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186641"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>使用 Azure PowerShell 配置 Web 應用程式防火牆策略

Azure Web 應用程式防火牆 （WAF） 策略定義請求到達前門時所需的檢查。
本文演示如何配置由某些自訂規則和啟用 Azure 管理的預設規則集的 WAF 策略。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

在開始設置速率限制策略之前，請設置 PowerShell 環境並創建前門設定檔。

### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境

Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，並將其用於任何 PowerShell 工作階段。 按照頁面上的說明操作，使用 Azure 憑據登錄，然後安裝 Az PowerShell 模組。

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

按照["快速入門：創建前門設定檔](../../frontdoor/quickstart-create-front-door.md)"中所述的說明創建前門設定檔

## <a name="custom-rule-based-on-http-parameters"></a>基於 HTTP 參數的自訂規則

下面的示例演示如何使用[New-AzFrontDoorWafMatch條件物件](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)配置具有兩個匹配條件的自訂規則。 請求來自引用者定義的指定網站，查詢字串不包含"密碼"。 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>基於 HTTP 請求方法的自訂規則

使用[New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)創建阻止"PUT"方法的規則，如下所示：

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>基於大小約束創建自訂規則

下面的示例使用 Azure PowerShell 創建 Url 超過 100 個字元的規則阻止請求：
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>添加託管預設規則集

以下示例使用 Azure PowerShell 創建託管預設規則集：
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>配置安全性原則

使用 `Get-AzResourceGroup` 尋找包含 Front Door 設定檔的資源群組名稱。 接下來，使用包含前門設定檔的指定資源組中[的 New-AzFrontDoorWafPolicy，](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)在前面的步驟中使用創建的規則配置安全性原則。

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>將策略連結到前門前端主機

將安全性原則物件連結到現有的前門前端主機並更新前門屬性。 首先，使用[Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)檢索前門物件。
接下來，將前端 Web*應用程式防火牆策略連結*屬性設置為使用[Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)在上一步中創建的"$myWAFPolicy$"*的資源識別碼。* 

下面的示例使用資源組名稱*myResourceGroupFD1，* 假定您已使用["快速入門：創建前門"](../../frontdoor/quickstart-create-front-door.md)一文中提供的說明創建了前門設定檔。 此外，在下面的示例中，將$frontDoorName替換為前門設定檔的名稱。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 您只需設置一次*Web 應用程式防火牆策略連結*屬性，就將安全性原則連結到前門前端。 後續策略更新將自動應用於前端。

## <a name="next-steps"></a>後續步驟

- 瞭解有關[前門的更多](../../frontdoor/front-door-overview.md) 
- 瞭解有關[帶前門的 WAF](afds-overview.md)的更多詳細資訊
