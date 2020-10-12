---
title: 使用 PowerShell 將資料磁片連結至 Azure 中的 Windows VM
description: 如何使用 PowerShell 搭配 Resource Manager 部署模型，將新的或現有的資料磁碟連結至 Windows VM。
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: da4f9fd9fd4305029f42dbe63326c8782d22d907
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87825441"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>使用 PowerShell 將資料磁碟連結至 Windows VM

本文說明如何使用 PowerShell 將新的及現有的磁碟連結至 Windows 虛擬機器。 

首先，請檢閱下列提示：

* 虛擬機器的大小會控制您可以連接的資料磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 若要使用進階 SSD，您將需要[已啟用進階儲存體的 VM 類型](../sizes-memory.md)，例如 DS 系列或 GS 系列的虛擬機器。

本文使用 [Azure Cloud Shell](../../cloud-shell/overview.md)中的 PowerShell，這會持續更新至最新版本。 若要開啟 Cloud Shell，請選取任何程式碼區塊頂端的 [試試看]。

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>將空的資料磁碟新增至虛擬機器

這個範例示範如何將空的資料磁碟新增至現有的虛擬機器。

### <a name="using-managed-disks"></a>使用受控磁碟

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>在可用性區域中使用受控磁碟

若要在可用性區域中建立磁碟，請使用 [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) 並搭配 `-Zone` 參數。 下列範例會在區域 1** 建立磁碟。

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>初始化磁碟

新增空的磁碟之後，您需要將它初始化。 若要將磁碟初始化，您可以登入 VM 並使用磁碟管理。 如果您在建立 VM 時於 VM 上啟用 [WinRM](/windows/desktop/winrm/portal) 和憑證，您便可以使用遠端 PowerShell 將磁碟初始化。 您也可以使用自訂指令碼擴充：

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

指令檔可以包含用以將磁碟初始化的程式碼，例如︰

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>將現有的資料磁碟附加至 VM

您也可以將現有的受控磁碟連結至虛擬機器作為資料磁碟。

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>後續步驟

您也可以使用範本部署受控磁片。 如需詳細資訊，請參閱 [在 Azure Resource Manager 範本中使用受控磁碟](using-managed-disks-template-deployments.md) 或部署多個資料磁片的 [快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) 。