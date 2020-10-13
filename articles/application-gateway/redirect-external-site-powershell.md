---
title: 使用 PowerShell 進行外部重新導向
titleSuffix: Azure Application Gateway
description: 了解如何使用 Azure Powershell 建立應用程式閘道，以將 Web 流量重新導向至外部網站。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/24/2020
ms.author: victorh
ms.openlocfilehash: 5b2bb50fc2ad8084d79097fe7acb449821d0a4da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331054"
---
# <a name="create-an-application-gateway-with-external-redirection-using-azure-powershell"></a>使用 Azure PowerShell 建立具有外部重新導向的應用程式閘道

您可以使用 Azure PowerShell，在建立[應用程式閘道](overview.md)時設定 [Web 流量重新導向](multiple-site-overview.md)。 在本教學課程中，您會設定接聽程式和規則，以將抵達應用程式閘道的 Web 流量重新導向至外部網站。

在本文中，您將學會如何：

* 設定網路
* 建立接聽程式和重新導向規則
* 建立應用程式閘道

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本教學課程會要求使用 Azure PowerShell 模組 1.0.0 版或更新版本。 若要尋找版本，請執行 `Get-Module -ListAvailable Az`。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure 資源的邏輯容器。 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>建立網路資源

使用[新的->new-azvirtualnetworksubnetconfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)建立子網設定*myAGSubnet* 。 使用[new-azvirtualnetwork](/powershell/module/az.network/new-azvirtualnetwork)與子網設定建立名為*myVNet*的虛擬網路。 最後，使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 建立公用 IP 位址。 這些資源可用來為應用程式閘道及其相關聯的資源提供網路連線。

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>建立應用程式閘道

### <a name="create-the-ip-configurations-and-frontend-port"></a>建立 IP 設定與前端連接埠

使用 [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)，讓先前建立的 *myAGSubnet* 與應用程式閘道產生關聯。 使用 [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)，將公用 IP 位址指派給應用程式閘道。 然後，您可以使用 [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) 來建立 HTTP 連接埠。

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>建立後端集區和設定

使用[>appgatewaybackendpool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)為應用程式閘道建立名為*defaultPool*的後端集區。 使用 [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) 設定集區的設定。

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name defaultPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-rule"></a>建立接聽程式和規則

需要接聽程式才能讓應用程式閘道適當地路由傳送流量。 使用 >new-azapplicationgatewayHTTPlistener 搭配您先前建立的前端設定和前端埠，來建立接聽 [程式](/powershell/module/az.network/new-azapplicationgatewayhttplistener) 。 接聽程式需要規則才能知道要將傳入流量傳送到何處。 使用[New->new-azapplicationgatewayrequestroutingrule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)建立名為 *>new-azurermapplicationgatewayrequestroutingrule*的基本規則。

```azurepowershell-interactive
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$redirectConfig = New-AzApplicationGatewayRedirectConfiguration `
  -Name myredirect `
  -RedirectType Temporary `
  -TargetUrl "https://bing.com"
$redirectRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name redirectRule `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -RedirectConfiguration $redirectConfig
```

### <a name="create-the-application-gateway"></a>建立應用程式閘道

既然您已建立必要的支援資源，接著請使用 [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) 為名為 *myAppGateway* 的應用程式閘道指定參數，然後使用 [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) 來建立它。

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $redirectRule `
  -RedirectConfigurations $redirectConfig `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>測試應用程式閘道

您可使用 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) 來取得應用程式閘道的公用 IP 位址。 將公用 IP 位址複製並貼到您瀏覽器的網址列。

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

您應該會看到瀏覽器中出現 bing.com**。

## <a name="next-steps"></a>後續步驟

- [使用 Azure PowerShell 來建立具有內部重新導向的應用程式閘道](redirect-internal-site-powershell.md)
