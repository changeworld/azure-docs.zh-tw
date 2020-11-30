---
title: 針對 Azure VM 的備份錯誤進行疑難排解
description: 在本文中，了解如何針對備份和還原 Azure 虛擬機器時遇到的錯誤進行疑難排解。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: cb25d9263648fbd92bc075751c1a8e627d03bd44
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325208"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>針對 Azure 虛擬機器上的備份失敗進行疑難排解

您可以利用底下列出的資訊，針對使用 Azure 備份時所發生的錯誤進行疑難排解：

## <a name="backup"></a>Backup

本節涵蓋 Azure 虛擬機器的備份作業失敗。

### <a name="basic-troubleshooting"></a>基本疑難排解

* 確定 VM 代理程式 (WA 代理程式) 是[最新版本](./backup-azure-arm-vms-prepare.md#install-the-vm-agent)。
* 確定支援 Windows 或 Linux VM 作業系統版本，請參閱 [IaaS VM 備份支援矩陣](./backup-support-matrix-iaas.md)。
* 確認另一個備份服務未執行。
  * 若要確保沒有快照集擴充功能問題，請[解除安裝擴充功能，以強制重新載入並重試備份](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)。
* 確認 VM 具有網際網路連線。
  * 請確定沒有其他備份服務正在執行。
* 從 `Services.msc` 中，確定 **Windows Azure 客體代理程式** 服務 **執行中**。 如果遺漏 **Windows Azure 客體代理程式** 服務，請從 [備份復原服務保存庫中的 Azure VM](./backup-azure-arm-vms-prepare.md#install-the-vm-agent)安裝該服務。
* **事件記錄** 檔可能會顯示來自其他備份產品（例如 Windows Server backup）的備份失敗，而不是因為 Azure 備份。 請使用下列步驟來判斷問題是否與 Azure 備份有關：
  * 如果事件來源或訊息中的專案 **備份** 發生錯誤，請檢查 AZURE IaaS VM 備份備份是否成功，以及是否以所需的快照集類型建立還原點。
  * 如果 Azure 備份運作中，則問題可能與另一個備份解決方案有關。
  * 以下是事件檢視器錯誤517的範例，其中 Azure 備份可正常運作，但 "Windows Server Backup" 失敗： ![ Windows Server Backup 失敗](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * 如果 Azure 備份失敗，請在本文的常見 VM 備份錯誤一節中尋找對應的錯誤碼。

## <a name="common-issues"></a>常見問題

以下是 Azure 虛擬機器上備份失敗的常見問題。

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>VMRestorePointInternalError-在 VM 中設定的防毒軟體正在限制備份擴充功能的執行

錯誤碼： VMRestorePointInternalError

如果備份時， **事件檢視器應用程式記錄** 檔會顯示訊息錯誤的 **應用程式名稱： IaaSBcdrExtension.exe** 接著確認 VM 中設定的防毒軟體會限制備份延伸模組的執行。
若要解決此問題，請將下列目錄排除在防毒軟體設定中，然後重試備份作業。

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - 從保存庫複製備份的資料已逾時

錯誤碼：CopyingVHDsFromBackUpVaultTakingLongTime <br/>
錯誤訊息：從保存庫複製備份的資料已逾時

發生此情況的原因是儲存體有暫時性的錯誤，或儲存體帳戶的 IOPS 不足，導致備份服務無法在逾時期間內將資料傳輸到保存庫。 請使用這些[最佳做法](backup-azure-vms-introduction.md#best-practices)設定 VM 備份，然後重試備份作業。

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - VM 不是處於允許備份的狀態

錯誤碼：UserErrorVmNotInDesirableState <br/>
錯誤訊息：VM 目前的狀態不允許備份。<br/>

備份作業失敗，因為 VM 處於失敗狀態。 若要成功完成備份，VM 的狀態應該處於「執行中」、「已停止」或「已停止 (已解除配置)」。

* 如果 VM 處於 [正在執行] 和 [關機] 之間的暫時性狀態，請等候狀態變更。 然後再觸發備份作業。
* 如果 VM 是 Linux VM 並使用安全性強化的 Linux 核心模組，請從安全性原則中排除 Azure Linux 代理程式路徑 **/var/lib/waagent**，並確定已安裝備份延伸模組。

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - 無法凍結 VM 的一或多個掛接點，以取得檔案系統一致快照集

錯誤碼：UserErrorFsFreezeFailed <br/>
錯誤訊息：無法凍結 VM 的一或多個掛接點以建立檔案系統一致快照集。

* 使用 **umount** 命令卸載未清除檔案系統狀態的裝置。
* 使用 **fsck** 命令對這些裝置執行檔案系統一致性檢查。
* 重新裝載裝置並重試備份作業。</ol>

### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC - 擴充功能安裝/作業由於 COM+ 錯誤而失敗

錯誤碼：ExtensionSnapshotFailedCOM <br/>
錯誤訊息：因 COM + 錯誤而導致快照集作業失敗

錯誤碼：ExtensionInstallationFailedCOM  <br/>
錯誤訊息：擴充功能安裝/作業由於 COM+ 錯誤而失敗

錯誤碼：ExtensionInstallationFailedMDTC <br/>
錯誤訊息：擴充功能安裝失敗，發生錯誤「COM+ 無法與 Microsoft Distributed Transaction Coordinator 通話」 <br/>

備份作業失敗，因為 Windows 服務 **COM+ 系統** 應用程式發生問題。  若要解決此問題，請依照下列步驟執行︰

* 嘗試重新啟動/重新啟動 Windows 服務 **COM+ System Application** (從提高權限的命令提示字元 **- net start COMSysApp**)。
* 確定 **分散式交易協調器** 服務正在以 **網路服務** 帳戶的身分執行。 如果不是，請將其變更為以 **網路服務** 帳戶的身分執行，然後重新啟動 **COM+ 系統應用程式**。
* 如果無法重新開機服務，請遵循下列步驟來重新安裝 **分散式交易協調器** 服務：
  * 停止 MSDTC 服務
  * 開啟命令提示字元 (cmd)
  * 執行命令`msdtc -uninstall`
  * 執行命令`msdtc -install`
  * 啟動 MSDTC 服務
* 啟動 Windows 服務 **COM+ System Application**。 **COM+ System Application** 啟動後，請從 Azure 入口網站觸發備份作業。</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - 快照集作業失敗，因為 VSS 寫入器處於不良狀態。

錯誤碼：ExtensionFailedVssWriterInBadState <br/>
錯誤訊息：快照集作業失敗，因為 VSS 寫入器處於不良狀態。

發生此錯誤是因為 VSS 寫入器處於不良狀態。 Azure 備份擴充功能會與 VSS 寫入器互動，以取得磁片的快照集。 若要解決此問題，請遵循下列步驟：

步驟1：重新開機處於不良狀態的 VSS 寫入器。

* 從提升權限的命令提示字元，執行 ```vssadmin list writers```。
* 輸出會包含所有 VSS 寫入器和其狀態。 針對狀態不是 **[1]** 的每個 vss 寫入器，重新開機各自的 vss 寫入器服務。
* 若要重新開機服務，請從提高許可權的命令提示字元執行下列命令：

 ```net stop serviceName``` <br>
 ```net start serviceName```

> [!NOTE]
> 重新開機某些服務可能會影響您的生產環境。 請確定已遵循核准程式，並在排定的停機時間重新開機服務。

步驟2：如果重新開機 VSS 寫入器並未解決問題，請從提升許可權的命令提示字元中執行下列命令 (系統管理員) ，以防止建立 blob 快照集的執行緒。

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

步驟3：如果步驟1和2沒有解決問題，可能是因為因為 IOPS 受限而導致 VSS 寫入器超時。<br>

若要確認，請流覽至 [**系統並事件檢視器應用程式記錄** 檔 _]，並檢查下列錯誤訊息：<br>
_The 陰影複製提供者在保存要陰影複製的磁片區寫入時超時。 這可能是因為應用程式或系統服務的磁片區上有過多活動。 請稍後再試一次磁片區上的活動減少。 *<br>

解決方案：

* 檢查是否有可將負載分散到 VM 磁片的可能性。 這會減少單一磁片上的負載。 您可以藉 [由在儲存體層級啟用診斷計量來檢查 IOPs 節流](../virtual-machines/troubleshooting/performance-diagnostics.md#install-and-run-performance-diagnostics-on-your-vm)。
* 將備份原則變更為當 VM 上的負載降到最低時，在離峰時段執行備份。
* 升級 Azure 磁片以支援更高的 IOPs。 [請於此處深入了解](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState - 快照集作業失敗，因為 VSS (磁碟區陰影複製) 服務處於不良狀態

錯誤碼： ExtensionFailedVssServiceInBadState <br/>
錯誤訊息：快照集作業失敗，因為 VSS (磁片區陰影複製) 服務處於不正確的狀態。

發生此錯誤的原因是 VSS 服務處於不良狀態。 Azure 備份擴充功能會與 VSS 服務互動，以取得磁片的快照集。 若要解決此問題，請遵循下列步驟：

重新開機 VSS (磁片區陰影複製) 服務。

* 流覽至 services.msc，然後重新開機「磁片區陰影複製服務」。<br>
(或)<br>
* 從提升權限的命令提示字元執行下列命令：

 ```net stop VSS``` <br>
 ```net start VSS```

如果問題仍持續發生，請在排定的停機時間重新開機 VM。

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable-VM 建立失敗，因為選取的 VM 大小無法使用

錯誤碼： UserErrorSkuNotAvailable 錯誤訊息： VM 建立失敗，因為選取的 VM 大小無法使用。

發生此錯誤的原因是在還原作業期間選取的 VM 大小是不支援的大小。 <br>

若要解決此問題，請在還原作業期間使用 [ [復原磁碟](./backup-azure-arm-restore-vms.md#restore-disks) ] 選項。 您可以使用這些磁片，透過[PowerShell Cmdlet](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks)，從[可用的支援 VM 大小](./backup-support-matrix-iaas.md#vm-compute-support)清單中建立 vm。

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported-VM 建立失敗，因為不存在市場採購申請

錯誤碼： UserErrorMarketPlaceVMNotSupported 錯誤訊息：因為沒有市場採購要求，所以 VM 建立失敗。

Azure 備份支援 Azure Marketplace 中可用 Vm 的備份和還原。 當您嘗試使用特定的方案/發行者設定來還原 VM (時，將會發生此錯誤，) 該設定已無法在 Azure Marketplace 中使用，請在 [這裡深入瞭解](/legal/marketplace/participation-policy#offering-suspension-and-removal)。

* 若要解決此問題，請在還原作業期間使用 [ [復原磁碟](./backup-azure-arm-restore-vms.md#restore-disks) ] 選項，然後使用 [PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 或 [AZURE CLI](./tutorial-restore-disk.md) Cmdlet，以與 vm 對應的最新 marketplace 資訊來建立 VM。
* 如果發行者沒有任何 Marketplace 資訊，您可以使用資料磁片來取出您的資料，並將其連結至現有的 VM。

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - 備份擴充功能的剖析和設定失敗

錯誤碼：ExtensionConfigParsingFailure<br/>
錯誤訊息：備份擴充功能的剖析和設定失敗。

此錯誤是因為 **MachineKeys** 目錄上的權限已變更︰ **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**。
執行下列命令，並確認 **MachineKeys** 目錄的許可權是預設值： `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys` 。

預設的權限如下︰

* 所有人：(R,W)
* BUILTIN\Administrators：(F)

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
2. 刪除 [發給] 是傳統部署模型或 **Windows Azure CRP 憑證產生器** 的所有憑證：

   * [在本機電腦主控台上開啟憑證](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) \(機器翻譯\)。
   * 在 [個人] > [憑證] 底下，刪除 [核發對象] 是傳統部署模型或 **Windows Azure CRP 憑證產生器** 的所有憑證。
3. 觸發 VM 備份作業。

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - 擴充狀態不支援備份作業

錯誤碼：ExtensionStuckInDeletionState <br/>
錯誤訊息：擴充狀態不支援備份作業

備份作業由於不一致的備份擴充狀態而失敗。 若要解決此問題，請依照下列步驟執行︰

* 確認客體代理程式已安裝且可回應
* 從 Azure 入口網站中移至 [虛擬機器] > [所有設定] > [擴充]
* 選取備份擴充功能 VmSnapshot 或 VmSnapshotLinux，然後選取 [ **卸載**]。
* 刪除備份擴充後，請再嘗試執行備份作業
* 後續的備份作業將會以所需狀態安裝新的擴充

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - 因為某些附加的磁碟超出快照集限制，所以快照集作業失敗

錯誤碼：ExtensionFailedSnapshotLimitReachedError  <br/>
錯誤訊息：快照集作業失敗，因為部分連結的磁碟已超出快照集限制

快照集作業失敗，因為部分連結的磁碟已超出快照集限制。 請完成下列疑難排解步驟，然後重試此操作。

* 刪除不需要的磁片 blob 快照集。 請小心不要刪除磁片 blob。 只有快照集 blob 應該刪除。
* 如果已在 VM 磁片儲存體帳戶上啟用虛刪除，請設定虛刪除保留，讓現有的快照集小於任何時間點允許的最大值。
* 如果已在備份的 VM 中啟用 Azure Site Recovery，請執行下列步驟：

  * 確定 **isanysnapshotfailed** 的值已在 /etc/azure/vmbackup.conf 設為 false
  * 請在不同的時間排程 Azure Site Recovery，使其不會與備份作業產生衝突。

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - 因為 VM 資源不足，所以快照集作業失敗

錯誤碼：ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
錯誤訊息：因為 VM 資源不足，所以快照集作業失敗。

因為執行快照作業時網路呼叫發生延遲，導致 VM 上的備份作業失敗。 若要解決此問題，請執行步驟 1。 如果問題持續發生，請嘗試步驟 2 和 3。

**步驟 1**：透過主機建立快照集

從提高許可權的 (系統管理員) 命令提示字元中，執行下列命令：

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

這可確保快照集會透過主機 (而非客體資源) 取得。 請重試備份作業。

**步驟 2**：嘗試將備份排程變更為 VM 低於負載 (（例如較少 CPU 或 IOPS）的時間) 

**步驟 3**：嘗試 [增加 VM 的大小](../virtual-machines/windows/resize-vm.md) ，然後再次嘗試操作

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001，ResourceNotFound-無法執行作業，因為 VM 已不存在/400094、BCMV2VMNotFound-虛擬機器不存在/找不到 Azure 虛擬機器

錯誤碼：320001, ResourceNotFound <br/> 錯誤訊息：無法執行作業，因為 VM 已不存在。 <br/> <br/> 錯誤碼：400094, BCMV2VMNotFound <br/> 錯誤訊息：虛擬機器不存在 <br/>
找不到 Azure 虛擬機器。

此錯誤會在已刪除主要 VM，但備份原則仍然在尋找 VM 來備份時發生。 若要修正此錯誤，請遵循下列步驟：

* 重新建立具有相同名稱和相同資源群組名稱 (**雲端服務名稱**) 的虛擬機器，<br>或
* 停止保護虛擬機器 (不論是否刪除備份資料)。 如需詳細資訊，請參閱[停止保護虛擬機器](backup-azure-manage-vms.md#stop-protecting-a-vm)。</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError-無法複製虛擬機器的快照集，因為儲存體帳戶中的可用空間不足

錯誤碼：UserErrorBCMPremiumStorageQuotaError<br/> 錯誤訊息：無法複製虛擬機器的快照集，因為儲存體帳戶中的可用空間不足

 針對 VM 備份堆疊 V1 上的進階 VM，我們會將快照集複製到儲存體帳戶。 此步驟是為了確定快照集上運作的備份管理流量不會限制可供使用進階磁碟的應用程式使用的 IOPS 數目。 <br><br>我們建議您僅配置 50% (17.5 TB) 的總儲存體帳戶空間。 然後 Azure 備份服務便可以將快照集複製到儲存體帳戶，並從儲存體帳戶中的這個複製位置將資料傳送到到保存庫。

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008，AzureVmOffline-無法安裝 Microsoft 復原服務擴充功能，因為虛擬機器未執行

錯誤碼：380008, AzureVmOffline <br/> 錯誤訊息：安裝 Microsoft 復原服務延伸失敗，因為虛擬機器並未執行

VM 代理程式是 Azure 復原服務延伸模組的必要條件。 請安裝 Azure 虛擬機器代理程式並重新啟動註冊作業。 <br> <ol> <li>檢查是否已正確安裝 VM 代理程式。 <li>確定已正確設定 VM 設定上的旗標。</ol> 深入了解如何安裝 VM 代理程式，以及如何驗證 VM 代理程式安裝。

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError-快照集作業失敗，發生磁碟區陰影複製服務 (VSS) 操作錯誤

錯誤碼：ExtensionSnapshotBitlockerError <br/> 錯誤訊息：快照集作業失敗，發生磁碟區陰影複製服務 (VSS) 作業錯誤： **BitLocker 磁碟機加密已鎖定此磁片磁碟機。您必須從主控台解除鎖定這個磁片磁碟機。**

對 VM 上的所有磁碟機關閉 BitLocker，並檢查是否已解決 VSS 問題。

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState-VM 未處於允許備份的狀態

錯誤碼：VmNotInDesirableState <br/> 錯誤訊息：VM 目前的狀態不允許備份。

* 如果 VM 處於 [正在執行] 和 [關機] 之間的暫時性狀態，請等候狀態變更。 然後再觸發備份作業。
* 如果 VM 是 Linux VM 並使用安全性強化的 Linux 核心模組，請從安全性原則中排除 Azure Linux 代理程式路徑 **/var/lib/waagent**，並確定已安裝備份延伸模組。

* 虛擬機器上沒有 VM 代理程式： <br>請安裝所有必要條件和 VM 代理程式。 接著請重新啟動作業。 | 深入瞭解 [Vm 代理程式安裝，以及如何驗證 Vm 代理程式安裝](#vm-agent)。

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork-快照集作業失敗，因為無法建立安全的網路通道

錯誤碼：ExtensionSnapshotFailedNoSecureNetwork <br/> 錯誤訊息：快照集作業失敗，因為無法建立安全網路通訊通道。

* 在提高權限的模式中執行 **regedit.exe**，來開啟登錄編輯程式。
* 識別系統中存在的所有 .NET Framework 版本。 它們位於登錄機碼 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** 的階層下。
* 針對登錄機碼中的每個 .NET Framework，新增下列機碼︰ <br> **SchUseStrongCrypto"=dword:00000001**。 </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure-因為無法安裝適用於 Visual Studio 的 Visual C++ 可轉散發套件2012，所以快照集作業失敗

錯誤碼：ExtensionVCRedistInstallationFailure <br/> 錯誤訊息：快照集作業失敗，因為無法安裝適用於 Visual Studio 2012 的 Visual C++ 可轉散發套件。

* 瀏覽至 `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` 並安裝 vcredist2013_x64。<br/>確定允許服務安裝的登錄機碼值已設定為正確的值。 也就是說，將 **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** 中的 **Start** 值設定為 **3**，而不是 **4**。 <br><br>如果您仍遇到安裝問題，請從提高權限的命令提示字元執行 **MSIEXEC /UNREGISTER**，再執行 **MSIEXEC /REGISTER**，以重新啟動安裝服務。
* 檢查事件記錄檔，以確認您是否注意到存取相關的問題。 例如：*產品：Microsoft Visual C++ 2013 x64 Minimum Runtime - 12.0.21005 -- 錯誤 1401。無法建立機碼：Software\Classes。系統錯誤 5。請驗證您是否有足夠的權限存取該機碼，或是連絡支援人員。* <br><br> 請確定管理員或使用者帳戶具有足夠權限，可更新登錄機碼 **HKEY_LOCAL_MACHINE \SOFTWARE\Classes**。 提供足夠的權限，並重新啟動 Windows Azure 客體代理程式。<br><br> <li> 如果您已備妥防毒軟體，請確定其具有正確的排除規則以允許安裝。

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy - VM 上設定了防止進行快照集作業的無效原則

錯誤碼：UserErrorRequestDisallowedByPolicy <BR> 錯誤訊息：VM 上設定了會防止進行快照集作業的無效原則。

如果您有[在環境內管理標籤](../governance/policy/tutorials/govern-tags.md)的 Azure 原則，請考慮將原則從[拒絕效果](../governance/policy/concepts/effects.md#deny)變更為[修改效果](../governance/policy/concepts/effects.md#modify)，或根據 [Azure 備份所需的命名結構描述](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)，手動建立資源群組。

## <a name="jobs"></a>工作

| 錯誤詳細資料 | 因應措施 |
| --- | --- |
| 此作業類型不支援取消： <br>請等候作業完成。 |None |
| 此作業未處於可取消的狀態： <br>請等候作業完成。 <br>**or**<br> 選取的作業未處於可取消的狀態： <br>請等候作業完成。 |作業很可能已經快要完成。 請等候作業完成。|
| 備份無法取消作業，因為它並未正在進行： <br>僅支援針對進行中的作業進行取消。 請嘗試取消正在進行的作業。 |此錯誤發生的原因是因為暫時性的狀態。 請稍候再重試取消作業。 |
| 備份無法取消作業： <br>請等候作業完成。 |None |

## <a name="restore"></a>還原

### <a name="disks-appear-offline-after-file-restore"></a>磁片在檔案還原後會離線

在還原之後，您會注意到磁片已離線，然後：

* 確認執行腳本的電腦是否符合作業系統需求。 [深入了解](./backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script)。  
* 請確定您不會還原至相同的來源， [深入瞭解](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>UserErrorInstantRpNotFound-還原失敗，因為找不到 VM 的快照集

錯誤碼： UserErrorInstantRpNotFound <br>
錯誤訊息：還原失敗，因為找不到 VM 的快照集。 快照集可能已刪除，請檢查。<br>

當您嘗試從未傳送至保存庫且已在快照集階段刪除的復原點還原時，就會發生此錯誤。 
<br>
若要解決此問題，請嘗試從不同的還原點還原 VM。<br>

#### <a name="common-errors"></a>常見錯誤 
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

如果您的備份時間超過 12 小時，或者還原時間超過 6 小時，請檢閱[最佳做法](backup-azure-vms-introduction.md#best-practices)和[效能考量](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM 代理程式

### <a name="set-up-the-vm-agent"></a>設定 VM 代理程式

一般而言，VM 代理程式已存在於從 Azure 資源庫建立的 VM 中。 不過，從內部部署資料中心移轉的虛擬機器並不會安裝 VM 代理程式。 針對那些 VM，必須明確安裝 VM 代理程式。

#### <a name="windows-vms---set-up-the-agent"></a>Windows Vm-設定代理程式

* 下載並安裝 [代理程式 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您需要有系統管理員權限，才能完成安裝。
* 針對使用傳統部署模型建立的虛擬機器，請[更新 VM 屬性](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) \(英文\) 以指出代理程式已安裝。 Azure Resource Manager 虛擬機器不需要此步驟。

#### <a name="linux-vms---set-up-the-agent"></a>Linux Vm-設定代理程式

* 從散發套件存放庫安裝最新版的代理程式。 如需套件名稱的詳細資料，請參閱 [Linux 代理程式存放庫](https://github.com/Azure/WALinuxAgent) \(英文\)。
* 針對使用傳統部署模型建立的 VM，請[更新 VM 屬性](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms)並確認代理程式已安裝。 資源管理員虛擬機器不需要此步驟。

### <a name="update-the-vm-agent"></a>更新 VM 代理程式

#### <a name="windows-vms---update-the-agent"></a>Windows Vm-更新代理程式

* 若要更新 VM 代理程式，請重新安裝 [VM 代理程式二進位檔](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 更新代理程式之前，請先確定 VM 代理程式更新期間不會進行任何備份作業。

#### <a name="linux-vms---update-the-agent"></a>Linux Vm-更新代理程式

* 若要更新 Linux VM 代理程式，請遵循[更新 Linux VM 代理程式](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json)一文中的指示。

    > [!NOTE]
    > 請一律使用散發套件存放庫來更新代理程式。

    請勿從 GitHub 下載代理程式程式碼。 如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以了解如何取得最新的代理程式。 您也可以在 GitHub 存放庫中查看最新的 [Windows Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent/releases)資訊。

### <a name="validate-vm-agent-installation"></a>驗證 VM 代理程式安裝

確認 Windows VM 上的 VM 代理程式版本：

1. 登入 Azure 虛擬機器，然後瀏覽至 C:\WindowsAzure\Packages 資料夾。 您應該會找到 **WaAppAgent.exe** 檔案。
2. 請以滑鼠右鍵按一下該檔案，然後移至 [內容]。 然後選取 [詳細資料] 索引標籤。[產品版本] 欄位應為 2.6.1198.718 或更高版本。

## <a name="troubleshoot-vm-snapshot-issues"></a>對 VM 快照集問題進行疑難排解

VM 備份仰賴發給底層儲存體的快照命令。 無法存取儲存體或快照集工作執行上的延遲，可能會造成備份作業失敗。 下列狀況可能導致快照集工作失敗：

* **已設定 SQL Server 備份的 VM 可能會造成快照集工作延遲**。 根據預設，VM 備份會在 Windows VM 上建立 VSS 完整備份。 執行 SQL Server 並已設定 SQL Server 備份的 VM，可能會遇到快照集延遲。 如果快照集延遲會導致備份失敗，請設定下列登錄機碼：

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **所報告的 VM 狀態不正確，因為 VM 已在 RDP 中關機**。 如果您使用遠端桌面來將虛擬機器關機，請確認該 VM 在入口網站中的狀態是否正確。 如果狀態不正確，請使用入口網站 VM 儀表板中的 [關機] 選項來關閉 VM。
* **如果有超過四個 VM 共用相同的雲端服務，請將 VM 分散到多個備份原則上**。 錯開備份時間，來讓同一時間開始的 VM 備份不超過四個。 請嘗試讓原則中的開始時間至少錯開一小時。
* **VM 執行會使用大量 CPU 或記憶體資源**。 若虛擬機器執行時的記憶體或 CPU 使用量非常高 (>90%)，快照集工作會被排入佇列並延遲。 最後它會逾時。如果發生此問題，請嘗試進行隨選備份。

## <a name="networking"></a>網路功能

必須在來賓內啟用 DHCP，IaaS VM 備份才能運作。 如果您需要靜態私人 IP，請透過 Azure 入口網站或 PowerShell 加以設定。 確定 VM 內的 DHCP 選項已啟用。
取得如何透過 PowerShell 設定靜態 IP 的詳細資訊：

* [如何將靜態內部 IP 位址新增至現有的 VM](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [針對指派至網路介面的私人 IP 位址變更配置方法](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)