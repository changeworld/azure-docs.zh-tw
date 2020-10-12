---
title: 建立 Azure 應用程式閘道 & 重寫 HTTP 標頭
description: 本文提供有關如何建立「Azure 應用程式閘道」及使用 Azure PowerShell 來重寫 HTTP 標頭的資訊
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: e18288dbc2a09c7e9dd5b0c0e96dfd04ec192596
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89595898"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>建立應用程式閘道及重寫 HTTP 標題

當您建立新的[自動調整規模和區域備援應用程式閘道 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 時，可以使用 Azure PowerShell 來設定[用以重寫 HTTP 要求和回應標頭的規則](rewrite-http-headers.md)

在本文中，您將學會如何：

* 建立自動調整虛擬網路
* 建立保留公用 IP
* 設定應用程式閘道基礎結構
* 指定 HTTP 標頭重寫規則設定
* 指定自動調整
* 建立應用程式閘道
* 測試應用程式閘道

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

本文要求您在本機執行 Azure PowerShell。 您必須安裝 Az 模組 1.0.0 版或更新版本。 請執行 `Import-Module Az`，然後執行 `Get-Module Az` 來尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。 驗證 PowerShell 版本之後，請執行 `Login-AzAccount` 以建立與 Azure 的連線。

## <a name="sign-in-to-azure"></a>登入 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>建立資源群組
在其中一個可用的位置建立資源群組。

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>建立虛擬網路

建立具有一個自動調整應用程式閘道專用子網路的虛擬網路。 目前在每個專用子網路只可以部署一個自動調整應用程式閘道。

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>建立保留公用 IP

將 PublicIPAddress 的配置方法指定為 [靜態]****。 自動調整應用程式閘道 VIP 只能是靜態。 不支援動態 IP。 只支援標準 PublicIpAddress SKU。

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>擷取詳細資料

擷取本機物件中的資源群組、子網路和 IP 詳細資料，來建立應用程式閘道的 IP 設定詳細資料。

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>設定基礎結構

以和現有標準應用程式閘道相同的格式，設定 IP 設定、前端 IP 設定、後端集區、HTTP 設定、憑證、連接埠及接聽程式。 新的 SKU 會遵循與標準 SKU 相同的物件模型。

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>指定 HTTP 標頭重寫規則設定

設定重寫 HTTP 標頭所需的新物件：

- **RequestHeaderConfiguration**：此物件可用來指定您想要重寫的要求標頭欄位，以及原始標頭在重寫後必須採用的新值。
- **ResponseHeaderConfiguration**：此物件可用來指定您想要重寫的回應標頭欄位，以及原始標頭在重寫後必須採用的新值。
- **ActionSet**：此物件包含以上所指定要求和回應標頭的設定。 
- **RewriteRule**：此物件包含以上所指定的所有 *actionSet*。 
- **RewriteRuleSet**：此物件包含所有 *rewriteRule*，且將必須連結至要求路由規則 - 基本或路徑型。

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>指定路由規則

建立要求路由規則。 此重寫設定在建立之後，就會透過路由規則連結至來源接聽程式。 使用基本路由規則時，標頭重寫設定會與來源接聽程式相關聯，並且會是全域標頭重寫。 使用路徑型錄由規則時，會在 URL 路徑對應上定義標頭重寫設定。 因此，它只適用於網站的特定路徑區域。 以下會建立一個基本路由規則，並連結重寫規則集。

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>指定自動調整

現在您可以指定應用程式閘道的自動調整設定。 支援兩種自動調整設定類型：

* **固定容量模式**。 在此模式中，應用程式閘道不會自動調整，並且會以固定的縮放單位容量運作。

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **自動調整模式**。 在此模式中，應用程式閘道會根據應用程式流量模式而自動調整。

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>建立應用程式閘道

建立應用程式閘道，並包含備援區域和自動調整設定。

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>測試應用程式閘道

使用 Get-AzPublicIPAddress 取得應用程式閘道的公用 IP 位址。 將公用 IP 位址或 DNS 名稱複製並貼到您瀏覽器的網址列。

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>清除資源

首先，探索使用應用程式閘道建立的資源。 然後，若不再需要這些資源，您可以使用 `Remove-AzResourceGroup` 命令將資源群組、應用程式閘道和所有相關資源移除。

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>後續步驟

- [建立包含 URL 路徑型路由規則的應用程式閘道](./tutorial-url-route-powershell.md)
