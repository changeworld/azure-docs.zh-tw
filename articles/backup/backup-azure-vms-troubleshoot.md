---
title: 使用 Azure VM 排除備份錯誤
description: 在本文中,瞭解如何解決 Azure 虛擬機器備份和還原時遇到的錯誤。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 019c27b1f7e8560c86252aaf2ed1fb79df2439fa
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677347"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>解決 Azure 虛擬機器上的備份故障

您可以使用 Azure 備份對以下資訊中遇到的錯誤進行故障排除:

## <a name="backup"></a>Backup

本節介紹 Azure 虛擬機器的備份操作故障。

### <a name="basic-troubleshooting"></a>基本疑難排解

* 確保 VM 代理程式 (WA 代理) 是[最新版本](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)。
* 確保支援 Windows 或 Linux VM 作業系統版本,請參閱[IaaS VM 備份支援矩陣](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)。
* 驗證另一個備份服務未運行。
  * 為了確保沒有快照延伸問題,[請卸載延伸以強制重新載入,然後重試備份](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout)。
* 驗證 VM 是否具有網路連接。
  * 確保另一個備份服務未運行。
* 從`Services.msc`,確保**Windows Azure 來賓代理**服務**執行**。 如果**缺少 Windows Azure 來賓代理**服務,請從[恢復服務保管庫中的備份 Azure VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)安裝該服務。
* **事件日誌**可能會顯示來自其他備份產品的備份失敗,例如 Windows 伺服器備份,並且不是由於 Azure 備份造成的。 使用以下步驟確定問題是否與 Azure 備份一起:
  * 如果事件源或消息中的條目**備份**出錯,請檢查 Azure IaaS VM 備份備份是否成功,以及是否使用所需的快照類型創建了還原點。
  * 如果 Azure 備份工作,則另一個備份解決方案可能會出現問題。
  * 下面是事件檢視器錯誤 517 的範例,其中 Azure 備份工作正常,但「Windows 伺服器備份」失敗:<br>
    ![Windows 伺服器備份失敗](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * 如果 Azure 備份失敗,則在本文中的"常見 VM 備份錯誤"部分中查找相應的錯誤代碼。

## <a name="common-issues"></a>常見問題

以下是 Azure 虛擬機器上備份失敗的常見問題。

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>從 BackBackVault 執行時複製 VHD - 從逾時的保管庫複製備份的資料

錯誤代碼:從備份複製 VHD,以長時間使用 <br/>
錯誤訊息:從保管庫超時複製備份的數據

這可能是由於瞬態存儲錯誤或存儲帳戶 IOPS 不足,以便備份服務在超時期間將數據傳輸到保管庫。 使用這些[最佳實務](backup-azure-vms-introduction.md#best-practices)配置 VM 備份,然後重試備份操作。

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>使用者ErrorVmNot理想狀態 - VM 未處於允許備份的狀態

錯誤代碼:使用者錯誤VmNot理想狀態 <br/>
錯誤消息:VM 未處於允許備份的狀態。<br/>

備份操作失敗,因為 VM 處於"失敗"狀態。 若要成功完成備份，VM 的狀態應該處於「執行中」、「已停止」或「已停止 (已解除配置)」。

* 如果 VM 處於 [正在執行]**** 和 [關機]**** 之間的暫時性狀態，請等候狀態變更。 然後再觸發備份作業。
* 如果 VM 是 Linux VM 並使用安全性強化的 Linux 核心模組，請從安全性原則中排除 Azure Linux 代理程式路徑 **/var/lib/waagent**，並確定已安裝備份延伸模組。

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreeze 失敗 ─ 無法凍結 VM 的一個或多個載入點以取得檔案系統一致的快照

錯誤代碼:使用者錯誤FsFreeze失敗 <br/>
錯誤消息:無法凍結 VM 的一個或多個裝載點以獲取文件系統一致的快照。

* 使用**umount**命令卸載未清除檔案系統狀態的設備。
* 使用**fsck**命令在這些設備上運行檔案系統一致性檢查。
* 再次安裝設備並重試備份操作。</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>延伸快照失敗COM / 延伸安裝失敗COM / 延伸安裝失敗MDTC - 延伸安裝/操作失敗,由於 COM+ 錯誤

錯誤代碼:延伸快照失敗COM <br/>
錯誤訊息:由於 COM+ 錯誤,快照操作失敗

錯誤代碼:延伸失敗COM  <br/>
錯誤訊息:由於 COM+ 錯誤,擴展安裝/操作失敗

錯誤代碼:延伸失敗MDTC <br/>
錯誤訊息:延伸安裝失敗,錯誤「COM+ 無法與 Microsoft 分散式事務協調員交談 <br/>

由於 Windows 服務**COM+ 系統**應用程式出現問題,備份操作失敗。  若要解決此問題，請依照下列步驟執行︰

* 嘗試啟動/重新啟動 Windows 服務**COM+ 系統應用程式**(從提升的命令提示 **- 網路啟動 COMSysApp**)。
* 確保**分散式事務協調器**服務作為**網路服務**帳戶運行。 如果沒有,請將其更改為作為**網路服務**帳號執行,然後重新啟動**COM+ 系統應用程式**。
* 如果無法重新啟動服務,則通過以下步驟重新安裝**分散式事務協調器**服務:
  * 停止 MSDTC 服務
  * 開啟命令提示字元 (cmd)
  * 運行命令"msdtc -卸載"
  * 執行命令"msdtc -安裝"
  * 啟動 MSDTC 服務
* 啟動 Windows 服務 **COM+ System Application**。 **COM+ System Application** 啟動後，請從 Azure 入口網站觸發備份作業。</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>延伸失敗VsSWriterInBadState - 快照操作失敗,因為 VSS 編寫器處於不良狀態

錯誤代碼: 延伸失敗,在巴貝德狀態 <br/>
錯誤消息:快照操作失敗,因為 VSS 編寫器處於不良狀態。

請重新啟動處於不良狀態的 VSS 寫入器。 從提升權限的命令提示字元，執行 ```vssadmin list writers```。 輸出會包含所有 VSS 寫入器和其狀態。 針對每個狀態不是「[1] 穩定」**** 的 VSS 寫入器，請從提升權限的命令提示字元執行下列命令，以重新啟動那些 VSS 寫入器：

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>延伸剖析失敗 - 分析備份延伸的設定失敗

錯誤代碼:延伸設定剖析失敗<br/>
錯誤訊息:分析備份擴展的配置失敗。

此錯誤是因為 **MachineKeys** 目錄上的權限已變更︰**%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**。
執行以下指令並驗證**MachineKeys**目錄上的權限是否為預設權限 **:icacls %系統驅動器%\程式資料\microsoft\sa_電腦金鑰**。

預設的權限如下︰

* 所有人: (R,W)
* 建築管理員: (F)

如果您發現 **MachineKeys** 目錄中的權限不同於預設權限，請依照下列步驟來修正權限，刪除憑證，並觸發備份：

1. 修正 **MachineKeys** 目錄上的權限。 在目錄中使用 Explorer 安全性屬性和進階安全性設定，來將權限重設為預設值。 從目錄中移除所有使用者物件 (預設值除外)，然後確定 [所有人]**** 權限有下列特殊存取權︰

   * 列出資料夾/讀取資料
   * 讀取屬性
   * 讀取擴充屬性
   * 建立檔案/寫入資料
   * 建立資料夾/附加資料
   * 寫入屬性
   * 寫入擴充屬性
   * 讀取權限
2. 刪除 **''** 是傳統部署模型或**Windows Azure CRP 憑證產生器**的憑證:

   * [在本機電腦主控台上開啟憑證](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) \(機器翻譯\)。
   * 在 **『個人** > **憑證』** 下,刪除 **「頒發到**」是經典部署模型或**Windows Azure CRP 憑證產生器**的所有證書。
3. 觸發 VM 備份作業。

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>延伸StuckInDeletionstate - 延伸狀態不支援備份操作

錯誤代碼:延伸Stuckin移除狀態 <br/>
錯誤訊息:延伸狀態不支援備份操作

由於備份擴展狀態不一致,備份操作失敗。 若要解決此問題，請依照下列步驟執行︰

* 確認客體代理程式已安裝且可回應
* 從 Azure 門戶轉到**虛擬機器** > **所有設定** > **擴充**
* 選取備份擴充 VmSnapshot 或 VmSnapshotLinux，然後按一下 [解除安裝]****
* 刪除備份延伸後,重試備份操作
* 後續的備份作業將會以所需狀態安裝新的擴充

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>延伸失敗快照有限達到錯誤 - 快照操作失敗,因為某些連線的磁碟的快照限制被超出

錯誤代碼:延伸失敗快照限制到錯誤 <br/>
錯誤消息:由於某些連接的磁碟超出快照限制,快照操作失敗

快照操作失敗,因為已超出所連接的某些磁碟的快照限制。 完成以下故障排除步驟,然後重試操作。

* 刪除不需要的磁碟 blob 快照。 小心不要刪除磁碟 blob,只應刪除快照 blob。
* 如果在 VM 磁碟儲存帳戶上啟用了軟刪除,請配置軟刪除保留,以便現有快照小於任何時間點允許的最大值。
* 如果在備份的 VM 中啟用了 Azure 網站恢復,則執行以下步驟:

  * 確保**isanysnapshot 失敗**的值在 /etc/azure/vmbackup.conf 中設定為 false
  * 安排 Azure 網站恢復在不同的時間,以便它不會與備份操作衝突。

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>延伸失敗逾時VM網路無回應 - 快照操作由於 VM 資源不足而失敗

錯誤代碼:延伸失敗逾時VM網路無回應<br/>
錯誤消息:由於 VM 資源不足,快照操作失敗。

由於執行快照操作時網路呼叫延遲,VM 上的備份操作失敗。 若要解決此問題，請執行步驟 1。 如果問題持續發生，請嘗試步驟 2 和 3。

**步驟 1:** 透過主機建立快照

從提升權限的 (系統管理) 命令提示字元中，執行下列命令：

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

這可確保快照集會透過主機 (而非客體資源) 取得。 請重試備份作業。

**第二步**:嘗試將備份計劃更改為 VM 負載不足(CPU/IOps 等)

**步驟 3:** 嘗試[增加 VM 的大小](https://azure.microsoft.com/blog/resize-virtual-machines/)並重試操作

## <a name="common-vm-backup-errors"></a>常見的 VM 備份錯誤

| 錯誤詳細資料 | 因應措施 |
| ------ | --- |
| **錯誤代碼**: 320001, 資源找不到 <br/> **錯誤消息**:無法執行操作,因為 VM 不再存在。 <br/> <br/> **錯誤代碼**: 400094, BCMV2VM 未發現 <br/> **錯誤訊息**:虛擬機器不存在 <br/> <br/>  找不到 Azure 虛擬機器。  |此錯誤會在已刪除主要 VM，但備份原則仍然在尋找 VM 來備份時發生。 若要修正此錯誤，請遵循下列步驟： <ol><li> 重新建立具有相同名稱和相同資源組名稱、**雲服務名稱**的虛擬機,<br>**或**</li><li> 停止保護虛擬機器 (不論是否刪除備份資料)。 如需詳細資訊，請參閱[停止保護虛擬機器](backup-azure-manage-vms.md#stop-protecting-a-vm)。</li></ol>|
|**錯誤代碼**: 使用者錯誤 BCM 進階儲存配額錯誤<br/> **錯誤訊息**:由於儲存帳戶中的可用空間不足,無法複製虛擬機器的快照 | 針對 VM 備份堆疊 V1 上的進階 VM，我們會將快照集複製到儲存體帳戶。 此步驟是為了確定快照集上運作的備份管理流量不會限制可供使用進階磁碟的應用程式使用的 IOPS 數目。 <br><br>我們建議您僅配置 50% (17.5 TB) 的總儲存體帳戶空間。 然後 Azure 備份服務便可以將快照集複製到儲存體帳戶，並從儲存體帳戶中的這個複製位置將資料傳送到到保存庫。 |
| **錯誤代碼**: 380008, AzureVm 離線 <br/> **錯誤訊息**: 安裝 Microsoft 修復服務延伸失敗,因為虛擬機器未執行 | VM 代理程式是 Azure 復原服務延伸模組的必要條件。 請安裝 Azure 虛擬機器代理程式並重新啟動註冊作業。 <br> <ol> <li>檢查是否已正確安裝 VM 代理程式。 <li>確定已正確設定 VM 設定上的旗標。</ol> 深入了解如何安裝 VM 代理程式，以及如何驗證 VM 代理程式安裝。 |
| **錯誤代碼**: 延伸快照位元鎖錯誤 <br/> **錯誤訊息**: 快照操作失敗與捲影影複製服務 (VSS) 操作錯誤**此驅動器被 BitLocker 驅動器加密鎖定。您必須從控制面板解鎖此驅動器。** |對 VM 上的所有磁碟機關閉 BitLocker，並檢查是否已解決 VSS 問題。 |
| **錯誤代碼**: VmNot 理想狀態 <br/> **錯誤消息**:VM 未處於允許備份的狀態。 |<ul><li>如果 VM 處於 [正在執行]**** 和 [關機]**** 之間的暫時性狀態，請等候狀態變更。 然後再觸發備份作業。 <li> 如果 VM 是 Linux VM 並使用安全性強化的 Linux 核心模組，請從安全性原則中排除 Azure Linux 代理程式路徑 **/var/lib/waagent**，並確定已安裝備份延伸模組。  |
| 虛擬機器上沒有 VM 代理程式： <br>請安裝所有必要條件和 VM 代理程式。 接著請重新啟動作業。 |深入了解 [VM 代理程式安裝，以及如何驗證 VM 代理程式安裝](#vm-agent)。 |
| **錯誤代碼**: 延伸快照失敗 無安全網路 <br/> **錯誤消息**:快照操作失敗,因為無法創建安全的網路通信通道。 | <ol><li> 在提高權限的模式中執行 **regedit.exe**，來開啟登錄編輯程式。 <li> 識別系統中存在的所有 .NET Framework 版本。 它們位於登錄機碼 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** 的階層下。 <li> 針對登錄機碼中的每個 .NET Framework，新增下列機碼︰ <br> **SchUseStrongCrypto"\dword:0000001**。 </ol>|
| **錯誤代碼**: 延伸 VCRedist 安裝失敗 <br/> **錯誤消息**:快照操作失敗,因為無法為 Visual Studio 2012 安裝 Visual C++可重新分發。 | 導航到 C:_包\外掛程式\微軟.Azure.恢復服務.VM快照\代理版本並安裝vcredist2013_x64。<br/>確保允許服務安裝的註冊表項值設置為正確的值。 也就是說,將**HKEY_LOCAL_MACHINE_SYSTEM_CurrentControlSet_服務\Msiserver**中的**起始**值設置為**3,** 而不是**4**。 <br><br>如果您仍遇到安裝問題，請從提高權限的命令提示字元執行 **MSIEXEC /UNREGISTER**，再執行 **MSIEXEC /REGISTER**，以重新啟動安裝服務。  |
| **錯誤代碼**: 使用者錯誤要求透過原則取消 <BR> **錯誤消息**:在 VM 上配置了無效的策略,這阻止了快照操作。 | 如果 Azure 策略[控制環境中的標記](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags),請考慮將策略從[拒絕效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deny)更改為["修改效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)",或者根據[Azure 備份所需的命名架構](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines)手動創建資源組。

## <a name="jobs"></a>工作

| 錯誤詳細資料 | 因應措施 |
| --- | --- |
| 此作業類型不支援取消： <br>請等候作業完成。 |None |
| 此作業未處於可取消的狀態： <br>請等候作業完成。 <br>**或**<br> 選取的作業未處於可取消的狀態： <br>請等候作業完成。 |作業很可能已經快要完成。 請等候作業完成。|
| 備份無法取消作業，因為它並未正在進行： <br>僅支援針對進行中的作業進行取消。 請嘗試取消正在進行的作業。 |此錯誤發生的原因是因為暫時性的狀態。 請稍候再重試取消作業。 |
| 備份無法取消作業： <br>請等候作業完成。 |None |

## <a name="restore"></a>還原

| 錯誤詳細資料 | 因應措施 |
| --- | --- |
| 還原失敗，發生雲端內部錯誤。 |<ol><li>您嘗試還原的雲端服務是使用 DNS 設定所設定。 您可以檢查： <br>**$deployment = 取得 Azure 部署 - 服務名稱"服務名稱' - 插槽"生產' 取得 AzureDns -dns 設定$deployment。DnsSettings**.<br>如果已設定 [位址]****，則 DNS 設定便已設定。<br> <li>您嘗試還原到其中的雲端服務是使用 **ReservedIP** 所設定，而雲端服務中的現有 VM 目前處於停止狀態。 您可以使用下列 PowerShell Cmdlet 來檢查雲端服務是否已保留 IP：**$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**。 <br><li>您嘗試將具有下列特殊網路組態的虛擬機器還原至相同的雲端服務： <ul><li>負載平衡器設定下的虛擬機器，內部與外部。<li>具有多個保留 IP 的虛擬機器。 <li>具有多個 NIC 的虛擬機器。 </ul><li>在 UI 中選取新的雲端服務，或參閱適用於具有特殊網路組態之 VM 的[還原考量](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)。</ol> |
| 選取的 DNS 名稱已有人使用： <br>請指定不同的 DNS 名稱並再試一次。 |此 DNS 名稱是指雲端服務名稱，其結尾通常是 **.cloudapp.net**。 此名稱必須是唯一的。 如果您遇到這個錯誤，您需要在還原期間選擇不同的 VM 名稱。 <br><br>  只有 Azure 入口網站的使用者才會看到這個錯誤。 透過 PowerShell 執行還原作業將會成功，因為它只會還原磁碟，並不會建立 VM。 當您在磁碟還原作業之後明確建立 VM 時，將會遇到此錯誤。 |
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

如果您的備份需要超過 12 小時,或者還原需要超過 6 小時,請查看[最佳實務](backup-azure-vms-introduction.md#best-practices)和效能[注意事項](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM 代理程式

### <a name="set-up-the-vm-agent"></a>設定 VM 代理程式

一般而言，VM 代理程式已存在於從 Azure 資源庫建立的 VM 中。 不過，從內部部署資料中心移轉的虛擬機器並不會安裝 VM 代理程式。 針對那些 VM，必須明確安裝 VM 代理程式。

#### <a name="windows-vms"></a>Windows VM

* 下載並安裝 [代理程式 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您需要有系統管理員權限，才能完成安裝。
* 針對使用傳統部署模型建立的虛擬機器，請[更新 VM 屬性](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) \(英文\) 以指出代理程式已安裝。 Azure Resource Manager 虛擬機器不需要此步驟。

#### <a name="linux-vms"></a>Linux VM

* 從散發套件存放庫安裝最新版的代理程式。 有關套件名稱的詳細資訊,請參閱 Linux[代理儲存庫](https://github.com/Azure/WALinuxAgent)。
* 對於使用經典部署模型創建的 VM,[請更新 VM 屬性](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms)並驗證代理是否已安裝。 資源管理員虛擬機器不需要此步驟。

### <a name="update-the-vm-agent"></a>更新 VM 代理程式

#### <a name="windows-vms"></a>Windows VM

* 若要更新 VM 代理程式，請重新安裝 [VM 代理程式二進位檔](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 更新代理程式之前，請先確定 VM 代理程式更新期間不會進行任何備份作業。

#### <a name="linux-vms"></a>Linux VM

* 要更新 Linux VM 代理,請按照文章「[更新 Linux VM 代理」中的](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)說明進行操作。

    > [!NOTE]
    > 請一律使用散發套件存放庫來更新代理程式。

    請勿從 GitHub 下載代理程式程式碼。 如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以了解如何取得最新的代理程式。 您也可以在 GitHub 存放庫中查看最新的 [Windows Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent/releases)資訊。

### <a name="validate-vm-agent-installation"></a>驗證 VM 代理程式安裝

確認 Windows VM 上的 VM 代理程式版本：

1. 登入 Azure 虛擬機器，然後瀏覽至 C:\WindowsAzure\Packages**** 資料夾。 您應該會找到 **WaAppAgent.exe** 檔案。
2. 請以滑鼠右鍵按一下該檔案，然後移至 [內容]****。 然後選擇「**詳細資訊**」選項卡。**產品版本**欄位應為2.6.1198.718或更高版本。

## <a name="troubleshoot-vm-snapshot-issues"></a>對 VM 快照集問題進行疑難排解

VM 備份仰賴發給底層儲存體的快照命令。 無法存取儲存體或快照集工作執行上的延遲，可能會造成備份作業失敗。 下列狀況可能導致快照集工作失敗：

* **設定 SQL Server 備份的 VM 可能會導致快照工作延遲**。 根據預設，VM 備份會在 Windows VM 上建立 VSS 完整備份。 執行 SQL Server 並已設定 SQL Server 備份的 VM，可能會遇到快照集延遲。 如果快照集延遲會導致備份失敗，請設定下列登錄機碼：

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **所報告的 VM 狀態不正確，因為 VM 已在 RDP 中關機**。 如果您使用遠端桌面來將虛擬機器關機，請確認該 VM 在入口網站中的狀態是否正確。 如果狀態不正確，請使用入口網站 VM 儀表板中的 [關機]**** 選項來關閉 VM。
* **如果超過四個 VM 共用同一個雲端服務,則將 VM 跨多個備份策略展開**。 錯開備份時間，來讓同一時間開始的 VM 備份不超過四個。 請嘗試讓原則中的開始時間至少錯開一小時。
* **VM 執行會使用大量 CPU 或記憶體資源**。 若虛擬機器執行時的記憶體或 CPU 使用量非常高 (>90%)，快照集工作會被排入佇列並延遲。 最終,它超時。如果發生此問題,請嘗試按需備份。

## <a name="networking"></a>網路功能

必須在來賓內啟用 DHCP，IaaS VM 備份才能運作。 如果您需要靜態私人 IP，請透過 Azure 入口網站或 PowerShell 加以設定。 確定 VM 內的 DHCP 選項已啟用。
取得如何透過 PowerShell 設定靜態 IP 的詳細資訊：

* [如何將靜態內部 IP 位址新增至現有的 VM](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
* [針對指派至網路介面的私人 IP 位址變更配置方法](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
