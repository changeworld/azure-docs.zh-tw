---
title: 從使用 Azure Site Recovery 和 Azure PowerShell 的複寫中排除 Azure VM 磁片
description: 瞭解如何使用 Azure PowerShell 在 Azure Site Recovery 期間排除 Azure 虛擬機器的磁片。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75973684"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>從 Azure Vm 的 PowerShell 複寫排除磁片

本文說明如何在複寫 Azure Vm 時排除磁片。 您可能會排除磁片，以優化使用的複寫頻寬或這些磁片使用的目標端資源。 目前，這項功能只能透過 Azure PowerShell 使用。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

開始之前：

- 請確定您瞭解嚴重損壞[修復架構和元件](azure-to-azure-architecture.md)。
- 請參閱所有元件的[支援需求](azure-to-azure-support-matrix.md)。
- 請確定您已 AzureRm PowerShell "Az" 模組。 若要安裝或更新 PowerShell，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 請確定您已建立復原服務保存庫和受保護的虛擬機器至少一次。 如果您尚未完成這些動作，請遵循[使用 Azure PowerShell 設定 Azure 虛擬機器的](azure-to-azure-powershell.md)嚴重損壞修復中的程式。
- 如果您想要瞭解如何將磁片新增至啟用複寫的 Azure VM，請[參閱這篇文章](azure-to-azure-enable-replication-added-disk.md)。

## <a name="why-exclude-disks-from-replication"></a>為何排除磁片不進行複寫
您可能需要從複寫中排除磁片，因為：

- 您的虛擬機器已達到[Azure Site Recovery 的限制，因此無法複寫資料變更率](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)。

- 在排除的磁片上變換的資料不重要或不需要複寫。

- 您想要藉由不要複寫資料來儲存儲存體和網路資源。

## <a name="how-to-exclude-disks-from-replication"></a>如何排除磁片不進行複寫

在我們的範例中，我們會將具有一個作業系統的虛擬機器和位於美國東部區域的三個數據磁片複寫到美國西部2區域。 虛擬機器的名稱是*AzureDemoVM*。 我們排除磁片1並保留磁片2和3。

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>取得要複寫之虛擬機器的詳細資料

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

取得虛擬機器磁片的詳細資料。 稍後當您開始複寫 VM 時，將會用到這項資訊。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>複寫 Azure 虛擬機器

在下列範例中，我們假設您已經有快取儲存體帳戶、複寫原則和對應。 如果您沒有這些專案，請遵循[使用 Azure PowerShell 設定 Azure 虛擬機器的](azure-to-azure-powershell.md)嚴重損壞修復中的程式。

複寫具有*受控磁片*的 Azure 虛擬機器。

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

當開始複寫作業成功時，VM 資料會複寫至復原區域。

您可以移至 Azure 入口網站，並查看 [複寫的專案] 底下的已複寫 Vm。

複寫程式一開始會在復原區域中植入虛擬機器的複寫磁片複本。 這個階段稱為「初始複寫」階段。

初始複寫完成後，複寫會移至差異同步處理階段。 此時，虛擬機器已受到保護。 選取受保護的虛擬機器，以查看是否已排除任何磁片。

## <a name="next-steps"></a>後續步驟

瞭解如何[執行測試容錯移轉](site-recovery-test-failover-to-azure.md)。
