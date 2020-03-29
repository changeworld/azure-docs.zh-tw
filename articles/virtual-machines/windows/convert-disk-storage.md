---
title: 在標準和高級 SSD 之間轉換託管磁片存儲
description: 如何使用 Azure PowerShell 將 Azure 託管磁片從"標準"轉換為高級磁片或高級磁片。
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720940"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>更新受控磁碟的儲存體類型

Azure 託管磁片有四種磁片類型：Azure 超 SSD（預覽）、高級 SSD、標準 SSD 和標準 HDD。 您可以根據您的性能需求在三種 GA 磁片類型（高級 SSD、標準 SSD 和標準硬碟）之間進行切換。 您還不能從超級 SSD 切換到超級 SSD，必須部署新的 SSD。

非託管磁片不支援此功能。 但是，您可以輕鬆地[將非託管磁片轉換為託管磁片](convert-unmanaged-to-managed-disks.md)，以便能夠在磁片類型之間切換。

 

## <a name="prerequisites"></a>Prerequisites

* 由於轉換需要重新開機虛擬機器 （VM），因此應安排在預先存在的維護時段內遷移磁片存儲。
* 如果您的磁片是非託管的，請先[將其轉換為託管磁片，](convert-unmanaged-to-managed-disks.md)以便在存儲選項之間切換。

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>在高級版和標準版之間切換 VM 的所有託管磁片

此示例演示如何將 VM 的所有磁片從標準存儲轉換為高級存儲或從高級存儲轉換為標準存儲。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會切換成可支援進階儲存體的大小：

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在標準磁片和高級磁片之間切換單個託管磁片

對於開發/測試工作負載，您可能需要混合使用標準磁片和高級磁片來降低成本。 您可以選擇僅升級那些需要更好性能的磁片。 此示例演示如何將單個 VM 磁片從標準存儲轉換為高級存儲或從高級存儲轉換為標準存儲。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此示例還演示如何切換到支援高級存儲的大小：

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>在 Azure 門戶中將託管磁片從"標準"轉換為高級磁片

請遵循下列步驟：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 從門戶中的**虛擬機器**清單中選擇 VM。
3. 如果 VM 未停止，請選擇"在 VM**概述"** 窗格頂部的 **"停止"，** 然後等待 VM 停止。
3. 在 VM 的窗格中，從功能表中選擇 **"磁片**"。
4. 選擇要轉換的磁片。
5. 從功能表中選擇 **"配置**"。
6. 將**帳戶類型**從**標準硬碟**更改為高級**SSD**。
7. 按一下"**保存**"並關閉磁片窗格。

磁片類型轉換是暫態轉換。 您可以在轉換後啟動 VM。

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>在標準硬碟和標準 SSD 之間切換託管磁片 

此示例演示如何將單個 VM 磁片從標準硬碟轉換為標準 SSD 或從標準 SSD 轉換為標準硬碟：

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>後續步驟

使用[快照集](snapshot-copy-managed-disk.md)來製作 VM 的唯讀複本。
