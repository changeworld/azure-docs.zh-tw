---
title: 設定 VM 的路由喜好設定 - Azure PowerShell
description: 了解如何使用 Azure PowerShell，建立具有靜態公用 IP 位址和路由喜好設定選擇的 VM。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 8325d63881c72a795e3b9e9a6d1d8498c84972ad
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829319"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>使用 Azure PowerShell 設定 VM 的路由喜好設定

本文說明如何設定虛擬機器的路由喜好設定。 當您選擇**網際網路**作為路由喜好設定選項時，來自 VM 的網際網路繫結流量將會透過 ISP 網路路由傳送。 預設路由是透過 Microsoft 全球網路。

本文說明如何使用 Azure PowerShell 來建立具有公用 IP 的虛擬機器，並將其設定為透過 ISP 網路路由流量。

> [!IMPORTANT]
> 路由喜好設定目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-feature-for-your-subscription"></a>註冊訂用帳戶的功能
「路由喜好設定」功能目前為預覽狀態。 註冊訂用帳戶的功能，如下所示：
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>建立資源群組
1. 如果使用 Cloud Shell，請跳至步驟 2。 開啟命令工作階段，然後使用 `Connect-AzAccount` 登入 Azure。
2. 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。 下列範例會在美國東部 Azure 區域中建立一個資源群組：

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>建立公用 IP 位址

您需要建立公用 IP 位址，透過網際網路存取您的虛擬機器。 使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 建立公用 IP 位址。 下列範例會建立名為 MyPublicIP 的 IPv4 公用 IP 位址， 路由喜好設定類型為「網際網路」，並位於「美國東部」區域的 MyResourceGroup 資源群組中：

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>建立網路資源

您必須先建立支援的網路資源，包括網路安全性群組、虛擬網路和虛擬 NIC，才能部署 VM。

### <a name="create-a-network-security-group"></a>建立網路安全性群組

使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 建立網路安全性群組。 下列範例會建立名為 myNSG 的 NSG

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路。 下列範例會建立名為 myVNET 的虛擬網路和 mySubNet：

### <a name="create-a-subnet"></a>建立子網路

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>建立 NIC

建立具有 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface 的虛擬 NIC。 下列範例會建立虛擬 NIC。

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

現在您可以使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 建立 VM。 下列範例會建立兩個 VM 及必要的虛擬網路元件 (如果尚未存在)。

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>允許對 VM 的網路流量

在可以從網際網路連線到公用 IP 位址之前，請確定您已在可能已與網路介面、網路介面所在的子網路或兩者相關聯的任何網路安全性群組中，開啟所需的連接埠。 您可以使用[入口網站](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)、[CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) 或 [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)，來檢視網路介面及其子網路的有效安全性規則。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

* 深入了解[公用 IP 位址中的路由喜好設定](routing-preference-overview.md)。
* 深入了解 Azure 中的[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
* 深入了解[公用 IP 位址設定](virtual-network-public-ip-address.md#create-a-public-ip-address)。
