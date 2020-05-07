---
title: 針對代理程式和延伸模組問題進行疑難排解
description: 與代理程式、延伸模組及磁碟相關之 Azure 備份失敗的徵狀、原因和解決方案。
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 4f87f2de3747f55562d3f683e1738595624940dd
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854642"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>針對 Azure 備份失敗進行疑難排解：與代理程式或延伸模組相關的問題

本文提供疑難排解步驟，協助您解決與 VM 代理程式和延伸模組通訊相關的 Azure 備份錯誤。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM 代理程式無法與 Azure 備份通訊

**錯誤碼**： UserErrorGuestAgentStatusUnavailable <br>
**錯誤訊息**： VM 代理程式無法與 Azure 備份通訊<br>

Azure VM 代理程式可能已停止、過期、處於不一致的狀態，或未安裝。 這些狀態會防止 Azure 備份服務觸發快照集。

- **開啟 Azure 入口網站 > vm > 設定 > [內容] 窗格**，> 確定 VM**狀態**為 [執行中] **，且****代理程式狀態**為 [**就緒**]。 如果 VM 代理程式已停止或處於不一致的狀態，請重新開機代理程式<br>
  - 針對 Windows Vm，請遵循下列[步驟](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)來重新開機來賓代理程式。<br>
  - 針對 Linux Vm，請遵循下列[步驟](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)來重新開機來賓代理程式。
- **開啟 Azure 入口網站 > VM > 設定 > 延伸**模組 > 確保所有延伸模組都處於布建**成功**狀態。 如果沒有，請遵循下列[步驟](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state)來解決問題。

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - 無法與 VM 代理程式通訊來取得快照集狀態

**錯誤碼**： GuestAgentSnapshotTaskStatusError<br>
**錯誤訊息**：無法與 VM 代理程式通訊來取得快照集狀態 <br>

在您註冊並排程 Azure 備份服務的 VM 之後，備份會藉由與 VM 備份擴充功能通訊來啟動作業，以取得時間點快照集。 下列任一種狀況都可能會阻止觸發快照集。 如果未觸發快照集，可能會發生備份失敗。 請依照列出的順序完成下列疑難排解步驟，然後重試作業：  

**原因 1：[代理程式已安裝到 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**原因 2︰[VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**原因 3︰[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**原因4：[未設定 VM 代理程式設定選項（適用于 Linux vm）](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed-VM 處於失敗的布建狀態

**錯誤碼**： UserErrorVmProvisioningStateFailed<br>
**錯誤訊息**： VM 處於失敗的布建狀態<br>

當其中一個延伸模組失敗使 VM 進入「布建失敗」狀態時，就會發生此錯誤。<br>**開啟 Azure 入口網站 > VM > 設定 > >** 延伸模組的狀態，並檢查所有延伸模組是否處於布建**成功**狀態。 若要深入瞭解，請參閱布建[狀態](https://docs.microsoft.com/azure/virtual-machines/windows/states-lifecycle#provisioning-states)。

- 如果 VMSnapshot 延伸模組處於失敗狀態，請以滑鼠右鍵按一下失敗的延伸模組，並將它移除。 觸發隨選備份。 此動作會重新安裝延伸模組，並執行備份作業。  <br>
- 如果任何其他延伸模組處於失敗狀態，則可能會干擾備份。 請確定已解決這些延伸模組問題，然後重試備份操作。
- 如果 VM 布建狀態為「正在更新」狀態，它可能會干擾備份。 請確定其狀況良好，然後重試備份操作。

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - 已達到還原點集合上限

**錯誤碼**：UserErrorRpCollectionLimitReached <br>
**錯誤訊息**：已達到還原點集合上限。 <br>

- 如果復原點資源群組的鎖定導致無法自動清除復原點，就會發生此問題。
- 如果每日觸發多個備份，也會發生此問題。 目前建議每天只進行一次備份，因為在每個設定的快照集保留期間，立即還原點會保留1-5 天，而且在任何指定時間內，只有18個立即 Rp 可以與 VM 相關聯。 <br>
- 跨還原點集合和 VM 的資源群組的還原點數目不能超過18個。 若要建立新的還原點，請刪除現有的還原點。

建議的動作：<br>
若要解決此問題，請移除 VM 資源群組的鎖定，並重試此作業以觸發清除動作。
> [!NOTE]
> 備份服務會建立與 VM 資源群組不同的資源群組，來儲存還原點集合。 建議客戶請勿鎖定建立給備份服務使用的資源群組。 備份服務建立的資源群組命名格式為：AzureBackupRG_`<Geo>`_`<number>` 例如：AzureBackupRG_northeurope_1

**步驟 1：[從還原點資源群組中移除鎖定](#remove_lock_from_the_recovery_point_resource_group)** <br>
**步驟 2：[清除還原點集合](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - 備份沒有足夠的金鑰保存庫權限來備份加密的 VM

**錯誤碼**：UserErrorKeyvaultPermissionsNotConfigured <br>
**錯誤訊息**：備份沒有足夠的金鑰保存庫權限可以進行加密 VM 的備份。 <br>

若要在加密的 Vm 上成功執行備份作業，它必須具有存取金鑰保存庫的許可權。 您可以透過[Azure 入口網站](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)或透過[PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)來設定許可權。

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - 快照集作業因虛擬機器沒有網路連線而失敗

**錯誤碼**： ExtensionSnapshotFailedNoNetwork<br>
**錯誤訊息**：因為虛擬機器沒有網路連線，所以快照集作業失敗<br>

在您註冊並排程 Azure 備份服務的 VM 之後，備份會藉由與 VM 備份擴充功能通訊來啟動作業，以取得時間點快照集。 下列任一種狀況都可能會阻止觸發快照集。 如果未觸發快照集，可能會發生備份失敗。 完成下列疑難排解步驟，然後再次嘗試操作：

**[無法抓取快照集狀態，或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks-VMSnapshot 延伸模組作業失敗

**錯誤碼**： ExtensionOperationFailedForManagedDisks <br>
**錯誤訊息**：VMSnapshot 延伸模組作業失敗<br>

在您註冊並排程 Azure 備份服務的 VM 之後，備份會藉由與 VM 備份擴充功能通訊來啟動作業，以取得時間點快照集。 下列任一種狀況都可能會阻止觸發快照集。 如果未觸發快照集，可能會發生備份失敗。 請依照列出的順序完成下列疑難排解步驟，然後重試作業：  
**原因 1︰[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2：[代理程式已安裝到 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 3︰[VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - 備份失敗，發生內部錯誤

**錯誤碼**：BackUpOperationFailed / BackUpOperationFailedV2 <br>
**錯誤訊息**：備份因為內部錯誤而失敗 - 請在幾分鐘內重試此作業 <br>

在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 備份擴充功能通訊以取得時間點快照，來起始作業。 下列任一種狀況都可能會阻止觸發快照集。 如果未觸發快照集，可能會發生備份失敗。 請依照列出的順序完成下列疑難排解步驟，然後重試作業：  
**原因 1：[代理程式已安裝到 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 2︰[VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3︰[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因4：[備份服務因為資源群組鎖定而沒有刪除舊還原點的許可權](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize-目前不支援設定的磁片大小 Azure 備份

**錯誤碼**：UserErrorUnsupportedDiskSize <br>
**錯誤訊息**： Azure 備份目前不支援已設定的磁片大小。 <br>

當備份磁片大小大於 32 TB 的 VM 時，您的備份操作可能會失敗。 此外，目前不支援大小大於 4 TB 的加密磁片備份。 藉由分割磁片，確定磁片大小小於或等於支援的限制值。

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - 無法起始備份，因為另一個備份作業正在進行中

**錯誤碼**： UserErrorBackupOperationInProgress <br>
**錯誤訊息**：無法起始備份，因為另一個備份作業目前正在進行中<br>

您最近的備份作業失敗，因為有現有的備份作業正在進行中。 必須等到目前的作業完成，才能啟動新的備份作業。 請確定目前正在進行的備份作業已完成，再觸發或排定其他備份作業。 若要檢查備份作業狀態，請執行下列步驟：

1. 登入 Azure 入口網站，按一下 [**所有服務**]。 輸入「復原服務」，然後按一下 [復原服務保存庫]****。 復原服務保存庫清單隨即出現。
2. 在復原服務保存庫清單中，選取其中已設定備份的保存庫。
3. 在 [保存庫儀表板] 功能表中，按一下 [備份作業]**** 以顯示所有備份作業。
   - 如果有正在進行中的備份作業，請等到該作業完成或取消備份作業。
     - 若要取消備份作業，請以滑鼠右鍵按一下備份作業，然後按一下 [**取消**] 或使用[PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)。
   - 如果您已在不同的保存庫中重新設定備份，請確定舊的保存庫中沒有正在執行的備份作業。 如果存在，則取消備份作業。
     - 若要取消備份作業，請以滑鼠右鍵按一下備份作業，然後按一下 [**取消**] 或使用[PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. 請重試備份作業。

如果排定的備份作業花費較長的時間，與下一個備份設定發生衝突，則請參閱[最佳作法](backup-azure-vms-introduction.md#best-practices)、[備份效能](backup-azure-vms-introduction.md#backup-performance)和[還原考慮](backup-azure-vms-introduction.md#backup-and-restore-considerations)。

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError-備份因錯誤而失敗。 如需詳細資訊，請參閱作業錯誤訊息詳細資料

**錯誤碼**： UserErrorCrpReportedUserError <br>
**錯誤訊息**：備份因錯誤而失敗。 如需詳細資訊，請參閱作業錯誤訊息詳細資料。

此錯誤是從 IaaS VM 回報。 若要找出問題的根本原因，請移至復原服務保存庫設定。 在 [**監視**] 區段下，選取 [**備份作業**] 以篩選並查看狀態。 按一下 [**失敗**]，以查看基礎錯誤訊息詳細資料。 根據 [錯誤詳細資料] 頁面中的建議採取進一步的動作。

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent-備份失敗：這部虛擬機器不受 Azure 備份保護（主動）

**錯誤碼**： UserErrorBcmDatasourceNotPresent <br>
**錯誤訊息**：備份失敗：此虛擬機器不是（主動）受到 Azure 備份保護。

請檢查指定的虛擬機器是否已受到 Azure 備份保護（不在暫停狀態）。 若要解決此問題，請確定虛擬機器正在使用中，然後再次嘗試操作。

## <a name="causes-and-solutions"></a>原因和解決方案

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)

#### <a name="solution"></a>解決方法

VM 代理程式可能已損毀，或服務可能已停止。 重新安裝 VM 代理程式有助於取得最新版本。 也有助於重新開始與服務通訊。

1. 判斷 Windows Azure 客體代理程式服務是否在 VM 服務 (services.msc) 中執行。 請嘗試重新啟動 Windows Azure 客體代理程式服務並啟動備份。
2. 如果 Windows Azure 客體代理程式服務未顯示在控制台的服務中，請移至 [程式和功能]**** 來判斷是否已安裝 Windows Azure 客體代理程式服務。
3. 如果 Windows Azure 客體代理程式顯示在 [程式和功能]**** 中，請將 Windows Azure 客體代理程式解除安裝。
4. 下載並安裝[最新版的代理程式 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您必須擁有系統管理員權限，才能完成安裝。
5. 確認 Windows Azure 客體代理程式服務顯示在服務中。
6. 執行隨選備份：
   - 在入口網站中，選取 [立即備份]****。

此外，確認 VM 中[已安裝 Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)。 VM 代理程式需有 .NET 4.5 才能與服務通訊。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 中安裝的代理程式已過時 (適用於 Linux VM)

#### <a name="solution"></a>解決方法

針對 Linux VM，與代理程式或擴充功能相關的多數失敗是由於會影響過時 VM 代理程式的問題所造成。 若要對此問題進行疑難排解，請遵循下列一般方針：

1. 請遵循[更新 Linux VM 代理程式](../virtual-machines/linux/update-agent.md)的指示。

   > [!NOTE]
   > 我們強烈建議** 您只透過散發套件存放庫更新代理程式。 我們不建議直接從 GitHub 下載代理程式程式碼，並加以更新。 如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以取得如何進行安裝的指示。 若要檢查最新的代理程式，請移至 GitHub 儲存機制中的 [Microsoft Azure Linux 代理程式 (英文)](https://github.com/Azure/WALinuxAgent/releases) 頁面。

2. 執行下列命令，確定 Azure 代理程式正在 VM 上執行：`ps -e`

   如果此程序不在執行中，請使用下列命令來重新啟動它：

   - 針對 Ubuntu：`service walinuxagent start`
   - 針對其他散發套件︰`service waagent start`

3. [設定自動重新啟動代理程式](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 執行新的測試備份。 如果失敗持續發生，請從 VM 收集下列記錄：

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

如果您需要 waagent 的詳細資訊記錄，請遵循下列步驟：

1. 在 /etc/waagent.conf 檔案中，找出下一行︰**Enable verbose logging (y|n)**
2. 將 **Logs.Verbose** 值從 *n* 變更為 *y*。
3. 儲存變更，然後完成本節前面所述的步驟來重新啟動 waagent。

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>未設定 VM 代理程式設定選項（適用于 Linux Vm）

組態檔 (/etc/waagent.conf) 控制 waagent 的動作。 設定檔案選項**延伸。 [啟用**] 應設定為 [ **y** ] 和 [布建] **。代理程式**應設定為 [**自動**]，備份才能正常執行。
如需 VM 代理程式設定檔案選項的完整清單，請參閱<https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>無法擷取快照集狀態或無法取得快照集

VM 備份仰賴發給底層儲存體帳戶的快照命令。 備份可能會失敗，因為它無權存取儲存體帳戶，或是因為快照集工作延遲執行。

#### <a name="solution"></a>解決方法

下列狀況可能導致快照集工作失敗：

| 原因 | 解決方法 |
| --- | --- |
| 因為遠端桌面通訊協定 (RDP) 中的 VM 關機，而導致報告的 VM 狀態不正確。 | 如果您關閉 RDP 中的 VM，請檢查入口網站，以判斷 VM 狀態是否正確。 如果不正確，請在入口網站中使用 VM 儀表板上的 [**關機**] 選項來關閉 vm。 |
| VM 無法從 DHCP 取得主機或網狀架構位址。 | 必須在來賓內啟用 DHCP，IaaS VM 備份才能運作。 如果 VM 無法從 DHCP 回應 245 取得主機或網狀架構位址，則無法下載或執行任何延伸模組。 如果您需要靜態私人 IP，則應透過**Azure 入口網站**或**PowerShell**加以設定，並確定已啟用 VM 內的 DHCP 選項。 [深入瞭解](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)如何使用 PowerShell 設定靜態 IP 位址。

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>從還原點資源群組中移除鎖定

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 移至 [**所有資源] 選項**，以下列格式選取還原點集合資源群組 AzureBackupRG_`<Geo>`_`<number>`。
3. 在 [設定]**** 區段中，選取 [鎖定]**** 來顯示鎖定項目。
4. 若要移除鎖定，請選取省略符號，然後按一下 [刪除]****。

    ![刪除鎖定](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>清除還原點集合

移除鎖定之後，必須清除還原點。

如果您刪除 VM 的資源群組或 VM 本身，受控磁片的立即還原快照集會維持作用中狀態，並會根據保留集而過期。 若要刪除儲存在還原點集合中的立即還原快照集（如果不需要的話），請根據以下所述的步驟來清除還原點集合。

若要清除還原點，請遵循下列任一方法：<br>

- [藉由執行隨選備份來清除還原點集合](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [從 Azure 入口網站清除還原點集合](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>藉由執行隨選備份來清除還原點集合

移除鎖定之後，請觸發隨選備份。 此動作可確保還原點會自動清除。 預期此隨選作業會第一次失敗;不過，它會確保自動清除，而不是手動刪除還原點。 清除之後，您的下一個排定的備份應該會成功。

> [!NOTE]
> 在觸發隨選備份的幾個小時後，將會自動清除。 如果您排定的備份仍然失敗，請使用[此處](#clean-up-restore-point-collection-from-azure-portal)列出的步驟，嘗試手動刪除還原點集合。

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>從 Azure 入口網站清除還原點集合 <br>

若要手動清除因為資源群組鎖定而未清除的還原點集合，請嘗試下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞]**** 功能表上按一下 [所有資源]****，選取下列格式的資源群組：AzureBackupRG_`<Geo>`_`<number>`，也就是您 VM 所在的位置。

    ![刪除鎖定](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. 按一下 [資源群組]，即會顯示 [**總覽**] 窗格。
4. 選取 [顯示隱藏的類型]**** 選項，以顯示所有隱藏的資源。 選取下列格式的還原點集合：AzureBackupRG_`<VMName>`_`<number>`。

    ![刪除鎖定](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. 按一下 [**刪除**] 以清除還原點集合。
6. 重試備份作業。

> [!NOTE]
 >如果資源（RP 集合）有大量的還原點，則從入口網站刪除它們可能會超時並失敗。 這是已知的 CRP 問題，其中所有還原點不會在規定的時間刪除而作業會逾時；不過，刪除作業通常在 2 或 3 次重試之後就會成功。
