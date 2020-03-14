---
title: 針對 Azure Vm 的備份錯誤進行疑難排解
description: 在本文中，您將瞭解如何針對 Azure 虛擬機器備份和還原時遇到的錯誤進行疑難排解。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 15e4b4c8850798fd2386cd2874b6ab58a18d5406
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297385"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>針對 Azure 虛擬機器上的備份失敗進行疑難排解

您可以針對使用 Azure 備份時所遇到的錯誤進行疑難排解，並提供下列資訊：

## <a name="backup"></a>Backup

本節涵蓋 Azure 虛擬機器的備份作業失敗。

### <a name="basic-troubleshooting"></a>基本疑難排解

* 確定 VM 代理程式（WA 代理程式）為[最新版本](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)。
* 請確定已支援 Windows 或 Linux VM 作業系統版本，請參閱[IAAS VM 備份支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)。
* 確認另一個備份服務並未執行。
  * 若要確保沒有任何快照集擴充功能問題，請[卸載擴充功能以強制重載，然後再重試備份](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout)。
* 確認 VM 具有網際網路連線能力。
  * 請確定另一個備份服務並未執行。
* 從 `Services.msc`，確定**Windows Azure 來賓代理程式** **服務正在執行。** 如果**Windows Azure 來賓代理程式**服務遺失，請從[備份復原服務保存庫中的 Azure vm](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)進行安裝。
* **事件記錄**檔可能會顯示來自其他備份產品的備份失敗（例如，Windows Server backup），而不是 Azure 備份所致。 使用下列步驟來判斷問題是否為 Azure 備份：
  * 如果事件來源或訊息中的專案**備份**發生錯誤，請檢查 AZURE IaaS VM 備份是否成功，以及是否使用所需的快照集類型建立還原點。
  * 如果 Azure 備份運作中，則問題可能是另一個備份解決方案。
  * 以下是事件檢視器錯誤517的範例，其中 Azure 備份正常運作，但 "Windows Server Backup" 失敗：<br>
    ![Windows Server Backup 失敗](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * 如果 Azure 備份失敗，請在本文的常見 VM 備份錯誤一節中尋找對應的錯誤碼。

## <a name="common-issues"></a>常見問題

以下是 Azure 虛擬機器上的備份失敗常見問題。

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime-從保存庫複本備份的資料超時

錯誤碼： CopyingVHDsFromBackUpVaultTakingLongTime <br/>
錯誤訊息：從保存庫複本備份的資料超時

這可能是因為暫時性儲存體錯誤或儲存體帳戶 IOPS 不足，備份服務在超時期間內將資料傳輸到保存庫。 請使用這些[最佳作法](backup-azure-vms-introduction.md#best-practices)來設定 VM 備份，然後重試備份操作。

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState-VM 不是處於允許備份的狀態

錯誤碼： UserErrorVmNotInDesirableState <br/>
錯誤訊息： VM 不是處於允許備份的狀態。<br/>

備份作業失敗，因為 VM 處於失敗狀態。 若要成功完成備份，VM 的狀態應該處於「執行中」、「已停止」或「已停止 (已解除配置)」。

* 如果 VM 處於 [正在執行] 和 [關機] 之間的暫時性狀態，請等候狀態變更。 然後再觸發備份作業。
* 如果 VM 是 Linux VM 並使用安全性強化的 Linux 核心模組，請從安全性原則中排除 Azure Linux 代理程式路徑 **/var/lib/waagent**，並確定已安裝備份延伸模組。

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed-無法凍結 VM 的一或多個掛接點，以取得檔案系統一致快照集

錯誤碼： UserErrorFsFreezeFailed <br/>
錯誤訊息：無法凍結 VM 的一或多個掛接點以取得檔案系統一致的快照集。

* 使用**umount**命令，卸載未清除檔案系統狀態的裝置。
* 使用**fsck**命令，在這些裝置上執行檔案系統一致性檢查。
* 再次掛接裝置，然後重試備份操作。</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC-延伸模組安裝/操作因 COM + 錯誤而失敗

錯誤碼： ExtensionSnapshotFailedCOM <br/>
錯誤訊息：快照集作業因 COM + 錯誤而失敗

錯誤碼： ExtensionInstallationFailedCOM  <br/>
錯誤訊息：延伸模組安裝/作業因 COM + 錯誤而失敗

錯誤碼： ExtensionInstallationFailedMDTC <br/>
錯誤訊息：延伸模組安裝失敗，錯誤為「COM + 無法與 Microsoft 分散式交易協調器交談 <br/>

由於 Windows 服務**Com + 系統**應用程式發生問題，導致備份操作失敗。  若要解決此問題，請依照下列步驟執行︰

* 嘗試啟動/重新開機 Windows 服務**Com + 系統應用程式**（從提高許可權的命令提示字元 **-net start COMSysApp**）。
* 請確定**分散式交易協調器**服務是以**Network service**帳戶的身分執行。 如果沒有，請將它變更為 [以**網路服務**帳戶執行]，然後重新開機**Com + 系統應用程式**。
* 如果無法重新開機服務，請遵循下列步驟來重新安裝**分散式交易協調器**服務：
  * 停止 MSDTC 服務
  * 開啟命令提示字元 (cmd)
  * 執行命令 "msdtc-uninstall"
  * 執行命令 "msdtc-install"
  * 啟動 MSDTC 服務
* 啟動 Windows 服務 **COM+ System Application**。 **COM+ System Application** 啟動後，請從 Azure 入口網站觸發備份作業。</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState-快照集作業失敗，因為 VSS 寫入器處於不良狀態

錯誤碼： ExtensionFailedVssWriterInBadState <br/>
錯誤訊息：快照集作業失敗，因為 VSS 寫入器處於不正常的狀態。

請重新啟動處於不良狀態的 VSS 寫入器。 從提升權限的命令提示字元，執行 ```vssadmin list writers```。 輸出會包含所有 VSS 寫入器和其狀態。 針對每個狀態不是「[1] 穩定」的 VSS 寫入器，請從提升權限的命令提示字元執行下列命令，以重新啟動那些 VSS 寫入器：

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure-剖析備份延伸模組的設定失敗

錯誤碼： ExtensionConfigParsingFailure<br/>
錯誤訊息：無法剖析備份延伸模組的設定。

此錯誤是因為 **MachineKeys** 目錄上的權限已變更︰ **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**。
執行下列命令，並確認**MachineKeys**目錄的許可權是預設的：**icacls%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**。

預設的權限如下︰

* 每個人：（R，W）
* BUILTIN\Administrators：（F）

如果您發現 **MachineKeys** 目錄中的權限不同於預設權限，請依照下列步驟來修正權限，刪除憑證，並觸發備份：

1. 修正 **MachineKeys** 目錄上的權限。 在目錄中使用 Explorer 安全性屬性和進階安全性設定，來將權限重設為預設值。 從目錄中移除所有使用者物件 (預設值除外)，然後確定 [所有人] 權限有下列特殊存取權︰

   * 列出資料夾/讀取資料
   * 讀取屬性
   * 讀取擴充屬性
   * 建立檔案/寫入資料
   * 建立資料夾/附加資料
   * 寫入屬性
   * 寫入擴充屬性
   * 讀取權限
2. 刪除 [發給] 是傳統部署模型或 **Windows Azure CRP 憑證產生器**的所有憑證：

   * [在本機電腦主控台上開啟憑證](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) \(機器翻譯\)。
   * 在 [個人] > [憑證] 底下，刪除 [核發對象] 是傳統部署模型或 **Windows Azure CRP 憑證產生器**的所有憑證。
3. 觸發 VM 備份作業。

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState-延伸模組狀態不是備份作業的可進行操作

錯誤碼： ExtensionStuckInDeletionState <br/>
錯誤訊息：無法執行備份作業的延伸模組狀態

備份作業失敗，因為備份延伸模組的狀態不一致。 若要解決此問題，請依照下列步驟執行︰

* 確認客體代理程式已安裝且可回應
* 從 Azure 入口網站，移至 [**虛擬機器**] > [**所有設定**] > [**延伸**模組]
* 選取備份擴充 VmSnapshot 或 VmSnapshotLinux，然後按一下 [解除安裝]
* 刪除備份擴充功能之後，請重試備份作業
* 後續的備份作業將會以所需狀態安裝新的擴充

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError-快照集作業失敗，因為部分連結的磁片已超出快照集限制

錯誤碼： ExtensionFailedSnapshotLimitReachedError  <br/>
錯誤訊息：快照集作業失敗，因為某些連接的磁片已超過快照集限制

快照集作業失敗，因為某些連接的磁片已超過快照集限制。 請完成下列疑難排解步驟，然後再次嘗試操作。

* 刪除不需要的磁片 blob 快照集。 請小心不要刪除磁片 blob，只應刪除快照集 blob。
* 如果已在 VM 磁片儲存體帳戶上啟用虛刪除，請設定虛刪除保留，讓現有的快照集小於任何時間點所允許的最大值。
* 如果已在備份的 VM 中啟用 Azure Site Recovery，請執行下列步驟：

  * 確定在/etc/azure/vmbackup.conf 中， **isanysnapshotfailed**的值設定為 false
  * 排程 Azure Site Recovery 在不同的時間，使其不會與備份作業衝突。

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive-快照集作業因 VM 資源不足而失敗

錯誤碼： ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
錯誤訊息：因為 VM 資源不足，所以快照集作業失敗。

VM 上的備份作業失敗，因為執行快照集作業時網路呼叫發生延遲。 若要解決此問題，請執行步驟 1。 如果問題持續發生，請嘗試步驟 2 和 3。

**步驟 1**：透過主機建立快照集

從提升權限的 (系統管理) 命令提示字元中，執行下列命令：

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

這可確保快照集會透過主機 (而非客體資源) 取得。 請重試備份作業。

**步驟 2**：嘗試將備份排程變更為 VM 負載低於較少的時間（較少的 CPU/IOps 等等）。

**步驟 3**：嘗試[增加 VM 的大小](https://azure.microsoft.com/blog/resize-virtual-machines/)，然後重試操作

## <a name="common-vm-backup-errors"></a>常見的 VM 備份錯誤

| 錯誤詳細資料 | 因應措施 |
| ------ | --- |
| **錯誤碼**：320001、ResourceNotFound <br/> **錯誤訊息**：因為 VM 已不存在，所以無法執行此作業。 <br/> <br/> **錯誤碼**：400094、BCMV2VMNotFound <br/> **錯誤訊息**：虛擬機器不存在 <br/> <br/>  找不到 Azure 虛擬機器。  |此錯誤會在已刪除主要 VM，但備份原則仍然在尋找 VM 來備份時發生。 若要修正此錯誤，請遵循下列步驟： <ol><li> 重新建立具有相同名稱和相同資源群組名稱 (**雲端服務名稱**) 的虛擬機器，<br>**or**</li><li> 停止保護虛擬機器 (不論是否刪除備份資料)。 如需詳細資訊，請參閱[停止保護虛擬機器](backup-azure-manage-vms.md#stop-protecting-a-vm)。</li></ol>|
|**錯誤碼**： UserErrorBCMPremiumStorageQuotaError<br/> **錯誤訊息**：因為儲存體帳戶中的可用空間不足，所以無法複製虛擬機器的快照集 | 針對 VM 備份堆疊 V1 上的進階 VM，我們會將快照集複製到儲存體帳戶。 此步驟是為了確定快照集上運作的備份管理流量不會限制可供使用進階磁碟的應用程式使用的 IOPS 數目。 <br><br>我們建議您僅配置 50% (17.5 TB) 的總儲存體帳戶空間。 然後 Azure 備份服務便可以將快照集複製到儲存體帳戶，並從儲存體帳戶中的這個複製位置將資料傳送到到保存庫。 |
| **錯誤碼**：380008、AzureVmOffline <br/> **錯誤訊息**：無法安裝 Microsoft 復原服務延伸模組，因為虛擬機器並未執行 | VM 代理程式是 Azure 復原服務延伸模組的必要條件。 請安裝 Azure 虛擬機器代理程式並重新啟動註冊作業。 <br> <ol> <li>檢查是否已正確安裝 VM 代理程式。 <li>確定已正確設定 VM 設定上的旗標。</ol> 深入了解如何安裝 VM 代理程式，以及如何驗證 VM 代理程式安裝。 |
| **錯誤碼**： ExtensionSnapshotBitlockerError <br/> **錯誤訊息**：快照集作業失敗，發生磁碟區陰影複製服務（VSS）作業錯誤，**此磁片磁碟機已由 BitLocker 磁碟機加密鎖定。您必須從 [控制台] 解除鎖定這個磁片磁碟機。** |對 VM 上的所有磁碟機關閉 BitLocker，並檢查是否已解決 VSS 問題。 |
| **錯誤碼**： VmNotInDesirableState <br/> **錯誤訊息**： VM 不是處於允許備份的狀態。 |<ul><li>如果 VM 處於 [正在執行] 和 [關機] 之間的暫時性狀態，請等候狀態變更。 然後再觸發備份作業。 <li> 如果 VM 是 Linux VM 並使用安全性強化的 Linux 核心模組，請從安全性原則中排除 Azure Linux 代理程式路徑 **/var/lib/waagent**，並確定已安裝備份延伸模組。  |
| 虛擬機器上沒有 VM 代理程式： <br>請安裝所有必要條件和 VM 代理程式。 接著請重新啟動作業。 |深入了解 [VM 代理程式安裝，以及如何驗證 VM 代理程式安裝](#vm-agent)。 |
| **錯誤碼**： ExtensionSnapshotFailedNoSecureNetwork <br/> **錯誤訊息**：快照集作業失敗，因為無法建立安全的網路通道。 | <ol><li> 在提高權限的模式中執行 **regedit.exe**，來開啟登錄編輯程式。 <li> 識別系統中存在的所有 .NET Framework 版本。 它們位於登錄機碼 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** 的階層下。 <li> 針對登錄機碼中的每個 .NET Framework，新增下列機碼︰ <br> **SchUseStrongCrypto"=dword:00000001**。 </ol>|
| **錯誤碼**： ExtensionVCRedistInstallationFailure <br/> **錯誤訊息**：快照集作業失敗，因為無法安裝適用于C++ Visual Studio 2012 的 Visual 可轉散發套件。 | 流覽至 C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion 並安裝 vcredist2013_x64。<br/>請確定允許服務安裝的登錄機碼值設定為正確的值。 也就是將**HKEY_LOCAL_MACHINE \system\currentcontrolset\services\msiserver**中的**Start**值設定為**3** ，而不是**4**。 <br><br>如果您仍遇到安裝問題，請從提高權限的命令提示字元執行 **MSIEXEC /UNREGISTER**，再執行 **MSIEXEC /REGISTER**，以重新啟動安裝服務。  |
| **錯誤碼**： UserErrorRequestDisallowedByPolicy <BR> **錯誤訊息**：在 VM 上設定了不正確原則，這會導致快照集作業無法運作。 | 如果您的 Azure 原則可[控制環境中的標記](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags)，請考慮將原則從[拒絕效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deny)變更為[修改效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)，或根據[Azure 備份所需的命名架構](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines)，手動建立資源群組。
## <a name="jobs"></a>工作

| 錯誤詳細資料 | 因應措施 |
| --- | --- |
| 此作業類型不支援取消： <br>請等候作業完成。 |None |
| 此作業未處於可取消的狀態： <br>請等候作業完成。 <br>**or**<br> 選取的作業未處於可取消的狀態： <br>請等候作業完成。 |作業很可能已經快要完成。 請等候作業完成。|
| 備份無法取消作業，因為它並未正在進行： <br>僅支援針對進行中的作業進行取消。 請嘗試取消正在進行的作業。 |此錯誤發生的原因是因為暫時性的狀態。 請稍候再重試取消作業。 |
| 備份無法取消作業： <br>請等候作業完成。 |None |

## <a name="restore"></a>{1}還原{2}

| 錯誤詳細資料 | 因應措施 |
| --- | --- |
| 還原失敗，發生雲端內部錯誤。 |<ol><li>您嘗試還原的雲端服務是使用 DNS 設定所設定。 您可以檢查： <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**。<br>如果已設定 [位址]，則 DNS 設定便已設定。<br> <li>您嘗試還原到其中的雲端服務是使用 **ReservedIP** 所設定，而雲端服務中的現有 VM 目前處於停止狀態。 您可以使用下列 PowerShell Cmdlet 來檢查雲端服務是否已保留 IP： **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**。 <br><li>您嘗試將具有下列特殊網路組態的虛擬機器還原至相同的雲端服務： <ul><li>負載平衡器設定下的虛擬機器，內部與外部。<li>具有多個保留 IP 的虛擬機器。 <li>具有多個 NIC 的虛擬機器。 </ul><li>在 UI 中選取新的雲端服務，或參閱適用於具有特殊網路組態之 VM 的[還原考量](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)。</ol> |
| 選取的 DNS 名稱已有人使用： <br>請指定不同的 DNS 名稱並再試一次。 |此 DNS 名稱是指雲端服務名稱，其結尾通常是 **.cloudapp.net**。 此名稱必須是唯一的。 如果您遇到這個錯誤，您需要在還原期間選擇不同的 VM 名稱。 <br><br> 只有 Azure 入口網站的使用者才會看到這個錯誤。 透過 PowerShell 執行還原作業將會成功，因為它只會還原磁碟，並不會建立 VM。 當您在磁碟還原作業之後明確建立 VM 時，將會遇到此錯誤。 |
| 指定的虛擬網路設定不正確： <br>請指定不同的虛擬網路設定並再試一次。 |None |
| 指定的雲端服務所使用的保留 IP 不符合要還原之虛擬機器的設定： <br>請指定未使用保留 IP 的其他雲端服務。 或選擇另一個復原點來進行還原。 |None |
| 雲端服務已達到其輸入端點的數目限制： <br>請指定不同的雲端服務或使用現有的端點來重試作業。 |None |
| 復原服務保存庫和目標儲存體帳戶處於兩個不同的區域： <br>請確定還原作業中所指定的儲存體帳戶和您的復原服務保存庫皆位於相同的 Azure 區域中。 |None |
| 不支援針對還原作業所指定的儲存體帳戶： <br>僅支援具有本地備援或異地備援複寫設定的「基本」或「標準」儲存體帳戶。 請選取支援的儲存體帳戶。 |None |
| 針對還原作業所指定的儲存體帳戶類型未上線： <br>請確定針對還原作業所指定的儲存體帳戶類型已上線。 |此錯誤發生的原因可能是因為 Azure 儲存體中發生暫時性錯誤，或是因為運作中斷。 選擇另一個儲存體帳戶。 |
| 已達到資源群組配額： <br>請從 Azure 入口網站刪除一些資源群組，或連絡 Azure 支援以提高限制。 |None |
| 選取的子網路不存在： <br>請選取存在的子網路。 |None |
| 備份服務無權存取您訂用帳戶中的資源。 |若要解決此錯誤，請先使用[還原備份的磁碟](backup-azure-arm-restore-vms.md#restore-disks)中的步驟來還原磁碟。 然後使用[從還原的磁碟建立 VM](backup-azure-vms-automation.md#restore-an-azure-vm) 中的 PowerShell 步驟。 |

## <a name="backup-or-restore-takes-time"></a>備份或還原需花費很長的時間

如果您的備份時間超過12小時，或還原時間超過6小時，請參閱[最佳做法](backup-azure-vms-introduction.md#best-practices)和[效能考慮](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM 代理程式

### <a name="set-up-the-vm-agent"></a>設定 VM 代理程式

一般而言，VM 代理程式已存在於從 Azure 資源庫建立的 VM 中。 不過，從內部部署資料中心移轉的虛擬機器並不會安裝 VM 代理程式。 針對那些 VM，必須明確安裝 VM 代理程式。

#### <a name="windows-vms"></a>Windows VM

* 下載並安裝 [代理程式 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您需要有系統管理員權限，才能完成安裝。
* 針對使用傳統部署模型建立的虛擬機器，請[更新 VM 屬性](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) \(英文\) 以指出代理程式已安裝。 Azure Resource Manager 虛擬機器不需要此步驟。

#### <a name="linux-vms"></a>Linux VM

* 從散發套件存放庫安裝最新版的代理程式。 如需套件名稱的詳細資料，請參閱 [Linux 代理程式存放庫](https://github.com/Azure/WALinuxAgent) \(英文\)。
* 針對使用傳統部署模型建立的 VM，請[使用此部落格](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) \(英文\) 來更新 VM 屬性並確認代理程式已安裝。 資源管理員虛擬機器不需要此步驟。

### <a name="update-the-vm-agent"></a>更新 VM 代理程式

#### <a name="windows-vms"></a>Windows VM

* 若要更新 VM 代理程式，請重新安裝 [VM 代理程式二進位檔](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 更新代理程式之前，請先確定 VM 代理程式更新期間不會進行任何備份作業。

#### <a name="linux-vms"></a>Linux VM

* 若要更新 Linux VM 代理程式，請遵循[更新 Linux VM 代理程式](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文中的指示。

    > [!NOTE]
    > 請一律使用散發套件存放庫來更新代理程式。

    請勿從 GitHub 下載代理程式程式碼。 如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以了解如何取得最新的代理程式。 您也可以在 GitHub 存放庫中查看最新的 [Windows Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent/releases)資訊。

### <a name="validate-vm-agent-installation"></a>驗證 VM 代理程式安裝

確認 Windows VM 上的 VM 代理程式版本：

1. 登入 Azure 虛擬機器，然後瀏覽至 C:\WindowsAzure\Packages 資料夾。 您應該會找到 **WaAppAgent.exe** 檔案。
2. 請以滑鼠右鍵按一下該檔案，然後移至 [內容]。 然後選取 [**詳細資料**] 索引標籤。[**產品版本**] 欄位應為 [2.6.1198.718] 或更新版本。

## <a name="troubleshoot-vm-snapshot-issues"></a>對 VM 快照集問題進行疑難排解

VM 備份仰賴發給底層儲存體的快照命令。 無法存取儲存體或快照集工作執行上的延遲，可能會造成備份作業失敗。 下列狀況可能導致快照集工作失敗：

* **已設定 SQL Server 備份的 VM 可能會造成快照集工作延遲**。 根據預設，VM 備份會在 Windows VM 上建立 VSS 完整備份。 執行 SQL Server 並已設定 SQL Server 備份的 VM，可能會遇到快照集延遲。 如果快照集延遲會導致備份失敗，請設定下列登錄機碼：

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **所報告的 VM 狀態不正確，因為 VM 已在 RDP 中關機**。 如果您使用遠端桌面來將虛擬機器關機，請確認該 VM 在入口網站中的狀態是否正確。 如果狀態不正確，請使用入口網站 VM 儀表板中的 [關機] 選項來關閉 VM。
* **如果有超過四個 VM 共用相同的雲端服務，請將 VM 分散到多個備份原則上**。 錯開備份時間，來讓同一時間開始的 VM 備份不超過四個。 請嘗試讓原則中的開始時間至少錯開一小時。
* **VM 執行會使用大量 CPU 或記憶體資源**。 若虛擬機器執行時的記憶體或 CPU 使用量非常高 (>90%)，快照集工作會被排入佇列並延遲。 最後，它會超時。如果發生此問題，請嘗試隨選備份。

## <a name="networking"></a>網路功能

必須在來賓內啟用 DHCP，IaaS VM 備份才能運作。 如果您需要靜態私人 IP，請透過 Azure 入口網站或 PowerShell 加以設定。 確定 VM 內的 DHCP 選項已啟用。
取得如何透過 PowerShell 設定靜態 IP 的詳細資訊：

* [如何將靜態內部 IP 位址新增至現有的 VM](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
* [針對指派至網路介面的私人 IP 位址變更配置方法](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)

