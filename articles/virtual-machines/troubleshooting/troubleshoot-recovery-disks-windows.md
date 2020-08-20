---
title: 使用 Windows 疑難排解 VM 與 Azure PowerShell | Microsoft Docs
description: 了解如何在 Azure 中使用 Azure PowerShell 將 OS 磁碟連接至復原 VM，以針對 Windows VM 問題進行疑難排解
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 63d5e4bcc57a734fc5ea455da7c2db940a4b8ec3
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654679"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>使用 Azure PowerShell 將 OS 磁碟連結至復原 VM，以針對 Windows VM 進行疑難排解
如果 Azure 中的 Windows 虛擬機器 (VM) 發生開機或磁碟錯誤，您可能需要對磁碟本身執行疑難排解步驟。 常見的例子是應用程式更新無效，導致 VM 無法成功開機。 本文詳細說明如何使用 Azure PowerShell 將磁碟連接至另一部 Windows VM，以修正任何錯誤，然後重新建立原始 VM。 

> [!Important]
> 本文中的指令碼只適用於使用[受控磁碟](../managed-disks-overview.md)的 VM。 

 

## <a name="recovery-process-overview"></a>復原程序概觀
我們現在可以使用 Azure PowerShell 來變更 VM 的 OS 磁碟。 我們不再需要刪除及重新建立虛擬機器。

疑難排解程序如下所示︰

1. 停止受影響的 VM。
2. 從 VM 的 OS 磁碟建立快照集。
3. 從 OS 磁碟快照集建立磁碟。
4. 將此磁碟當作資料磁碟連結到復原 VM。
5. 連線至復原 VM。 編輯檔案或執行任何工具來修正所複製 OS 磁碟的問題。
6. 從復原 VM 取消掛接磁碟並中斷其連結。
7. 變更受影響 VM 的 OS 磁碟。

您可以使用 VM repair 命令將步驟1、2、3、4、6和7自動化。 如需詳細的檔和指示，請參閱 [使用 Azure 虛擬機器修復命令修復 WINDOWS VM](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

確定您已安裝[最新的 Azure PowerShell](/powershell/azure/) 並登入您的訂用帳戶。

```powershell
Connect-AzAccount
```

在下列範例中，以您自己的值取代參數名稱。 

## <a name="determine-boot-issues"></a>判斷開機問題
您可以在 Azure 中檢視 VM 的螢幕擷取畫面，以協助疑難排解開機問題。 這個螢幕擷取畫面有助於找出 VM 無法開機的原因。 下列範例會從名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 Windows VM 取得螢幕擷取畫面：

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

檢閱螢幕擷取畫面來判斷 VM 為何無法開機。 請注意任何特定的錯誤訊息或提供的錯誤代碼。

## <a name="stop-the-vm"></a>停止 VM

下列範例會從資源群組 `myResourceGroup` 中停止 VM `myVM`：

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

請等到 VM 完成刪除之後，再繼續下一個步驟。


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>從 VM 的 OS 磁碟建立快照集

下列範例會從 'myVM' VM 的 OS 磁碟建立名為 `mySnapshot` 的快照集。 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

快照集是完整的 VHD 唯讀複本。 無法將它連結至 VM。 在下一個步驟中，我們將從此快照集建立磁碟。

## <a name="create-a-disk-from-the-snapshot"></a>從快照集建立磁碟

此指令碼會從快照集 `mysnapshot` 建立名為 `newOSDisk` 的受控磁碟。  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
您現在有原始 OS 磁碟的複本。 您可以將此磁碟掛接到另一個 Windows VM，以便進行疑難排解。

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>將磁碟連結到另一個 Windows VM 以進行疑難排解

我們現在會將原始 OS 磁碟的複本當作資料磁碟連結到 VM。 此程序可讓您更正任何組態錯誤，或檢閱磁碟中的其他應用程式記錄檔或系統記錄檔。 下列範例會將磁碟 `newOSDisk` 連結至 VM `RecoveryVM`。

> [!NOTE]
> 若要連結此磁碟，原始 OS 磁碟的複本和復原 VM 必須位於相同的位置。

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>連線至復原 VM 並修正所連結磁碟的問題

1. 使用適當的認證以 RDP 登入復原 VM。 下列範例會下載名為 `myResourceGroup` 的資源群組中名為 `RecoveryVM`的 VM 之 RDP 連接檔案，並將它下載至 `C:\Users\ops\Documents`」

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. 系統會自動偵測並連結此資料磁碟。 檢視已連接磁碟區的清單來判斷磁碟機代號，如下所示︰

    ```powershell
    Get-Disk
    ```

    下列範例輸出顯示此磁碟已連接磁碟 **2**。 (您也可以使用 `Get-Volume` 檢視磁碟機代號)：

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

掛接原始 OS 磁碟的複本之後，您可以視需要執行任何維護和疑難排解步驟。 解決問題之後，請繼續進行下列步驟。

## <a name="unmount-and-detach-original-os-disk"></a>取消掛接原始 OS磁碟並中斷其連結
一旦解決錯誤，您就會從復原 VM 取消掛接現有的磁碟並中斷其連結。 直到將此磁碟連結至復原 VM 的租用釋放，您才能將此磁碟使用於其他任何 VM。

1. 從您的 RDP 工作階段內卸載您復原 VM 上的資料磁碟。 您需要先前 `Get-Disk` cmdlet 的磁碟編號。 然後，使用 `Set-Disk` 將磁碟設為離線︰

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    再次使用 `Get-Disk` 確認磁碟現已設為離線。 下列範例輸出顯示磁碟現在設為離線：

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. 結束您的 RDP 工作階段。 在 Azure PowerShell 工作階段中，從名為 'RecoveryVM' 的 VM 中移除磁碟 `newOSDisk`。

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>變更受影響 VM 的 OS 磁碟

您可以使用 Azure PowerShell 來交換 OS 磁碟。 您不需要刪除及重新建立虛擬機器。

此範例會停止名為 `myVM` 的 VM，並將名為 `newOSDisk` 的磁碟指派為新的 OS 磁碟。 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>確認並啟用開機診斷

下列範例會在資源群組 `myResourceGroup` 中的 VM `myVMDeployed` 上啟用診斷擴充：

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>後續步驟
如果連接至 VM 時發生問題，請參閱[針對 Azure VM 的 RDP 連接進行疑難排解](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 如果存取 VM 上執行的應用程式時發生問題，請參閱[針對 Windows VM 上的應用程式連線問題進行疑難排解](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

如需使用 Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。
