---
title: 快速入門：使用 PowerShell 引導網路流量
titleSuffix: Azure Application Gateway
description: 在本快速入門中，深入了解如何使用 Azure PowerShell 建立 Azure 應用程式閘道，該應用程式閘道會將網路流量導向至後端集區中的虛擬機器。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 08/27/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 115f01bffc4664798682923cb83a99a23a8ce274
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958315"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>快速入門：透過 Azure PowerShell 使用 Azure 應用程式閘道引導網路流量

在本快速入門中，您會使用 Azure PowerShell 來建立應用程式閘道。 然後，您會進行測試以確定能正常運作。 

應用程式閘道會將應用程式 Web 流量導向至後端集區中的特定資源。 您可以將接聽程式指派給連接埠、建立規則，並將資源新增至後端集區。 為了簡單起見，本文使用簡單的設定，包括公用前端 IP、在此應用程式閘道上裝載單一網站的基本接聽程式、基本的要求路由規則，以及後端集區中的兩部虛擬機器。

您也可以使用 [Azure CLI](quick-create-cli.md) 或 [Azure 入口網站](quick-create-portal.md)完成本快速入門。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Azure PowerShell 1.0.0 版或更新版本](/powershell/azure/install-az-ps) (如果您在本機執行 Azure PowerShell)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure"></a>連線到 Azure

若要與 Azure 連線，請執行 `Connect-AzAccount`。

## <a name="create-a-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 您可以使用現有的資源群組，或建立一個新的群組。

使用 `New-AzResourceGroup` Cmdlet 建立新的資源群組： 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>建立網路資源

Azure 需要虛擬網路才能在您所建立的資源之間進行通訊。  應用程式閘道子網路只能包含應用程式閘道。 不允許任何其他資源。  您可以為應用程式閘道建立新的子網路，或使用現有的子網路。 在此範例中您會建立兩個子網路：一個用於應用程式閘道，另一個用於後端伺服器。 您可以根據自己的使用案例，將應用程式閘道的前端 IP 設定為「公用」或「私人」。 在此範例中，您會選擇公用前端 IP。

1. 使用 `New-AzVirtualNetworkSubnetConfig` 建立子網路組態。
2. 使用 `New-AzVirtualNetwork` 以子網路組態建立虛擬網路。 
3. 使用 `New-AzPublicIpAddress` 建立公用 IP 位址。 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>建立應用程式閘道

### <a name="create-the-ip-configurations-and-frontend-port"></a>建立 IP 設定與前端連接埠

1. 使用 `New-AzApplicationGatewayIPConfiguration`，建立讓您先前建立的子網路與應用程式閘道產生關聯的組態。 
2. 使用 `New-AzApplicationGatewayFrontendIPConfig`，建立會將您先前建立的公用 IP 位址指派給應用程式閘道的組態。 
3. 使用 `New-AzApplicationGatewayFrontendPort` 指派連接埠 80 來存取應用程式閘道。

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>建立後端集區

1. 使用 `New-AzApplicationGatewayBackendAddressPool` 來建立應用程式閘道的後端集區。 後端集區現在會是空的。 當您在下一節中建立後端伺服器 NIC 時，會將其新增至後端集區。
2. 使用 `New-AzApplicationGatewayBackendHttpSetting` 設定後端集區的各項設定。

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>建立接聽程式並新增規則

Azure 需要接聽程式才能讓應用程式閘道將流量適當地路由到後端集區。 Azure 也需要接聽程式的規則，以得知要對傳入的流量使用哪個後端集區。 

1. 使用 `New-AzApplicationGatewayHttpListener` 以及您先前建立的前端設定和前端連接埠，來建立接聽程式。 
2. 使用 `New-AzApplicationGatewayRequestRoutingRule` 建立名為 *rule1* 的規則。 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>建立應用程式閘道

您已建立必要的支援資源，接著請建立應用程式閘道：

1. 使用 `New-AzApplicationGatewaySku` 指定應用程式閘道的參數。
2. 使用 `New-AzApplicationGateway` 建立應用程式閘道。

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>後端伺服器

現在您已建立應用程式閘道，接著請建立將裝載網站的後端虛擬機器。 後端可以包含 NIC、虛擬機器擴展集、公用 IP、內部 IP、完整網域名稱 (FQDN)，以及多租用戶後端，例如 Azure App Service。 在此範例中，您會建立兩個虛擬機器，供 Azure 作為應用程式閘道的後端伺服器。 您也可以在虛擬機器上安裝 IIS，以確認 Azure 已成功建立應用程式閘道。

#### <a name="create-two-virtual-machines"></a>建立兩部虛擬機器

1. 使用 `Get-AzApplicationGatewayBackendAddressPool` 取得最近建立的應用程式閘道後端集區設定。
2. 使用 `New-AzNetworkInterface` 建立網路介面。
3. 使用 `New-AzVMConfig` 建立虛擬機器組態。
4. 使用 `New-AzVM` 建立虛擬機器。

當您執行下列程式碼範例以建立虛擬機器時，Azure 會提示您輸入認證。 輸入「azureuser」  作為使用者名稱，並且輸入密碼：
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>測試應用程式閘道

雖然不需要 IIS 即可建立應用程式閘道，但您仍會在本快速入門中加以安裝，以確認 Azure 是否已成功建立應用程式閘道。 使用 IIS 測試應用程式閘道：

1. 執行 `Get-AzPublicIPAddress` 以取得應用程式閘道的公用 IP 位址。 
2. 將公用 IP 位址複製並貼到瀏覽器的網址列中。 當您重新整理瀏覽器時，應該會看到虛擬機器的名稱。 有效的回應會確認應用程式閘道已成功建立，並可與後端順利連線。

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![測試應用程式閘道](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為應用程式閘道建立的資源時，請刪除資源群組。 當您刪除資源群組時，也可以刪除應用程式閘道及其所有相關資源。 

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure PowerShell 以應用程式閘道管理網路流量](./tutorial-manage-web-traffic-powershell.md)

