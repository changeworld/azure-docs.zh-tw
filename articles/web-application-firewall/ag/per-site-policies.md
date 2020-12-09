---
title: 使用 PowerShell 設定個別網站的 WAF 原則
titleSuffix: Azure Web Application Firewall
description: 了解如何使用 Azure PowerShell，在應用程式閘道上設定個別網站的 Web 應用程式防火牆原則。
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 12/09/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: bf6bfd02610e92825d96255e0ee914fada091805
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932076"
---
# <a name="configure-per-site-waf-policies-using-azure-powershell"></a>使用 Azure PowerShell 設定個別網站的 WAF 原則

Web 應用程式防火牆 (WAF) 設定包含在 WAF 原則中，若要變更 WAF 設定，請修改 WAF 原則。

當與應用程式閘道建立關聯時，則會全域反映原則和所有設定。 因此，如果 WAF 後方有五個網站，則所有五個網站都會受到相同 WAF 原則的保護。 這很適合所有網站都需要相同安全性設定的情況。 但您也可以將 WAF 原則套用至個別接聽程式，以允許網站特定的 WAF 設定。

藉由將 WAF 原則套用至接聽程式，即可設定個別網站的 WAF 設定，而不需要影響所有網站的變更。 最特定的原則會優先採用。 如果有全域原則和個別網站原則 (與接聽程式建立關聯的 WAF 原則)，則個別網站原則會覆寫該接聽程式的全域 WAF 原則。 本身沒有原則的其他接聽程式只會受到全域 WAF 原則影響。

在本文中，您將學會如何：

* 設定網路
* 建立 WAF 原則
* 建立已啟用 WAF 的應用程式閘道
* 全域、依個別網站及依個別 URI 套用 WAF 原則 
* 建立虛擬機器擴展集
* 建立儲存體帳戶並設定診斷
* 測試應用程式閘道

![Web 應用程式防火牆範例](../media/tutorial-restrict-web-traffic-powershell/scenario-waf.png)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝並使用 PowerShell，此文章會要求使用 Azure PowerShell 模組 1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure 資源的邏輯容器。 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。  

```azurepowershell-interactive
$rgname = New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>建立網路資源 

使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 建立名為 myBackendSubnet 和 myAGSubnet 的子網路設定。 使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 搭配子網路設定來建立名為 *myVNet* 的虛擬網路。 最後，使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 來建立名為 *myAGPublicIPAddress* 的公用 IP 位址。 這些資源可用來為應用程式閘道及其相關聯的資源提供網路連線。

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>建立應用程式閘道

在此節中，您會建立支援應用程式閘道的資源，最後會建立應用程式閘道和 WAF。 您所建立的資源包括：

- IP 組態和前端連接埠 - 將您先前建立的子網路與應用程式閘道產生關聯，並且指派用來存取它的連接埠。
- 預設集區 - 所有應用程式閘道都必須具有至少一個伺服器後端集區。
- 預設接聽程式和規則 - 預設接聽程式會接聽所指派連接埠上的流量，而預設規則會將流量傳送到預設集區。

### <a name="create-the-ip-configurations-and-frontend-port"></a>建立 IP 設定與前端連接埠

使用 [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)，讓先前建立的 *myAGSubnet* 與應用程式閘道產生關聯。 使用 [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)，將 *myAGPublicIPAddress* 指派給應用程式閘道。

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[1]

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport80 = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
  
$frontendport8080 = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 8080
```

### <a name="create-the-backend-pool-and-settings"></a>建立後端集區和設定

使用 [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)，為應用程式閘道建立名為 *appGatewayBackendPool* 的後端集區。 使用 [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) 來設定後端位址集區的設定。

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-two-waf-policies"></a>建立兩個 WAF 原則

建立兩個 WAF 原則，一個用於全域，另一個用於個別網站，然後新增自訂原則。 

個別網站原則的檔案上傳限制為 5 MB。 其他一切都相同。

```azurepowershell-interactive
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "globalAllow" 
$rule = New-AzApplicationGatewayFirewallCustomRule -Name globalAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition -Action Allow

$variable1 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable1 -Operator Contains -MatchValue "globalBlock" 
$rule1 = New-AzApplicationGatewayFirewallCustomRule -Name globalBlock -Priority 10 -RuleType MatchRule -MatchCondition $condition1 -Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition2 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable2 -Operator Contains -MatchValue "siteAllow" 
$rule2 = New-AzApplicationGatewayFirewallCustomRule -Name siteAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition2 -Action Allow

$variable3 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition3 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable3 -Operator Contains -MatchValue "siteBlock" 
$rule3 = New-AzApplicationGatewayFirewallCustomRule -Name siteBlock -Priority 10 -RuleType MatchRule -MatchCondition $condition3 -Action Block

$variable4 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition4 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable4 -Operator Contains -MatchValue "URIAllow" 
$rule4 = New-AzApplicationGatewayFirewallCustomRule -Name URIAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition4 -Action Allow

$variable5 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition5 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable5 -Operator Contains -MatchValue "URIBlock" 
$rule5 = New-AzApplicationGatewayFirewallCustomRule -Name URIBlock -Priority 10 -RuleType MatchRule -MatchCondition $condition5 -Action Block

$policySettingGlobal = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 256

$wafPolicyGlobal = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicyGlobal `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingGlobal `
  -CustomRule $rule, $rule1

$policySettingSite = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 5

$wafPolicySite = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicySite `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingSite `
  -CustomRule $rule2, $rule3
```

### <a name="create-the-default-listener-and-rule"></a>建立預設接聽程式和規則

需要接聽程式才能讓應用程式閘道將流量適當地路由到後端位址集區。 在此範例中，您會建立基本接聽程式，接聽根 URL 的流量。 

使用 [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) 搭配您先前建立的前端設定和前端連接埠，來建立名為 *mydefaultListener* 的接聽程式。 接聽程式需要規則以便知道要針對連入流量使用哪個後端集區。 使用 [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) 來建立名為 *rule1* 的基本規則。

```azurepowershell-interactive
$globalListener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport80

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $globallistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
  
$siteListener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport8080 `
  -FirewallPolicy $wafPolicySite
  
$frontendRuleSite = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $siteListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-waf"></a>建立包含 WAF 的應用程式閘道

您已經建立必要的支援資源，接著請使用 [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) 指定應用程式閘道的參數。 使用 [New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/new-azapplicationgatewayfirewallpolicy) 來指定防火牆原則。 然後使用 [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) 建立名為 myAppGateway 的應用程式閘道。

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name WAF_v2 `
  -Tier WAF_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport80 `
  -HttpListeners $globallistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -FirewallPolicy $wafPolicyGlobal
```

### <a name="apply-a-per-uri-policy"></a>套用個別 URI 原則

若要套用個別 URI 原則，只要建立新的原則並將其套用至路徑規則設定即可。 

```azurepowershell-interactive
$policySettingURI = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 5

$wafPolicyURI = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicySite `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingURI `
  -CustomRule $rule4, $rule5

$Gateway = Get-AzApplicationGateway -Name "myAppGateway"

$PathRuleConfig = New-AzApplicationGatewayPathRuleConfig -Name "base" `
  -Paths "/base" `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings `
  -FirewallPolicy $wafPolicyURI

$PathRuleConfig1 = New-AzApplicationGatewayPathRuleConfig `
  -Name "base" -Paths "/test" `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings

$URLPathMap = New-AzApplicationGatewayUrlPathMapConfig -Name "PathMap" `
  -PathRules $PathRuleConfig, $PathRuleConfig1 `
  -DefaultBackendAddressPoolId $defaultPool.Id `
  -DefaultBackendHttpSettingsId $poolSettings.Id

Add-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $AppGw `
  -Name "RequestRoutingRule" `
  -RuleType PathBasedRouting `
  -HttpListener $siteListener `
  -UrlPathMap $URLPathMap
```

## <a name="create-a-virtual-machine-scale-set"></a>建立虛擬機器擴展集

在此範例中，您會建立虛擬機器擴展集，以在應用程式閘道中提供後端集區的伺服器。 當您設定 IP 設定時，要將擴展集指派給後端集區。

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[0].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic

Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage

Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss

Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig

New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>安裝 IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss

Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>建立儲存體帳戶並設定診斷

在本文中，應用程式閘道會使用儲存體帳戶來儲存資料，以達到偵測與預防的目的。 您也可以使用 Azure 監視器記錄或事件中樞來記錄資料。

### <a name="create-the-storage-account"></a>建立儲存體帳戶

使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 建立名為 myagstore1 的儲存體帳戶。

```azurepowershell-interactive
$storageAccount = New-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location eastus `
  -SkuName "Standard_LRS"
```

### <a name="configure-diagnostics"></a>設定診斷

使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) 設定診斷以將資料記錄到 ApplicationGatewayAccessLog、ApplicationGatewayPerformanceLog 和 ApplicationGatewayFirewallLog 記錄。

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$store = Get-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1

Set-AzDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Category ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>測試應用程式閘道

您可使用 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) 來取得應用程式閘道的公用 IP 位址。 然後使用此 IP 位址進行 curl 處理 (取代以下顯示的 1.1.1.1)。 

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress

#should be blocked
curl 1.1.1.1/globalBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1/globalAllow?1=1

#should be blocked
curl 1.1.1.1:8080/siteBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1:8080/siteAllow?1=1

#should be blocked
curl 1.1.1.1/URIBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1/URIAllow?1=1
```

![在應用程式閘道中測試基底 URL](../media/tutorial-restrict-web-traffic-powershell/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>清除資源

當不再需要資源群組、應用程式閘道及所有相關資源時，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 來將其移除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>後續步驟

[自訂 Web 應用程式防火牆規則](application-gateway-customize-waf-rules-portal.md)
