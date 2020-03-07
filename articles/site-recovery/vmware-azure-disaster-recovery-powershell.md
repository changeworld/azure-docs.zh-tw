---
title: 在 Azure Site 復原中使用 PowerShell 設定 VMware 嚴重損壞修復
description: 了解如何在 Azure Site Recovery 中使用 PowerShell 設定針對 Azure 的複寫和容錯移轉，以對 VMware VM 進行災害復原。
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: d2dfaab3d01ea29b0f9ecba1e9d748415bed2edc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391722"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>使用 PowerShell 設定 VMware VM 至 Azure 的災害復原

在本文中，您會看到如何使用 Azure PowerShell 將 VMware 虛擬機器複寫和容錯移轉到 Azure。

您會了解如何：

> [!div class="checklist"]
> - 建立復原服務保存庫並設定保存庫內容。
> - 驗證保存庫中的伺服器註冊。
> - 設定複寫，包括複寫原則。 新增 vCenter 伺服器並探索 VM。
> - 新增 vCenter 伺服器並探索
> - 建立儲存體帳戶以保存複寫記錄或資料，並複寫 Vm。
> - 執行容錯移轉。 設定容錯移轉設定，執行複寫虛擬機器的設定。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

開始之前：

- 請確定您了解[情節架構和元件](vmware-azure-architecture.md)。
- 檢閱所有元件的[支援需求](site-recovery-support-matrix-to-azure.md)。
- 您有 Azure PowerShell `Az` 模組。 如果您需要安裝或升級 Azure PowerShell，請按照此[安裝和設定 Azure PowerShell 指南](/powershell/azure/install-az-ps)的說明。

## <a name="log-into-azure"></a>登入 Azure

使用 Disconnect-azaccount Cmdlet 登入您的 Azure 訂用帳戶：

```azurepowershell
Connect-AzAccount
```
選取作為 VMware 虛擬機器複寫目的地的 Azure 訂用帳戶。 使用 Get-azsubscription Cmdlet 來取得您有權存取的 Azure 訂用帳戶清單。 使用 Get-azsubscription 指令，選取要使用的 Azure 訂用帳戶。

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>設定復原服務保存庫

1. 建立要在其中建立復原服務保存庫的資源群組。 在下列範例中，資源群組名為 VMwareDRtoAzurePS，而且是在東亞地區建立。

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. 建立復原服務保存庫。 在下列範例中，復原服務保存庫名為 VMwareDRToAzurePs，而且是在東亞地區建立，存在於上一個步驟中建立的資源群組。

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. 下載保存庫的保存庫註冊金鑰。 保存庫註冊金鑰用來將內部部署設定伺服器註冊至此保存庫。 註冊是設定伺服器軟體安裝程序的一環。

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. 使用下載的保存庫註冊金鑰，遵循下文提供的步驟，完成設定伺服器的安裝和註冊。
   - [選擇您的保護目標](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [設定來源環境](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>設定保存庫內容

使用 Set-ASRVaultContext Cmdlet 設定保存庫內容。 設定後，會在所選保存庫的內容中執行 PowerShell 工作階段中的後續 Azure Site Recovery 作業。

> [!TIP]
> Azure Site Recovery PowerShell 模組（Az. Azurerm.recoveryservices 模組）為大部分的 Cmdlet 提供容易使用的別名。 模組中的 Cmdlet 會採用\<作業的形式， *>**AzRecoveryServicesAsr**\<物件 >* ，並具有對等的別名，其採用\<作業 > *-**ASR**\<物件 >* 的格式。 您可以取代 Cmdlet 別名以方便使用。

在下列範例中，來自 $vault 變數的保存庫詳細資料用於指定 PowerShell 工作階段的保存庫內容。

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

除了 ASRVaultCoNtext 指令程式以外，您也可以使用 AzRecoveryServicesAsrVaultSettingsFile Cmdlet 來設定保存庫內容。 指定保存庫註冊金鑰檔案所在的路徑，做為 AzRecoveryServicesAsrVaultSettingsFile Cmdlet 的-path 參數。 例如，

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
本文的後續小節假設已設定 Azure Site Recovery 作業的保存庫內容。

## <a name="validate-vault-registration"></a>驗證保存庫註冊

在此範例中，我們有下列項目：

- 設定伺服器 (**ConfigurationServer**) 已註冊到此保存庫。
- 另一個處理序伺服器 **ScaleOut-ProcessServer** 已註冊到 *ConfigurationServer*
- 已在組態伺服器上設定帳戶 (**vCenter_account**、**WindowsAccount**、**LinuxAccount**)。 這些帳戶會用來新增 vCenter Server 以探索虛擬機器，並在要複寫的 Windows 和 Linux 伺服器上推入安裝行動服務軟體。

1. 已註冊的組態伺服器在 Site Recovery 中會呈現為網狀架構物件。 取得清單保存庫中的網狀架構物件清單，並識別組態伺服器。

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. 識別可用來複寫機器的處理序伺服器。

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   從以上的輸出，***$ProcessServers[0]*** 對應至 *ScaleOut-ProcessServer*，***$ProcessServers[1]*** 對應至 *ConfigurationServer* 上的處理序伺服器角色

3. 識別已在設定伺服器上設定的帳戶。

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   從以上的輸出，***$AccountHandles[0]*** 對應至帳戶 *vCenter_account*，***$AccountHandles[1]*** 對應至帳戶 *WindowsAccount*，***$AccountHandles[2]*** 對應至帳戶 *LinuxAccount*

## <a name="create-a-replication-policy"></a>建立複寫原則

在此步驟中，會建立兩個複寫原則。 一個原則會將 VMware 虛擬機器複寫到 Azure，另一個原則會將在 Azure 中執行且已容錯移轉的虛擬機器複寫回內部部署 VMware 網站。

> [!NOTE]
> 大部分的 Azure Site Recovery 作業會以非同步方式執行。 您開始作業時，會提交 Azure Site Recovery 作業，並傳回作業追蹤物件。 這個提交追蹤物件可用來監視作業的狀態。

1. 建立名為 *ReplicationPolicy* 的複寫原則，使用指定的屬性將 VMware 虛擬機器複寫至 Azure。

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. 建立複寫原則，用於從 Azure 到內部部署 VMware 網站的容錯回復。

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   使用 *$Job_FailbackPolicyCreate* 中的作業詳細資料追蹤作業，直到完成為止。

   * 建立保護容器對應來對應複寫原則與設定伺服器。

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>新增 vCenter 伺服器並探索 VM

依照 IP 位址或主機名稱新增 vCenter Server。 **-port** 參數會指定連接的 vCenter Server 連接埠， **-Name** 參數會指定用於 vCenter Server 的易記名稱， **-Account** 參數會指定設定伺服器上的帳戶控點，用來探索 vCenter Server 管理的虛擬機器。

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>建立用於複寫的儲存體帳戶

**若要寫入受控磁片，請使用[Powershell Az. azurerm.recoveryservices module 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 。** 它只需要建立記錄儲存體帳戶。 建議使用標準帳戶類型並 LRS 冗余，因為它是用來只儲存臨時記錄。 請確定儲存體帳戶建立在與保存庫相同的 Azure 區域中。

如果您使用的 Azurerm.recoveryservices 模組版本早于2.0.0，請使用下列步驟來建立儲存體帳戶。 稍後會建立這些儲存體帳戶來複寫虛擬機器。 確定已在保存庫所在的同一個 Azure 區域中建立儲存體帳戶。 如果您計畫使用現有的儲存體帳戶進行複寫，您可以略過此步驟。

> [!NOTE]
> 將內部部署虛擬機器複寫至進階儲存體帳戶時，您需要指定額外的標準儲存體帳戶 (記錄檔儲存體帳戶)。 記錄儲存體帳戶將保留複寫記錄做為中繼存放區，直到可在進階儲存體目標上套用記錄為止。
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>複寫 VMware VM

從 vCenter 伺服器探索虛擬機器大約需要 15 到 20 分鐘。 進行探索後，對於每個探索到的虛擬機器，將在 Azure Site Recovery 中建立可保護的項目物件。 在此步驟中，將三個探索到的虛擬機器複寫到上一個步驟中建立的 Azure 儲存體帳戶。

您需要下列詳細資料，才能保護探索到的虛擬機器：

* 將複寫的可保護項目。
* 要複寫虛擬機器的儲存體帳戶（僅限複寫至儲存體帳戶時）。 
* 需要記錄儲存體，才能保護虛擬機器對 premium 儲存體帳戶或受控磁片。
* 將用於複寫的處理序伺服器。 已取得可用進程伺服器的清單，並將其儲存在 ***$ProcessServers [0]***  *（向外延展-scaleout-processserver）* 和 ***$ProcessServers [1]*** *（ConfigurationServer）* 變數中。
* 用於將行動服務軟體推入安裝於機器的帳戶。 已擷取可用帳戶清單，並儲存於 ***$AccountHandles*** 變數中。
* 對於複寫所用的複寫原則使用的保護容器對應。
* 必須在容錯移轉時建立虛擬機器的資源群組。
* 或者，應連接容錯移轉虛擬機器的 Azure 虛擬網路和子網路。

現在使用此表格中指定的設定複寫下列虛擬機器


|虛擬機器  |處理序伺服器        |儲存體帳戶              |記錄檔儲存體帳戶  |Policy(Windows Intune 說明：原則)           |行動服務安裝的帳戶|目標資源群組  | 目標虛擬網路  |目標子網路  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |N/A| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| N/A                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

啟用複寫作業成功完成後，就會對於虛擬機器開始初始複寫。 初始複寫可能需要一些時間，取決於複寫的資料量和複寫可用的頻寬。 初始複寫完成後，虛擬機器將變更為受保護的狀態。 虛擬機器達到受保護的狀態後，您可以執行虛擬機器的測試容錯移轉，並將虛擬機器加入至復原計劃等等。

您可以使用 Get-ASRReplicationProtectedItem Cmdlet，檢查虛擬機器的複寫狀態和複寫健康情況。

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>設定容錯移轉設定

可以使用 Set-ASRReplicationProtectedItem Cmdlet 更新受保護的機器所用的容錯移轉設定。 可以透過這個 Cmdlet 更新的部份設定包括：
* 將在容錯移轉時建立的虛擬機器名稱
* 將在容錯移轉時建立的虛擬機器大小
* 虛擬機器的 NIC 應在容錯移轉時連接的 Azure 虛擬網路和子網路
* 容錯移轉至受控磁碟
* 套用 Azure Hybrid Use Benefit
* 從將在容錯移轉時指派至虛擬機器的目標虛擬網路中指派靜態 IP 位址。

在此範例中，我們更新將在虛擬機器 *Win2K12VM1* 的容錯移轉時建立的虛擬機器 VM 大小，並指定虛擬機器在容錯移轉時使用受控磁碟。

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>執行測試容錯移轉

1. 執行 DR 演練 (測試容錯移轉)，如下所示：

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. 測試容錯移轉作業順利完成時，您會發現已經在 Azure 中建立名稱後面加上 *"-Test"* (在此案例中，即為 Win2K12VM1-Test) 的虛擬機器。
3. 您現在可以連接到測試容錯移轉虛擬機器，並驗證測試容錯移轉。
4. 使用 Start-ASRTestFailoverCleanupJob Cmdlet 清除測試容錯移轉。 此作業會刪除在測試容錯移轉作業的過程中建立的虛擬機器。

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>容錯移轉至 Azure

在此步驟中，我們會將虛擬機器 Win2K12VM1 容錯移轉至特定復原點。

1. 取得可用於容錯移轉的復原點清單：
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. 成功容錯移轉後，您可以認可容錯移轉作業，並設定從 Azure 複寫回內部部署 VMware 網站的反向複寫。

## <a name="next-steps"></a>後續步驟
瞭解如何使用[Azure Site Recovery PowerShell 參考](https://docs.microsoft.com/powershell/module/Az.RecoveryServices)，將更多工做自動化。
