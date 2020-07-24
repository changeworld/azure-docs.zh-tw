---
title: 複寫在鄰近放置群組中執行的 Azure VM
description: 了解如何使用 Azure Site Recovery，複寫在鄰近放置群組中執行的 Azure VM。
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: ec516ac1cd9c2a6201bfc77bd1169bcd8ea83e44
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091499"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>將在鄰近放置群組中執行的 Azure 虛擬機器複寫至另一個區域

本文說明如何將在鄰近放置群組中執行的虛擬機器複寫、容錯移轉及容錯回復至次要區域。

[鄰近放置群組](../virtual-machines/windows/proximity-placement-groups-portal.md)是一項 Azure 虛擬機器邏輯群組功能，可用來減少與應用程式相關聯的 VM 間網路延遲。 VM 部署在相同的鄰近放置群組內時，其實際位置會盡可能地彼此接近。 對於易受延遲影響的工作負載，鄰近放置群組特別能因應其需求。

## <a name="disaster-recovery-with-proximity-placement-groups"></a>鄰近放置群組的災害復原

在一般情況下，您可能會讓虛擬機器在鄰近放置群組中執行，以避免應用程式的各個層級之間發生網路延遲。 雖然這可讓應用程式達到理想的網路延遲，但您仍應使用 Site Recovery，讓這些應用程式能夠防禦任何區域層級的失敗。 Site Recovery 會將一個區域的資料複寫至另一個 Azure 區域，並且在進行容錯移轉時，在災害復原區域中啟動機器。

## <a name="considerations"></a>考量

- 最佳做法是將虛擬機器容錯移轉/容錯回復至鄰近放置群組。 不過，如果 VM 在容錯移轉/容錯回復期間無法在「鄰近放置」中啟動，容錯移轉/容錯回復仍會執行，但虛擬機器將會建立在鄰近放置群組以外。
-  如果可用性設定組已釘選到鄰近放置群組，且可用性設定組中的 VM 在容錯移轉/容錯回復具有配置限制，則虛擬機器將會建立在可用性設定組和鄰近放置群組以外。
-  非受控磁碟不支援鄰近放置群組的 Site Recovery。

> [!Note]
> Azure Site Recovery 不支援從 Hyper-v 到 Azure 案例的受控磁片容錯回復。 因此，不支援從 Azure 的鄰近放置群組容錯回復至 Hyper-v。

## <a name="prerequisites"></a>Prerequisites

1. 確定您具有 Azure PowerShell Az 模組。 如果您需要安裝或升級 Azure PowerShell，請按照此[安裝和設定 Azure PowerShell 指南](/powershell/azure/install-az-ps)的說明。

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>為鄰近放置群組中的虛擬機器設定 Site Recovery

### <a name="azure-to-azure"></a>Azure 至 Azure

1. [登入](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription)您的帳戶，並設定您的訂用帳戶。
2. 取得您要複寫之虛擬機器的詳細資料，如[這裡](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated)所說明。
3. [建立](./azure-to-azure-powershell.md#create-a-recovery-services-vault)您的復原服務保存庫，並[設定](./azure-to-azure-powershell.md#set-the-vault-context)保存庫內容。
4. 為保存庫做好開始複寫 Azure 虛擬機器的準備。 其作業包括為主要區域和復原區域建立 [Service Fabric](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) 物件。
5. 為主要網狀架構和復原網狀架構[建立](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) Site Recovery 保護容器。
6. [建立](./azure-to-azure-powershell.md#create-a-replication-policy)複寫原則。
7. 使用[這些](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container)步驟和容錯回復的保護容器對應 (如[這裡](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)所述)，在主要和復原保護容器之間建立保護容器對應。
8. 依照[這些](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account)步驟建立快取儲存體帳戶。
9. 依照[這裡](./azure-to-azure-powershell.md#create-network-mappings)的說明建立必要的網路對應。
10. 若要以受控磁碟複寫 Azure 虛擬機器，請使用下列 PowerShell Cmdlet – 

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $recPpg.Id
```
複寫作業啟動成功後，系統就會將虛擬機器資料複寫至復原區域。

複寫程序一開始會先在復原區域中植入虛擬機器複寫磁碟的複本。 這個階段稱為初始複寫階段。

初始複寫完成後，複寫會進入差異同步處理階段。 此時，系統會保護虛擬機器，供您對其執行測試容錯移轉作業。 初始複寫完成後，代表虛擬機器的複寫項目狀態會進入「受保護」狀態。

請藉由取得虛擬機器所對應的受保護複寫項目詳細資料，來監視虛擬機器的複寫狀態和複寫健康情況。 

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. 若要執行測試容錯移轉、驗證和清除測試容錯移轉，請依照[這些](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover)步驟操作。
12. 若要進行容錯移轉，請依照[這裡](./azure-to-azure-powershell.md#fail-over-to-azure)所說明的步驟操作。
13. 若要重新保護並容錯回復到來源區域，請使用下列 PowerShell Cmdlet –

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```
14. 若要停用複寫，請依照[這裡](./azure-to-azure-powershell.md#disable-replication)的步驟操作。

### <a name="vmware-to-azure"></a>VMware 至 Azure

1. 確定您已針對 Azure 的災害復原[準備好內部部署 VMware 伺服器](./vmware-azure-tutorial-prepare-on-premises.md)。
2. 登入您的帳戶，並依照[這裡](./vmware-azure-disaster-recovery-powershell.md#log-into-azure)的指定設定您的訂用帳戶。
3. [設定](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault)復原服務保存庫，並[設定保存庫內容](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context)。
4. [驗證](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration)您的保存庫註冊。
5. [建立](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy)複寫原則。
6. [新增](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) vCenter Server、探索虛擬機器，並[建立](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication)用於複寫的儲存體帳戶。
7. 若要複寫 VMware 虛擬機器，請查看這裡的詳細資料，並執行下列 PowerShell Cmdlet –

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $recPpg.Id
```
8. 您可以使用 Get-ASRReplicationProtectedItem Cmdlet，檢查虛擬機器的複寫狀態和複寫健康情況。

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
9. 依照[這裡](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings)的步驟設定容錯移轉設定。
10. [執行](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover)測試容錯移轉。 
11. 使用[這些](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure)步驟容錯移轉至 Azure。

### <a name="hyper-v-to-azure"></a>Hyper-V 至 Azure

1. 確定您已針對 Azure 的災害復原[準備好內部部署 Hyper-V 伺服器](./hyper-v-prepare-on-premises-tutorial.md)。
2. [登入](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account) Azure。
3. [設定](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault)您的保存庫，並[設定](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context)復原服務保存庫內容。
4. [建立](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) Hyper-V 網站。
5. [安裝](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent)提供者和代理程式。
6. [建立](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy)複寫原則。
7. 使用下列步驟啟用複寫 – 
    
    a. 擷取對應至您想要保護之 VM 的可保護項目，如下所示：

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. 保護 VM。 如果您要保護的 VM 連結了多個磁碟，請使用 OSDiskName 參數指定作業系統磁碟。
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $recPpg.Id
    ```
    c. 在初始複寫後，等待 VM 達到受保護的狀態。 所需時間長短，受到要複寫的資料量和可用的 Azure 上游頻寬等因素影響。 達到受保護的狀態時，作業的 State 和 StateDescription 就會更新，如下所示： 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. 更新復原屬性 (例如 VM 角色大小)，以及在容錯移轉後要連結 VM NIC 的 Azure 網路。

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. 執行測試[容錯移轉](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover)。


## <a name="next-steps"></a>後續步驟

若要執行重新保護和 VMware 至 Azure 的容錯回復，請依照[這裡](./vmware-azure-prepare-failback.md)所述的步驟操作。

若要執行 Hyper-V 至 Azure 的容錯移轉，請依照[這裡](./site-recovery-failover.md)所述的步驟操作，若要執行容錯回復，請依照[這裡](./hyper-v-azure-failback.md)所述的步驟操作。

如需詳細資訊，請參閱[在 Site Recovery 中容錯移轉](site-recovery-failover.md)。
