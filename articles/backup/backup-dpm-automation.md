---
title: 使用 PowerShell 備份 DPM 工作負載
description: 了解如何使用 PowerShell 部署和管理 Data Protection Manager (DPM) 的 Azure 備份
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 176cbffe5152462055c4ffdb2367cf9c0ab97c1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968292"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>使用 PowerShell 部署和管理 Data Protection Manager (DPM) 伺服器的 Azure 備份

本文說明如何使用 PowerShell 來設定 DPM 伺服器上的 Azure 備份以及管理備份和復原。

## <a name="setting-up-the-powershell-environment"></a>設定 PowerShell 環境

在可以使用 PowerShell 來管理 Data Protection Manager 的 Azure 備份之前，您必須在 PowerShell 中具備適當的環境。 在 PowerShell 工作階段開始時，請確定您執行下列命令來匯入正確的模組並可讓您正確地參考 DPM Cmdlet：

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>設定和註冊

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要開始，請[下載最新 Azure PowerShell](/powershell/azure/install-az-ps)。

PowerShell 可以自動化下列設定和註冊工作：

* 建立復原服務保存庫
* 安裝 Azure 備份代理程式
* 向 Azure 備份服務進行註冊
* 網路設定
* 加密設定

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

下列步驟將引導您完成建立復原服務保存庫。 復原服務保存庫不同於備份保存庫。

1. 如果您是第一次使用 Azure 備份，就必須使用 **>register-azresourceprovider** 指令程式，向您的訂用帳戶註冊 Azure 復原服務提供者。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. 復原服務保存庫是 ARM 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或建立一個新的群組。 建立新的資源群組時，請指定資源群組的名稱和位置。

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. 使用 **New-AzRecoveryServicesVault** Cmdlet 來建立新的保存庫。 請務必為保存庫指定與用於資源群組相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. 指定要使用的儲存空間備援類型。 您可以使用 [本機冗余儲存體 (LRS) ](../storage/common/storage-redundancy.md#locally-redundant-storage)、 [異地冗余儲存體 (GRS) ](../storage/common/storage-redundancy.md#geo-redundant-storage)或 [區域冗余儲存體 (ZRS) ](../storage/common/storage-redundancy.md#zone-redundant-storage)。 下列範例顯示*testVault*設定為**異地備援**的**BackupStorageRedundancy**選項。

   > [!TIP]
   > 許多 Azure 備份 Cmdlet 都需要將復原服務保存庫物件當做輸入。 基於這個理由，將備份復原服務保存庫物件儲存在變數中會是方便的做法。
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

使用 **Get-AzRecoveryServicesVault** 來檢視目前訂用帳戶中所有保存庫的清單。 您可以使用此命令來檢查是否已建立新的保存庫，或查看訂用帳戶中有哪些保存庫可用。

執行命令 Get-AzRecoveryServicesVault，會列出訂用帳戶中的所有保存庫。

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>在 DPM 伺服器上安裝 Azure 備份代理程式

在安裝 Azure 備份代理程式之前，您必須在 Windows Server 上下載並提供安裝程式。 您可以從 [Microsoft 下載中心](https://aka.ms/azurebackup_agent) 或從復原服務保存庫的 [儀表板] 頁面取得最新版的安裝程式。 請將安裝程式儲存至容易存取的位置，例如 `C:\Downloads\*`。

若要安裝代理程式，請在 **DPM 伺服器**上已提升權限的 PowerShell 主控台中執行下列命令：

```powershell
MARSAgentInstaller.exe /q
```

這會以所有預設選項安裝代理程式。 安裝作業會在背景中進行幾分鐘。 如果您未指定 */nu* 選項， **Windows Update** 視窗會在安裝結束時開啟，以檢查是否有任何更新。

代理程式會顯示在已安裝的程式清單中。 若要查看已安裝的程式清單，請移至 [控制台] > [程式] > [程式和功能]。

![已安裝代理程式](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>安裝選項

若要查看所有可透過命令列執行的選項，請使用下列命令：

```powershell
MARSAgentInstaller.exe /?
```

可用的選項包括：

| 選項 | 詳細資料 | 預設 |
| --- | --- | --- |
| /q |無訊息安裝 |- |
| /p:"location" |Azure 備份代理程式的安裝資料夾路徑。 |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"location" |Azure 備份代理程式的快取資料夾路徑。 |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |選擇加入 Microsoft Update |- |
| /nu |安裝完成後不要檢查更新 |- |
| /d |解除安裝 Microsoft Azure 復原服務代理程式 |- |
| /ph |Proxy 主機位址 |- |
| /po |Proxy 主機連接埠號碼 |- |
| /pu |Proxy 主機使用者名稱 |- |
| /pw |Proxy 密碼 |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>向復原服務保存庫註冊 DPM

建立復原服務保存庫之後，請下載最新版本的代理程式和保存庫認證，並將它們儲存在方便的位置 (如 C:\Downloads)。

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

在 DPM 伺服器上，執行 [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) Cmdlet 以向保存庫註冊電腦。

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>初始組態設定

一旦向復原服務保存庫註冊 DPM 伺服器，就會使用預設的訂用帳戶設定開始。 這些訂用帳戶設定包括網路、加密和臨時區域。 若要變更訂用帳戶設定，您需要先使用 [Get-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting) Cmdlet 取得現有 (預設) 設定上的控制代碼：

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

所有修改都會對此本機 PowerShell 物件 ```$setting``` 進行，然後使用 [Set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) Cmdlet 將完整物件認可到 DPM 和 Azure 備份以加以儲存。 您必須使用 ```–Commit``` 旗標，以確保會保存所做的變更。 除非已認可，否則 Azure 備份將不會套用及使用這些設定。

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>網路功能

如果 DPM 機器對在網際網路上的 Azure 備份服務的連線是透過 Proxy 伺服器，則應該提供 Proxy 伺服器設定，備份才能成功。 這是使用 ```-ProxyServer```、```-ProxyPort```、```-ProxyUsername``` 及 ```ProxyPassword``` 參數搭配 [Set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) Cmdlet 來完成。 在此範例中，沒有 proxy 伺服器，因此我們要明確地清除任何 proxy 相關資訊。

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

您也可以針對給定的一組當週天數，使用 ```-WorkHourBandwidth``` 和 ```-NonWorkHourBandwidth``` 的選項來控制頻寬使用情形。 在此範例中，我們不會設定任何節流。

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>設定臨時區域

在 DPM 伺服器上執行的 Azure 備份代理程式需要暫存儲存體，以供存放從雲端還原的資料 (本機臨時區域)。 使用 [Set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) Cmdlet 和 ```-StagingAreaPath``` 參數來設定臨時區域。

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

在上述範例中，臨時區域將在 PowerShell 物件 ```$setting``` 中設定為 *C:\StagingArea*。 請確保指定的資料夾已經存在，否則訂用帳戶設定的最終認可將會失敗。

### <a name="encryption-settings"></a>加密設定

傳送至 Azure 備份的備份資料會進行加密來保護資料的機密性。 加密複雜密碼是在還原時用來解密資料的「密碼」。 設定之後，請務必確保這項資訊的安全安全。

在下面的範例中，第一個命令會將字串 ```passphrase123456789``` 轉換為安全字串，並將安全字串指派給名為 ```$Passphrase``` 的變數。 第二個命令會將 ```$Passphrase``` 中的安全字串設定為加密備份的密碼。

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> 設定之後，請確保複雜密碼資訊安全且安全。 若沒有此複雜密碼，您將無法從 Azure 還原資料。
>
>

此時，您應該已對 ```$setting``` 物件進行所有必要的變更。 記得要認可變更。

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>保護 Azure 備份的資料

在本節中，您會將實際執行伺服器新增至 DPM，然後將資料保護至本機 DPM 儲存體，然後再 Azure 備份。 在範例中，我們將示範如何備份檔案和資料夾。 您可以輕鬆地運用同樣的觀念，來備份任何 DPM 支援的資料來源。 所有 DPM 備份皆受到保護群組 (PG) 所控管，並且由四個部分構成：

1. **群組成員** 是您要在相同的保護群組中保護的所有可保護物件的清單 (在 DPM 中也稱為 *資料來源* )。 比方說，您可能想要保護一個保護群組的實際執行 VM 與另一個保護群組中的 SQL Server 資料庫，因為它們可能會有不同的備份需求。 在可以備份實際執行伺服器上的資料來源之前，您必須先確定 DPM 代理程式已安裝在伺服器上並受到 DPM 管理。 請遵循 [安裝 DPM 代理程式](/system-center/dpm/deploy-dpm-protection-agent) 的步驟，並將其連結至適當的 DPM 伺服器。
2. **資料保護方法** 指定目標備份位置 - 磁帶、磁碟和雲端。 在我們的範例中，我們會保護本機磁片和雲端的資料。
3. **備份排程** 可指定何時需要進行備份，以及應該在 DPM 伺服器和實際執行伺服器之間同步處理資料的頻率。
4. **保留排程** 可指定要在 Azure 中保留復原點多久時間。

### <a name="creating-a-protection-group"></a>建立保護群組

從使用 [Add-DPMProtectionGroup](/powershell/module/dataprotectionmanager/new-dpmprotectiongroup) Cmdlet 來建立新的保護群組開始。

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

以上 Cmdlet 會建立名為 *ProtectGroup01*的保護群組。 也可以修改稍後現有的保護群組，以加入備份至 Azure 雲端。 不過，若要對保護群組 - 新的或現有的- 進行任何變更，我們需要使用 *Get-DPMModifiableProtectionGroup* Cmdlet 來取得 [modifiable](/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup) 物件上的控制代碼。

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>將群組成員加入至保護群組

每個 DPM 代理程式都知道所安裝之伺服器上的資料來源清單。 若要將資料來源加入至保護群組，DPM 代理程式必須先將資料來源清單傳回到 DPM 伺服器。 然後會選取一或多個資料來源，並加入至保護群組。 達成此動作所需的 PowerShell 步驟包括：

1. 擷取透過 DPM 代理程式由 DPM 管理的所有伺服器清單。
2. 選擇特定伺服器。
3. 擷取伺服器上所有資料來源的清單。
4. 選擇一或多個資料來源，並將它們加入至保護群組

安裝 DPM 代理程式且受 DPM 伺服器管理所在的伺服器清單是使用 [Get-DPMProductionServer](/powershell/module/dataprotectionmanager/get-dpmproductionserver) Cmdlet 取得。 在此範例中，我們將篩選並只使用名稱 *productionserver01* 進行備份來設定 PowerShell。

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

現在使用 [Get-DPMDatasource](/powershell/module/dataprotectionmanager/get-dpmdatasource) Cmdlet 擷取 ```$server``` 上的資料來源清單。 在此範例中，我們 `D:\` 會篩選要設定備份的磁片區。 然後此資料來源會使用 [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) Cmdlet 新增至保護群組。 記得使用 *「可修改的」* 保護群組物件 ```$MPG``` 來進行新增。

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

您可以視需要多次重複此步驟，直到將所有選擇的資料來源新增至保護群組為止。 您也可以從只有一個資料來源開始，並完成建立保護群組的工作流程，然後稍後將更多的資料來源加入至保護群組。

### <a name="selecting-the-data-protection-method"></a>選取資料保護方式

資料來源加入至保護群組之後，下一步是使用 [Set-DPMProtectionType](/powershell/module/dataprotectionmanager/set-dpmprotectiontype) Cmdlet 指定保護方法。 此範例中，將為本機磁碟和雲端備份設定保護群組。 您也必須使用 [Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) Cmdlet 搭配 -Online 旗標，指定您想要在雲端中保護的資料來源。

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>設定保留範圍

使用 [Set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) Cmdlet 設定備份點的保留。 雖然在定義備份排程之前設定保留點看起來有點奇怪，使用 ```Set-DPMPolicyObjective``` Cmdlet 會自動設定預設的備份排程，之後便可加以修改。 您一律可以先設定備份排程，然後再設定保留原則。

在下面的範例中，此 Cmdlet 會設定磁碟備份的保留參數。 這將會保留 10 天備份，並每隔 6 小時同步處理實際執行伺服器和 DPM 伺服器之間的資料。 ```SynchronizationFrequencyMinutes``` 不會定義建立備份點的頻率，只會定有資料複製到 DPM 伺服器的頻率。  此設定可防止備份變得太大。

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

針對移至 Azure 的備份 (DPM 將這些稱為線上備份)，保留範圍可設定為 [使用 Grandfather-Father-Son 配置 (GFS) 的長期保留](backup-azure-backup-cloud-as-tape.md)。 也就是說，您可以定義結合的保留原則，包含每日、每週、每月和每年保留原則。 在此範例中，我們會建立陣列，表示我們需要的複雜保留配置，然後使用 [Set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) Cmdlet 設定保留範圍。

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>設定備份排程

如果您使用 ```Set-DPMPolicyObjective``` Cmdlet 指定保護目標，DPM 會自動設定預設的備份排程。 若要變更預設排程，請依序使用 [Get-DPMPolicySchedule](/powershell/module/dataprotectionmanager/get-dpmpolicyschedule) Cmdlet 和 [Set-DPMPolicySchedule](/powershell/module/dataprotectionmanager/set-dpmpolicyschedule) Cmdlet。

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

在上述範例中， ```$onlineSch``` 是具有四個元素的陣列，其中包含 GFS 配置中保護群組的現有線上保護排程：

1. ```$onlineSch[0]``` 包含每日排程
2. ```$onlineSch[1]``` 包含每週排程
3. ```$onlineSch[2]``` 包含每月排程
4. ```$onlineSch[3]``` 包含每年排程

因此，如果您需要修改每週排程，您需要參考 ```$onlineSch[1]```。

### <a name="initial-backup"></a>初始備份

第一次備份資料來源時，DPM 需要建立初始複本，以建立要在 DPM 複本磁碟區上保護的資料來源完整複本。 此活動可以排定在特定的時間，或可以使用 [Set-DPMReplicaCreationMethod](/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod) Cmdlet 搭配參數 ```-NOW``` 手動觸發。

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>變更 DPM 複本和復原點磁碟區的大小

您也可以使用 [Set-DPMDatasourceDiskAllocation](/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation) Cmdlet，變更 DPM 複本磁碟區和陰影複製磁碟區的大小，如下列範例所示：Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>將變更認可到保護群組

最後，需要先認可變更，DPM 才可以根據每個新保護群組組態進行備份。 這可以使用 [Set-DPMProtectionGroup](/powershell/module/dataprotectionmanager/set-dpmprotectiongroup) Cmdlet 來達成。

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>檢視備份點

您可以使用 [Get-DPMRecoveryPoint](/powershell/module/dataprotectionmanager/get-dpmrecoverypoint) Cmdlet 來取得資料來源所有復原點的清單。 在此範例中，我們將會：

* 擷取 DPM 伺服器上以及儲存在 ```$PG```
* 取得與 ```$PG[0]```
* 取得資料來源的所有復原點。

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>還原在 Azure 上保護的資料

還原資料是 ```RecoverableItem``` 物件和 ```RecoveryOption``` 物件的組合。 在上一個節中，我們已取得資料來源的備份點清單。

在下列範例中，我們將示範如何透過結合備份點與復原目標從 Azure 備份還原 Hyper-V 虛擬機器。 這個範例包含︰

* 使用 [New-DPMRecoveryOption](/powershell/module/dataprotectionmanager/new-dpmrecoveryoption) Cmdlet 建立復原選項。
* 使用 ```Get-DPMRecoveryPoint``` Cmdlet 擷取備份點的陣列。
* 選擇要從中還原的備份點。

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

命令可以很容易地針對任何資料來源類型擴充。

## <a name="next-steps"></a>後續步驟

* 如需 DPM 至 Azure 備份的詳細資訊，請參閱 [DPM 備份簡介](backup-azure-dpm-introduction.md)
