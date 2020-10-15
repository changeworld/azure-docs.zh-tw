---
title: 將通用 VHD 上傳至 Azure 的 PowerShell 指令碼範例
description: 使用 Resource Manager 部署模型和受控磁碟，將通用 VHD 上傳至 Azure 並新建 VM 的 PowerShell 範例指令碼。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 03e3ea745e00773272cd141aebb845465ee9890c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89072463"
---
# <a name="sample-script-to-upload-a-vhd-to-azure-and-create-a-new-vm"></a>將 VHD 上傳至 Azure 並新建 VM 的範例指令碼

此指令碼會使用來自通用 VM 的本機 .vhd 檔案，將其上傳到 Azure，再建立受控磁碟映像，並新建 VM。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

```powershell
# Provide values for the variables
$resourceGroup = 'myResourceGroup'
$location = 'EastUS'
$storageaccount = 'mystorageaccount'
$storageType = 'Standard_LRS'
$containername = 'mycontainer'
$localPath = 'C:\Users\Public\Documents\Hyper-V\VHDs\generalized.vhd'
$vmName = 'myVM'
$imageName = 'myImage'
$vhdName = 'myUploadedVhd.vhd'
$diskSizeGB = '128'
$subnetName = 'mySubnet'
$vnetName = 'myVnet'
$ipName = 'myPip'
$nicName = 'myNic'
$nsgName = 'myNsg'
$ruleName = 'myRdpRule'
$computerName = 'myComputerName'
$vmSize = 'Standard_DS1_v2'

# Get the username and password to be used for the administrators account on the VM. 
# This is used when connecting to the VM using RDP.

$cred = Get-Credential

# Upload the VHD
New-AzResourceGroup -Name $resourceGroup -Location $location
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccount -Location $location `
    -SkuName $storageType -Kind "Storage"
$urlOfUploadedImageVhd = ('https://' + $storageaccount + '.blob.core.windows.net/' + $containername + '/' + $vhdName)
Add-AzVhd -ResourceGroupName $resourceGroup -Destination $urlOfUploadedImageVhd `
    -LocalFilePath $localPath

# Note: Uploading the VHD may take awhile!

# Create a managed image from the uploaded VHD 
$imageConfig = New-AzImageConfig -Location $location
$imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized `
    -BlobUri $urlOfUploadedImageVhd
$image = New-AzImage -ImageName $imageName -ResourceGroupName $resourceGroup -Image $imageConfig
 
# Create the networking resources
$singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
$pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $resourceGroup -Location $location `
    -AllocationMethod Dynamic
$rdpRule = New-AzNetworkSecurityRuleConfig -Name $ruleName -Description 'Allow RDP' -Access Allow `
    -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
$nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $resourceGroup -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
$vnet = Get-AzVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName

# Start building the VM configuration
$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize

# Set the VM image as source image for the new VM
$vm = Set-AzVMSourceImage -VM $vm -Id $image.Id

# Finish the VM configuration and add the NIC.
$vm = Set-AzVMOSDisk -VM $vm  -DiskSizeInGB $diskSizeGB -CreateOption FromImage -Caching ReadWrite
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

# Create the VM
New-AzVM -VM $vm -ResourceGroupName $resourceGroup -Location $location

# Verify that the VM was created
$vmList = Get-AzVM -ResourceGroupName $resourceGroup
$vmList.Name


```


<!-- 
[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")] -->

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| Command                                                                                                             | 注意                                                                                                                                                                                |
|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)                           | 建立用來存放所有資源的資源群組。                                                                                                                          |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)                         | 建立儲存體帳戶。                                                                                                                                                           |
| [Add-AzVhd](/powershell/module/az.compute/add-azvhd)                                               | 將虛擬硬碟從內部部署虛擬機器上傳至 Azure 雲端儲存體帳戶中的 Blob。                                                                       |
| [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig)                               | 建立可設定的映像物件。                                                                                                                                                 |
| [Set-AzImageOsDisk](/powershell/module/az.compute/set-azimageosdisk)                               | 設定映像物件上的作業系統磁碟屬性。                                                                                                                        |
| [New-AzImage](/powershell/module/az.compute/new-azimage)                                           | 建立新的映像。                                                                                                                                                                 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 建立子網路組態。 此組態可使用於虛擬網路建立程序。                                                                                |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)                         | 建立虛擬網路。                                                                                                                                                           |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)                       | 建立公用 IP 位址。                                                                                                                                                         |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)                     | 建立網路介面。                                                                                                                                                         |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)   | 建立網路安全性群組規則組態。 建立 NSG 時，此組態用來建立 NSG 規則。                                                       |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)             | 建立網路安全性群組。                                                                                                                                                    |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)                         | 取得資源群組中的虛擬網路。                                                                                                                                          |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)                                     | 建立 VM 組態。 此組態包括 VM 名稱、作業系統和系統管理認證等資訊。 建立 VM 時會使用此組態。 |
| [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)                           | 指定虛擬機器的映像。                                                                                                                                            |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk)                                     | 設定虛擬機器上的作業系統磁碟屬性。                                                                                                                      |
| [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)                   | 設定虛擬機器上的作業系統磁碟屬性。                                                                                                                      |
| [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)                 | 將網路介面新增至虛擬機器。                                                                                                                                       |
| [New-AzVM](/powershell/module/az.compute/new-azvm)                                                 | 建立虛擬機器。                                                                                                                                                            |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)                     | 移除資源群組及其內含的所有資源。                                                                                                                         |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure Windows VM 文件](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
