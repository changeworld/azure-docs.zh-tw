---
title: 使用 Azure PowerShell 建立分區的 Windows VM
description: 使用 Azure PowerShell 在可用性區域中建立 Windows 虛擬機器
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 60ce5b868b2a8f955b32e372201613ba66d49eff
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208970"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-powershell"></a>使用 PowerShell 在可用性區域中建立 Windows 虛擬機器

本文詳細說明在 Azure 可用性區域中，使用 Azure PowerShell 建立 Azure 虛擬機器執行 Windows Server 2016。 [可用性區域](../../availability-zones/az-overview.md)是 Azure 區域中的實體獨立區域。 萬一整個資料中心失敗或遺失，使用可用性區域可保護您的應用程式和資料免於受害。

若要使用可用性區域，請在[支援的 Azure 區域](../../availability-zones/az-region.md)中建立虛擬機器。

 

## <a name="sign-in-to-azure"></a>登入 Azure

使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>檢查 VM SKU 可用性
VM 大小或 SKU 的可用性可能因地區和區域而有所不同。 為了協助您規劃如何利用可用性區域，您可以依照 Azure 地區和區域列出可用的 VM SKU。 這項功能可確保您選擇適當的 VM 大小，並取得所需的跨區域復原功能。 如需有關不同 VM 類型和大小的詳細資訊，請參閱 [VM 大小概觀](sizes.md)。

您可以使用 [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) 命令來檢視可用的 VM SKU。 下列範例會列出 eastus2** 地區中可用的 VM SKU：

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

其輸出類似下列扼要的範例，會顯示每個 VM 大小適用的可用性區域：

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 在此範例中，會在 eastus2** 區域中建立名為 myResourceGroup** 的資源群組。 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>建立網路資源

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>建立虛擬網路、子網路和公用 IP 位址 
這些資源用來提供虛擬機器的網路連線能力，並可將它連線到網際網路。 在此範例的 2 ** 中，在 [可用性] 區域中建立 IP 位址。 在稍後步驟中，您會在用於建立 IP 位址的相同區域中建立 VM。

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)" -Sku Standard
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>建立網路安全性群組和網路安全性群組規則 
網路安全性群組可使用輸入和輸出規則來保護虛擬機器。 在此情況下，會建立連接埠 3389 的輸入規則，以允許連入的遠端桌面連線。 我們也會建立連接埠 80 的輸入規則，以允許連入的 Web 流量。

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>建立虛擬機器的網路卡 
使用 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) 建立虛擬機器的網路卡。 網路卡可讓虛擬機器連線到子網路、網路安全性群組和公用 IP 位址。

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>建立虛擬機器

建立虛擬機器組態。 此組態包括部署虛擬機器時所使用的組態，例如虛擬機器映像、大小和驗證組態。 可用性區域支援此範例中的 Standard_DS1_v2** 大小。 建立 IP 位址時，此設定也會指定您所設定的可用性區域。 執行此步驟時，系統會提示您輸入認證。 您輸入的值會設定為虛擬機器的使用者名稱和密碼。

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 來建立虛擬機器。

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>確認受控磁碟的區域

您已在與 VM 相同的可用性區域中建立了 VM 的 IP 位址資源。 在相同的可用性區域中會建立 VM 的受控磁碟資源。 您可使用 [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) 來確認這點：

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

輸出會顯示與 VM 相同的可用性區域中的受控磁碟：

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>後續步驟

在本文中，您已學到如何在可用性區域中建立 VM。 深入瞭解 Azure Vm 的[可用性](availability.md)。
