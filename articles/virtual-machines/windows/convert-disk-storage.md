---
title: 轉換標準和 premium SSD 之間的受控磁片儲存體
description: 如何使用 Azure PowerShell 將 Azure 受控磁片從 Standard 轉換為 Premium 或 Premium 轉換成標準。
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d81cd0ac57a5a18d90144584e8705cbffcba6f9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871423"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>更新受控磁碟的儲存體類型

Azure 受控磁片有四種磁片類型： Azure ultra 磁片、premium SSD、標準 SSD 和標準 HDD。 您可以根據您的效能需求，切換三個 GA 磁片類型 (premium SSD、標準 SSD 和標準 HDD) 。 您還無法從 ultra 磁片切換，也必須部署一個新的磁片。

非受控磁片不支援此功能。 但是，您可以輕鬆地 [將非受控磁片轉換成受控磁片](convert-unmanaged-to-managed-disks.md) ，以便在磁片類型之間切換。

 

## <a name="prerequisites"></a>必要條件

* 因為轉換需要重新開機虛擬機器 (VM) ，所以您應該在預先存在的維護期間排程磁片儲存體的遷移。
* 如果您的磁片未受管理，請先 [將它轉換成受控磁片](convert-unmanaged-to-managed-disks.md) ，讓您可以在存放裝置選項之間切換。

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>在 Premium 和 Standard 之間切換 VM 的所有受控磁片

此範例示範如何將 VM 的所有磁片從標準轉換至 Premium 儲存體，或從 Premium 轉換成標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](../sizes.md)。 此範例也會切換成可支援進階儲存體的大小：

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

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在 Standard 與 Premium 之間切換個別的受控磁片

針對您的開發/測試工作負載，您可能會想要混用標準和 Premium 磁片來降低成本。 您可以選擇只升級需要更佳效能的磁片。 此範例示範如何將單一 VM 磁片從標準轉換至 Premium 儲存體，或從 Premium 轉換成標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](../sizes.md)。 此範例也會示範如何切換至支援 Premium 儲存體的大小：

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

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>在 Azure 入口網站中將受控磁片從標準轉換為 Premium

請遵循這些步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從入口網站的 **虛擬機器** 清單中選取 VM。
3. 如果 VM 未停止，請選取 [VM 總覽] 窗格頂端的 **[** **停止**]，並等候 vm 停止。
3. 在 VM 的窗格中，從功能表中選取 [ **磁片** ]。
4. 選取您要轉換的磁片。
5. 從 **功能表** 選取 [設定]。
6. 將 **帳戶類型** 從 **標準 HDD** 變更為 **進階 SSD**。
7. 按一下 [ **儲存**]，然後關閉 [磁片] 窗格。

磁片類型轉換是瞬間進行。 您可以在轉換之後啟動 VM。

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>切換標準 HDD 與標準 SSD 之間的受控磁片 

此範例示範如何將單一 VM 磁片從標準 HDD 轉換為標準 SSD，或從標準 SSD 轉換為標準 HDD：

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