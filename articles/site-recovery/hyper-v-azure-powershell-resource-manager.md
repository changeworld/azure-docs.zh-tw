---
title: 使用 Azure Site Recovery 和 PowerShell 的 hyper-v VM 嚴重損壞修復
description: 使用 PowerShell 和 Azure Resource Manager，透過 Azure Site Recovery 服務自動將 Hyper-V VM 災害復原至 Azure。
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4140a0b22f7ca8164d50cf60fe57c861f826eb4
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86132510"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>針對 Hyper-V VM，使用 PowerShell 和 Azure Resource Manager 設定至 Azure 的災害復原

[Azure Site Recovery](site-recovery-overview.md) 可藉由協調 Azure 虛擬機器 (VM)、內部部署 VM 與實體伺服器的複寫、容錯移轉及復原，為您的商務持續性與嚴重損壞修復 (BCDR) 策略做出貢獻。

本文說明如何搭配 Azure Resource Manager 使用 Windows PowerShell，將 Hyper-V VM 複寫到 Azure。 本文使用的範例示範如何將 Hyper-V 主機上執行的單一 VM 複寫到 Azure。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 提供 Cmdlet，讓您使用 Windows PowerShell 管理 Azure。 Azure PowerShell for Azure Resource Manager 提供的 Site Recovery PowerShell Cmdlet 可讓您在 Azure 中保護與復原伺服器。

您不需要是 PowerShell 專家也能使用本文，但您必須了解模組、Cmdlet 和工作階段等基本概念。 如需詳細資訊，請參閱[PowerShell 檔](/powershell)和搭配[Azure Resource Manager 使用 Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)。

> [!NOTE]
> 雲端方案提供者 (CSP) 計畫的 Microsoft 合作夥伴，可以在其客戶各自的 CSP 訂用帳戶 (租用戶訂用帳戶) 設定和管理客戶的伺服器保護。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

確認您已備妥這些必要條件：

- [Microsoft Azure](https://azure.microsoft.com/) 帳戶。 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 此外，您可以參閱 [Azure Site Recovery 管理員價格](https://azure.microsoft.com/pricing/details/site-recovery/)。
- Azure PowerShell。 如需有關此版本及如何安裝的詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

此外，本文所述的特定範例有下列先決條件：

- 執行 Windows Server 2012 R2 或 Microsoft Hyper-V Server 2012 R2 且包含一或多部 VM 的 Hyper-V 主機。 Hyper-V 伺服器應該直接或透過 Proxy 連接到網際網路。
- 您想要複寫的 VM 都應該符合[這些必要條件](hyper-v-azure-support-matrix.md#replicated-vms)。

## <a name="step-1-sign-in-to-your-azure-account"></a>步驟 1：登入您的 Azure 帳戶

1. 開啟 PowerShell 主控台並執行這個命令，登入您的 Azure 帳戶。 此 Cmdlet 會顯示一個網頁，提示您輸入您的帳號憑證： `Connect-AzAccount` 。
   - 或者，您可以 `Connect-AzAccount` 使用**Credential**參數，將您的帳號憑證納入 Cmdlet 中做為參數。
   - 如果您是代表租使用者工作的 CSP 合作夥伴，請使用其 tenantID 或租使用者的主功能變數名稱稱，將客戶指定為租使用者。 例如： `Connect-AzAccount -Tenant "fabrikam.com"`
1. 由於一個帳戶可以有多個訂用帳戶，因此您必須將要使用的訂用帳戶與帳戶建立關聯：

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. 使用下列命令確認您的訂用帳戶已註冊使用 Azure 復原服務提供者和 Site Recovery：

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. 確認 **RegistrationState** 在命令輸出中設為 [已註冊]****，您可以繼續執行步驟 2。 如果未設定，請執行下列命令來註冊訂用帳戶中遺漏的提供者：

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. 確認提供者成功使用下列命令註冊：

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>步驟 2：設定保存庫

1. 建立將在其中建立保存庫的 Azure Resource Manager 資源群組，或使用現有的資源群組。 建立新的資源群組，如下所示。 `$ResourceGroupName`變數包含您想要建立的資源組名，而 $Geo 變數包含要在其中建立資源群組的 Azure 區域（例如，「巴西南部」）。

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. 若要取得訂用帳戶中的資源群組清單，請執行 `Get-AzResourceGroup` Cmdlet。
1. 建立新的 Azure 復原服務保存庫，如下所示：

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

您可以使用 Cmdlet 來捕獲現有保存庫的清單 `Get-AzRecoveryServicesVault` 。

## <a name="step-3-set-the-recovery-services-vault-context"></a>步驟 3：設定復原服務保存庫內容

設定保存庫內容，如下所示：

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>步驟 4：建立 Hyper-V 網站

1. 建立新的 Hyper-V 站台，如下所示：

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. 這個 Cmdlet 會啟動 Site Recovery 作業來建立網站，並傳回 Site Recovery 作業物件。 等待作業完成，並確認作業已成功完成。
1. 使用 `Get-AzRecoveryServicesAsrJob` Cmdlet 來取出工作物件，並檢查作業的目前狀態。
1. 產生並下載網站的註冊金鑰，如下所示：

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. 將下載的金鑰複製到 Hyper-V 主機。 您需要金鑰向網站註冊 Hyper-V 主機。

## <a name="step-5-install-the-provider-and-agent"></a>步驟 5：安裝提供者和代理程式

1. 從[Microsoft](https://aka.ms/downloaddra)下載最新版提供者的安裝程式。
1. 在 Hyper-v 主機上執行安裝程式。
1. 在安裝結尾繼續註冊步驟。
1. 當系統提示時，請提供下載金鑰，並完成 Hyper-V 主機註冊。
1. 確認 Hyper-V 主機向網站註冊，如下所示：

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

如果您執行的是 Hyper-V 核心伺服器，請下載安裝檔案並執行下列步驟：

1. 執行下列命令，將檔案從_AzureSiteRecoveryProvider.exe_解壓縮至本機目錄：

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. 執行下列命令：

   ```console
   .\setupdr.exe /i
   ```

   結果會記錄到 _%ProgramData%\ASRLogs\DRASetupWizard.log_。

1. 執行下列命令以註冊伺服器：

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>步驟 6：建立複寫原則

開始之前，指定的儲存體帳戶應該位於與保存庫相同的 Azure 區域中，而且應該啟用異地複寫。

1. 建立複寫原則，如下所示︰

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. 請檢查傳回的作業，以確保複寫原則建立成功。

1. 擷取對應至網站的保護容器，如下所示：

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. 建立保護容器與複寫原則的關聯，如下所示：

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. 等到關聯工作順利完成。

1. 取出保護容器對應。

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>步驟 7：啟用 VM 保護

1. 擷取對應至您想要保護之 VM 的可保護項目，如下所示：

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. 保護 VM。 如果您要保護的 VM 已連接一個以上的磁片，請使用**OSDiskName**參數指定作業系統磁片。

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. 在初始複寫後，等待 VM 達到受保護的狀態。 所需時間長短，受到要複寫的資料量和可用的 Azure 上游頻寬等因素影響。 達到受保護的狀態時，作業的 State 和 StateDescription 就會更新，如下所示：

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. 更新復原屬性 (例如 VM 角色大小)，以及在容錯移轉後要連結 VM NIC 的 Azure 網路。

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> 如果您想要複寫至 Azure 中已啟用 CMK 的受控磁片，請使用 Az PowerShell 3.3.0 to do 執行下列步驟：
>
> 1. 藉由更新 VM 屬性來啟用容錯移轉至受控磁片
> 1. 使用 `Get-AzRecoveryServicesAsrReplicationProtectedItem` Cmdlet 來提取受保護專案之每個磁片的磁片識別碼
> 1. 使用 Cmdlet 建立字典物件， `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` 以包含磁片識別碼與磁片加密集的對應。 這些磁片加密集是由您在目的地區域中預先建立的。
> 1. 藉 `Set-AzRecoveryServicesAsrReplicationProtectedItem` 由在**DiskIdToDiskEncryptionSetMap**參數中傳遞 dictionary 物件，使用 Cmdlet 更新 VM 屬性。

## <a name="step-8-run-a-test-failover"></a>步驟 8：執行測試容錯移轉

1. 執行測試容錯移轉，如下所示：

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. 確認已在 Azure 中建立測試 VM。 在 Azure 中建立測試 VM 之後，測試容錯移轉作業已經暫停。
1. 若要清除和完成測試容錯移轉，請執行：

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>後續步驟

[深入了解](/powershell/module/az.recoveryservices) 使用 Azure Resource Manager PowerShell Cmdlet 進行 Azure Site Recovery 的相關資訊。
