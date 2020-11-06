---
title: 重寫 Azure 應用程式閘道中的 HTTP 標頭
description: 本文提供如何使用 Azure PowerShell 在 Azure 應用程式閘道中重寫 HTTP 標頭的相關資訊。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 6938ad55915286af397fee6d72a333e3bb39a1e6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397910"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>使用 Azure 應用程式閘道重寫 HTTP 要求和回應標頭-Azure PowerShell

本文說明如何使用 Azure PowerShell 來設定 [應用程式閘道 V2 SKU](./application-gateway-autoscaling-zone-redundant.md) 實例，以重寫要求和回應中的 HTTP 標頭。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>開始之前

- 您必須在本機執行 Azure PowerShell，才能完成本文中的步驟。 您也需要安裝 Az 模組1.0.0 版或更新版本。 執行 `Import-Module Az` ，然後 `Get-Module Az` 判斷您已安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 驗證 PowerShell 版本之後，請執行 `Login-AzAccount` 以建立與 Azure 的連線。
- 您需要有應用程式閘道 v2 SKU 實例。 V1 SKU 不支援重寫標頭。 如果您沒有 v2 SKU，請在開始之前先建立 [應用程式閘道 V2 sku](./tutorial-autoscale-ps.md) 實例。

## <a name="create-required-objects"></a>建立必要的物件

若要設定 HTTP 標頭重寫，您需要完成這些步驟。

1. 建立 HTTP 標頭重寫所需的物件：

   - **RequestHeaderConfiguration** ：用來指定您想要重寫的要求標頭欄位，以及新的標頭值。

   - **ResponseHeaderConfiguration** ：用來指定您想要重寫的回應標頭欄位，以及新的標頭值。

   - **ActionSet** ：包含先前指定的要求和回應標頭的設定。

   - **條件** ：選擇性的設定。 重寫條件會評估 HTTP (S 的內容) 要求和回應。 如果 HTTP (S) 要求或回應符合重寫條件，則會發生重寫動作。

     如果您將多個條件與某個動作產生關聯，則只有在符合所有條件時，才會發生此動作。 換句話說，此作業是邏輯 AND 運算。

   - **RewriteRule** ：包含多個重寫動作/重寫條件組合。

   - **RuleSequence** ：選擇性的設定，可協助判斷重寫規則的執行順序。 當您在重寫集中有多個重寫規則時，此設定很有説明。 具有較低規則順序值的重寫規則會先執行。 如果您將相同的規則順序值指派給兩個重寫規則，則執行的順序不具決定性。

     如果您未明確指定 RuleSequence，則會設定預設值100。

   - **RewriteRuleSet** ：包含多個將與要求路由規則相關聯的重寫規則。

2. 將 RewriteRuleSet 附加至路由規則。 重寫設定會透過路由規則附加至來源接聽程式。 當您使用基本路由規則時，標頭重寫設定會與來源接聽程式相關聯，而且是全域標頭重寫。 當您使用以路徑為基礎的路由規則時，會在 URL 路徑對應上定義標頭重寫設定。 在這種情況下，它只會套用到網站的特定路徑區域。

您可以建立多個 HTTP 標頭重寫集，並將每個重寫集套用至多個接聽程式。 但是，您只能將一個重寫集套用至特定的接聽程式。

## <a name="sign-in-to-azure"></a>登入 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>指定 HTTP 標頭重寫規則設定

在此範例中，每當 location 標頭包含 azurewebsites.net 的參考時，我們將會在 HTTP 回應中重寫 location 標頭，以修改重新導向 URL。 若要這樣做，我們將新增條件來評估回應中的 location 標頭是否包含 azurewebsites.net。 我們會使用此模式 `(https?):\/\/.*azurewebsites\.net(.*)$` 。 我們會使用 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 做為標頭值。 此值會將 location 標頭中的 *azurewebsites.net* 取代為 *contoso.com* 。

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>取得應用程式閘道的設定

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>取得要求路由規則的設定

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>使用重寫 HTTP 標頭的設定來更新應用程式閘道

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>刪除重寫規則

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何設定一些常見的使用案例，請參閱 [常見的標頭重寫案例](./rewrite-http-headers.md)。