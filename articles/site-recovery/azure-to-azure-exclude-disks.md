---
title: 使用 Azure 網站恢復和 Azure PowerShell 從複製中排除 Azure VM 磁片
description: 瞭解如何使用 Azure PowerShell 在 Azure 網站恢復期間排除 Azure 虛擬機器的磁片。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973684"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>從 Azure VM 的 PowerShell 複製中排除磁片

本文介紹在複製 Azure VM 時如何排除磁片。 您可以排除磁片以優化消耗的複製頻寬或這些磁片使用的目標端資源。 目前，此功能只能通過 Azure PowerShell 提供。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

開始之前：

- 請確保您瞭解[災害復原體系結構和元件](azure-to-azure-architecture.md)。
- 查看所有元件[的支援要求](azure-to-azure-support-matrix.md)。
- 請確保您具有 AzureRm 電源外殼"Az"模組。 要安裝或更新 PowerShell，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 確保至少創建了一次恢復服務保存庫和保護虛擬機器一次。 如果尚未執行這些操作，請按照[使用 Azure PowerShell 為 Azure 虛擬機器設置災害復原](azure-to-azure-powershell.md)的過程進行操作。
- 如果要查找有關將磁片添加到啟用的用於複製的 Azure VM 的資訊，[請查看本文](azure-to-azure-enable-replication-added-disk.md)。

## <a name="why-exclude-disks-from-replication"></a>為什麼從複製中排除磁片
您可能需要從複製中排除磁片，因為：

- 虛擬機器已達到 Azure[網站恢復限制，以複製資料更改率](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)。

- 在排除的磁片上改動的資料並不重要或不需要複製。

- 您希望通過不復制資料來節省存儲和網路資源。

## <a name="how-to-exclude-disks-from-replication"></a>如何從複製中排除磁片

在我們的示例中，我們將具有一個作業系統和三個數據磁片的虛擬機器複製到美國西部 2 區域。 虛擬機器的名稱是*AzureDemoVM。* 我們排除磁片 1 並保留磁片 2 和 3。

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>獲取要複製的虛擬機器的詳細資訊

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

獲取有關虛擬機器磁片的詳細資訊。 稍後，當您開始複製 VM 時，將使用此資訊。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>複製 Azure 虛擬機器

對於以下示例，我們假定您已經擁有緩存存儲帳戶、複寫原則和映射。 如果沒有這些操作，請按照[使用 Azure PowerShell 為 Azure 虛擬機器設置災害復原](azure-to-azure-powershell.md)的過程執行此過程。

使用*託管磁片*複製 Azure 虛擬機器。

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

啟動複製操作成功後，VM 資料將複製到恢復區域。

您可以轉到 Azure 門戶，並在"複製專案"下查看複製的 VM。

複製過程首先在恢復區域中存儲虛擬機器複製磁片的副本。 此階段稱為初始複製階段。

初始複製完成後，複製將轉到差異同步階段。 此時，虛擬機器已受到保護。 選擇受保護的虛擬機器以查看是否排除任何磁片。

## <a name="next-steps"></a>後續步驟

瞭解如何[運行測試容錯移轉](site-recovery-test-failover-to-azure.md)。
