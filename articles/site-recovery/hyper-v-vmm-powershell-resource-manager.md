---
title: 使用 Azure Site Recovery/PowerShell 設定 Hyper-v （含 VMM）嚴重損壞修復至次要網站
description: 說明如何使用 Azure Site Recovery 和 PowerShell 將 VMM 雲端中 Hyper-V VM 的災害復原設定至次要 VMM 網站。
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: a46bca5c5c55338f8bea7e1ff370f92ce6a2d577
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841041"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>使用 PowerShell (Resource Manager) 將 Hyper-V VM 的災害復原設定至次要網站

本文說明如何自動執行相關步驟，使用 [Azure Site Recovery](site-recovery-overview.md) 將 System Center Virtual Machine Manager 雲端中的 Hyper-V VM 複寫至次要內部部署網站中的 Virtual Machine Manager 雲端。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

- 檢閱[案例架構和元件](hyper-v-vmm-architecture.md)。
- 檢閱所有元件的[支援需求](site-recovery-support-matrix-to-sec-site.md)。
- 請確定 Virtual Machine Manager 伺服器和 Hyper-V 主機符合[支援需求](site-recovery-support-matrix-to-sec-site.md)。
- 確認您要複寫的 VM 符合[複寫的機器支援](site-recovery-support-matrix-to-sec-site.md)

## <a name="prepare-for-network-mapping"></a>準備網路對應

[網路對應](hyper-v-vmm-network-mapping.md)會在來源與目標雲端中的內部部署 Virtual Machine Manager VM 網路之間對應。 對應具有下列功能：

- 在容錯移轉之後將 VM 連線至適當的目標 VM 網路。
- 以最佳方式將複本 VM 放在目標 Hyper-V 主機伺服器上。
- 如果您未設定網路對應，複本 VM 將不會在容錯移轉之後連線到 VM 網路。

根據下列指示準備 Virtual Machine Manager：

- 確定您的來源和目標 Virtual Machine Manager 伺服器上有 [Virtual Machine Manager 邏輯網路](https://docs.microsoft.com/system-center/vmm/network-logical)：
  - 來源伺服器上的邏輯網路應該與 Hyper-V 主機所在的來源雲端相關聯。
  - 目標伺服器上的邏輯網路應該與目標雲端相關聯。
- 確定您的來源和目標 Virtual Machine Manager 伺服器上有 [ 邏輯網路](https://docs.microsoft.com/system-center/vmm/network-virtual)。 VM 網路應連結至每個位置的邏輯網路。
- 將來源 Hyper-V 主機上的 VM 連線至來源 VM 網路。

## <a name="prepare-for-powershell"></a>準備 PowerShell

確定 Azure PowerShell 已經準備就緒：

- 如果您已經使用 PowerShell，請升級至 0.8.10 版或更新版本。 [深入了解](/powershell/azureps-cmdlets-docs)如何設定 PowerShell。
- 在您安裝並設定 PowerShell 後，請檢閱[服務 Cmdlet](/powershell/azure/overview)。
- 若要深入了解如何在 PowerShell 中使用參數值、輸入和輸出，請閱讀[開始使用](/powershell/azure/get-started-azureps)指南。

## <a name="set-up-a-subscription"></a>設定訂用帳戶

1. 從 PowerShell，登入您的 Azure 帳戶。

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. 使用訂用帳戶識別碼擷取您的訂用帳戶清單。 記下要建立復原服務保存庫的訂用帳戶識別碼。

   ```azurepowershell
   Get-AzSubscription
   ```

1. 進行訂用帳戶的保存庫設定。

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

1. 如果您沒有 Azure Resource Manager 資源群組，請建立一個。

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. 建立新的復原服務保存庫。 將保存庫物件儲存在稍後會用到的變數中。

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   使用 `Get-AzRecoveryServicesVault` Cmdlet 建立保存庫物件之後，您就可以將它取出。

## <a name="set-the-vault-context"></a>設定保存庫內容

1. 擷取現有的保存庫。

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. 設定保存庫內容。

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>安裝 Site Recovery 提供者

1. 在 Virtual Machine Manager 機器上，執行下列命令來建立目錄：

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. 使用下載的提供者安裝檔案將檔案解壓縮。

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. 安裝提供者，並等待安裝完成。

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. 在保存庫中註冊伺服器。

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>建立複寫原則並產生關聯

1. 建立複寫原則 (在此案例中，是針對 Hyper-V 2012 R2 建立)，如下所示︰

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > Virtual Machine Manager 雲端可以包含執行不同 Windows Server 版本的 Hyper-V 主機，但複寫原則僅適用於特定版本的作業系統。 如果您有執行不同作業系統的主機，請針對每個系統建立不同的複寫原則。 例如︰如果您有五部主機在 Windows Server 2012 上執行，有三部主機在 Windows Server 2012 R2 上執行，請建立兩個複寫原則。 您可以為每種類型的作業系統各建立一個。

1. 擷取主要保護容器 (主要 Virtual Machine Manager 雲端) 和復原保護容器 (復原 Virtual Machine Manager 雲端)。

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. 使用好記的名稱，擷取您所建立的複寫原則。

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. 開始建立保護容器 (Virtual Machine Manager 雲端) 與複寫原則的關聯。

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. 等候原則關聯工作完成。 若要檢查工作是否完成，請使用下列 PowerShell 程式碼片段：

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. 完成工作處理之後，執行下列命令：

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

若要檢查作業是否完成，請執行[監視活動](#monitor-activity)中的步驟。

##  <a name="configure-network-mapping"></a>設定網路對應

1. 此命令擷取目前保存庫的伺服器。 命令會將 Site Recovery 伺服器儲存在 `$Servers` 陣列變數中。

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. 執行此命令來擷取來源 Virtual Machine Manager 伺服器和目標 Virtual Machine Manager 伺服器的網路。

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

   > [!NOTE]
   > 來源 Virtual Machine Manager 伺服器在伺服器陣列中可以是第一部或第二部伺服器。 檢查 Virtual Machine Manager 伺服器名稱，並適當地擷取網路。

1. 此 Cmdlet 會在主要網路與復原網路之間建立對應。 它會指定主要網路做為 `$PrimaryNetworks`的第一個元素。 它會將復原網路指定為 `$RecoveryNetworks`的第一個元素。

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>為 VM 啟用保護

正確設定伺服器、雲端和網路後，就可以對雲端中的 VM 啟用保護。

1. 若要啟用保護，請執行下列命令以擷取保護容器：

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. 依照下列方式取得保護實體 (VM)：

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. 啟用 VM 複寫。

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> 如果您想要複寫至 Azure 中已啟用 CMK 的受控磁片，請使用 Az PowerShell 3.3.0 to do 執行下列步驟：
>
> 1. 藉由更新 VM 屬性來啟用容錯移轉至受控磁片
> 1. 使用 `Get-AzRecoveryServicesAsrReplicationProtectedItem` Cmdlet 來提取受保護專案之每個磁片的磁片識別碼
> 1. 使用 `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` Cmdlet 建立字典物件，以包含磁片識別碼與磁片加密集的對應。 這些磁片加密集是由您在目的地區域中預先建立的。
> 1. 藉由在**DiskIdToDiskEncryptionSetMap**參數中傳遞 dictionary 物件，使用 `Set-AzRecoveryServicesAsrReplicationProtectedItem` Cmdlet 來更新 VM 屬性。

## <a name="run-a-test-failover"></a>執行測試容錯移轉

若要測試您的部署，請針對單一虛擬機器執行測試容錯移轉。 您也可以建立包含多個 VM 的復原計劃，並針對計劃執行容錯移轉。 測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。

1. 擷取作為 VM 容錯移轉目標的 VM。

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. 執行測試容錯移轉。

   針對單一 VM：

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   針對復原計劃：

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

若要檢查作業是否完成，請執行[監視活動](#monitor-activity)中的步驟。

## <a name="run-planned-and-unplanned-failovers"></a>執行計劃性或非計劃性容錯移轉

1. 執行計劃性容錯移轉。

   針對單一 VM：

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   針對復原計劃：

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. 執行非計劃性容錯移轉。

   針對單一 VM：

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   針對復原計劃：

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>監視活動

使用下列命令監視容錯移轉活動。 等候作業之間的處理完成。

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>後續步驟

[深入了解](/powershell/module/az.recoveryservices)使用 Resource Manager PowerShell Cmdlet 進行 Site Recovery 的相關資訊。
