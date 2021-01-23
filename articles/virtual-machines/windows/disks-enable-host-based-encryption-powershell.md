---
title: Azure PowerShell-啟用 VM 主機的端對端加密
description: 如何使用「在主機加密」來啟用 Azure Vm 的端對端加密。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 46c26b6070a874947dfe5d7acd5a615961576b49
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736679"
---
# <a name="use-the-azure-powershell-module-to-enable-end-to-end-encryption-using-encryption-at-host"></a>使用 Azure PowerShell 模組，在主機上使用加密來啟用端對端加密

當您在主機上啟用加密時，儲存在 VM 主機上的資料會在待用時加密，並將流量加密至儲存體服務。 如需在主機上加密以及其他受控磁片加密類型的概念資訊，請參閱 [VM 資料的主機端對端](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)加密加密。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>支援區域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>支援的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

您也可以透過程式設計的方式找到 VM 大小。 若要瞭解如何以程式設計方式取得它們，請參閱 [尋找支援的 VM 大小](#finding-supported-vm-sizes) 一節。

## <a name="prerequisites"></a>Prerequisites

若要能夠針對您的 Vm 或虛擬機器擴展集使用主機加密，您必須在訂用帳戶上啟用此功能。 傳送電子郵件給 encryptionAtHost@microsoft.com 您的訂用帳戶識別碼，以取得訂用帳戶啟用的功能。

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>建立 Azure Key Vault 和 DiskEncryptionSet

啟用此功能之後，您必須設定 Azure Key Vault 和 DiskEncryptionSet （如果尚未安裝）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>針對連結至 VM 和虛擬機器擴展集的磁片啟用主機加密

您可以使用 API 版本 **2020-06-01** 和更新版本，在 vm 或虛擬機器擴展集的 securityProfile 下設定新的屬性 EncryptionAtHost，以啟用主機的加密。

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="examples"></a>範例

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>使用客戶管理的金鑰，在主機上建立具有加密功能的 VM。 

使用稍早建立的 DiskEncryptionSet 資源 URI 來建立具有受控磁片的 VM，以使用客戶管理的金鑰來加密作業系統和資料磁片的快取。 暫存磁片會使用平臺管理的金鑰進行加密。 

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "yourRegion"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "yourVMSize"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

# Enable encryption at host by specifying EncryptionAtHost parameter

$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize -EncryptionAtHost
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption with a customer managed key for OS disk by setting DiskEncryptionSetId property 

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

# Add a data disk encrypted with a customer managed key by setting DiskEncryptionSetId property 

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>使用平臺管理的金鑰，在主機上建立具有加密功能的 VM。 

建立已啟用「在主機加密」的 VM，以使用平臺管理的金鑰來加密作業系統/資料磁片和暫存磁片的快取。 

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "yourRegion"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "yourVMSize"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

# Enable encryption at host by specifying EncryptionAtHost parameter

$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize -EncryptionAtHost

$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>更新 VM 以啟用主機的加密。 

```powershell
$ResourceGroupName = "yourResourceGroupName"
$VMName = "yourVMName"

$VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName

Stop-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName -Force

Update-AzVM -VM $VM -ResourceGroupName $ResourceGroupName -EncryptionAtHost $true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>檢查 VM 在主機上的加密狀態

```powershell
$ResourceGroupName = "yourResourceGroupName"
$VMName = "yourVMName"

$VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName

$VM.SecurityProfile.EncryptionAtHost
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>使用客戶管理的金鑰，在主機上建立具有加密功能的虛擬機器擴展集。 

使用稍早建立之 DiskEncryptionSet 的資源 URI 來建立具有受控磁片的虛擬機器擴展集，以使用客戶管理的金鑰來加密作業系統和資料磁片的快取。 暫存磁片會使用平臺管理的金鑰進行加密。 

```powershell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

# Enable encryption at host by specifying EncryptionAtHost parameter

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic' -EncryptionAtHost

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption with a customer managed key for the OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted with a customer managed key by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>使用平臺管理的金鑰，在主機上建立具有加密功能的虛擬機器擴展集。 

在啟用主機加密的情況下建立虛擬機器擴展集，以使用平臺管理的金鑰來加密作業系統/資料磁片和暫存磁片的快取。 

```powershell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

# Enable encryption at host by specifying EncryptionAtHost parameter

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic' -EncryptionAtHost

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig
 
$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS 

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>更新虛擬機器擴展集，以啟用主機的加密。 

```powershell
$ResourceGroupName = "yourResourceGroupName"
$VMScaleSetName = "yourVMSSName"

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -Name $VMScaleSetName

Update-AzVmss -VirtualMachineScaleSet $VMSS -Name $VMScaleSetName -ResourceGroupName $ResourceGroupName -EncryptionAtHost $true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>檢查虛擬機器擴展集的主機加密狀態

```powershell
$ResourceGroupName = "yourResourceGroupName"
$VMScaleSetName = "yourVMSSName"

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -Name $VMScaleSetName

$VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
```

## <a name="finding-supported-vm-sizes"></a>尋找支援的 VM 大小

不支援舊版 VM 大小。 您可以透過下列方式尋找支援的 VM 大小清單：

呼叫 [資源 SKU API](/rest/api/compute/resourceskus/list) ，並檢查 `EncryptionAtHostSupported` 功能是否設定為 **True**。

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

或者，呼叫 [>get-azcomputeresourcesku](/powershell/module/az.compute/get-azcomputeresourcesku) PowerShell Cmdlet。

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>後續步驟

現在您已建立並設定這些資源，您可以使用它們來保護受控磁片。 下列連結包含範例腳本，每個都有各自的案例，可讓您用來保護您的受控磁片。

[Azure Resource Manager 範本範例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
