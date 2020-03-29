---
title: 重寫 Azure 應用程式閘道中的 HTTP 標頭
description: 本文提供有關如何使用 Azure PowerShell 重寫 Azure 應用程式閘道中的 HTTP 標頭的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "64947196"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>使用 Azure 應用程式閘道重寫 HTTP 要求和回應標頭 - Azure PowerShell

本文介紹如何使用 Azure PowerShell 配置[應用程式閘道 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)實例來重寫請求和回應中的 HTTP 標頭。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>開始之前

- 您需要在本地運行 Azure PowerShell 才能完成本文中的步驟。 您還需要安裝 Az 模組版本 1.0.0 或更高版本。 運行`Import-Module Az`，然後`Get-Module Az`確定已安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。 驗證 PowerShell 版本之後，請執行 `Login-AzAccount` 以建立與 Azure 的連線。
- 您需要有一個應用程式閘道 v2 SKU 實例。 v1 SKU 不支援重寫標頭。 如果沒有 v2 SKU，請先創建[應用程式閘道 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)實例。

## <a name="create-required-objects"></a>創建所需物件

要配置 HTTP 標頭重寫，您需要完成這些步驟。

1. 創建 HTTP 標頭重寫所需的物件：

   - **請求標題配置**：用於指定要重寫的請求標頭欄位和標頭的新值。

   - **回應標頭配置**：用於指定要重寫的回應標頭欄位和標頭的新值。

   - **操作集**：包含以前指定的請求和回應標頭的配置。

   - **條件**：可選配置。 重寫條件評估 HTTP（S） 請求和回應的內容。 如果 HTTP（S） 請求或回應與重寫條件匹配，則將執行重寫操作。

     如果將多個條件與操作相關聯，則僅當滿足所有條件時才會執行該操作。 換句話說，該操作是一個邏輯的 AND 操作。

   - **重寫規則**：包含多個重寫操作/重寫條件組合。

   - **規則序列**：一種可選配置，可説明確定重寫規則的執行順序。 當您重寫集中有多個重寫規則時，此配置非常有用。 具有較低規則序列值的重寫規則首先運行。 如果將相同的規則序列值分配給兩個重寫規則，則執行順序是非確定性的。

     如果不顯式指定 RuleSequence，則設置預設值 100。

   - **重寫規則集**：包含與請求路由規則關聯的多個重寫規則。

2. 將重寫規則集附加到路由規則。 重寫配置通過路由規則附加到源攔截器。 使用基本路由規則時，標頭重寫配置與源攔截器關聯，並且是全域標頭重寫。 使用基於路徑的路由規則時，將在 URL 路徑映射上定義標頭重寫配置。 在這種情況下，它僅適用于網站的特定路徑區域。

您可以創建多個 HTTP 標頭重寫集，並將每個重寫集應用於多個攔截器。 但是，您只能將一個重寫集應用於特定攔截器。

## <a name="sign-in-to-azure"></a>登入 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>指定 HTTP 標頭重寫規則配置

在此示例中，每當位置標頭包含對azurewebsites.net的引用時，我們將通過在 HTTP 回應中重寫位置標頭來修改重定向 URL。 為此，我們將添加一個條件來評估回應中的位置標頭是否包含azurewebsites.net。 我們將使用模式`(https?):\/\/.*azurewebsites\.net(.*)$`。 我們將用作`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`標頭值。 此值將在位置標頭中用*contoso.com*替換*azurewebsites.net。*

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>檢索應用程式閘道的配置

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>檢索請求路由規則的配置

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>使用用於重寫 HTTP 標頭的配置更新應用程式閘道

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

要瞭解有關如何設置一些常見用例的更多，請參閱[常見標頭重寫方案](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。