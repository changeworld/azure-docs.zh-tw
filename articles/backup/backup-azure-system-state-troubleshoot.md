---
title: 對系統狀態備份進行疑難排解
description: 在本文中，您將瞭解如何針對內部部署 Windows 伺服器的系統狀態備份問題進行疑難排解。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 56593176e705176b87cf955eb116909c1912e723
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824265"
---
# <a name="troubleshoot-system-state-backup"></a>對系統狀態備份進行疑難排解

本文說明您在使用系統狀態備份時可能會遇到的問題解決方案。

## <a name="basic-troubleshooting"></a>基本疑難排解

建議您在開始對系統狀態備份進行疑難排解之前，先執行下列驗證步驟：

- [確定 Microsoft Azure 復原服務 (MARS) 代理程式是最新版本](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [確保 MARS 代理程式和 Azure 之間具有網路連線能力](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- 確保 Microsoft Azure 復原服務正在執行中 (在服務主控台中)。 如有必要，請重新開機，然後重試作業
- [確保草稿資料夾位置具有 5-10% 的磁碟區空間可供使用](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [檢查是否有其他程序或防毒軟體在干擾 Azure 備份](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [已排程的備份失敗，但可以手動備份](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- 確保您的作業系統已更新到最新版本
- [從備份中排除不支援屬性的不支援磁片磁碟機和檔案](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 確保受保護系統上的**系統時鐘**已設定為正確的時區 <br>
- [確定伺服器至少具有 .Net Framework 4.5.2 版和更新版本](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 如果您嘗試將 **伺服器重新註冊** 至保存庫，則： <br>
  - 確定已在伺服器上卸載代理程式，且該代理程式已從入口網站中刪除 <br>
  - 使用與一開始登錄伺服器時所用的相同複雜密碼 <br>
- 如果這是離線備份，請確定 Azure PowerShell 版本3.7.0 已安裝在來源和複製電腦上，然後再開始離線備份作業
- [當備份代理程式在 Azure 虛擬機器上執行時的考慮](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>限制

- Microsoft 不建議使用系統狀態復原來復原到不同的硬體
- 系統狀態備份目前支援「內部部署」的 Windows 伺服器。 這是 Azure Vm 無法使用的功能。

## <a name="prerequisites"></a>先決條件

在使用 Azure 備份進行系統狀態備份的疑難排解之前，請先執行下列必要條件檢查。  

### <a name="verify-windows-server-backup-is-installed"></a>確認已安裝 Windows Server Backup

確定已在伺服器中安裝並啟用 Windows Server Backup。 若要檢查安裝狀態，請執行下列 PowerShell 命令：

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

如果輸出顯示 **安裝狀態** 為 [ **可用**]，則表示 Windows server backup 功能可用於安裝，但不會安裝在伺服器上。 但是，如果未安裝 Windows Server Backup，請使用下列其中一種方法來進行安裝。

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>方法1：使用 PowerShell 安裝 Windows Server Backup

若要使用 PowerShell 安裝 Windows Server Backup，請執行下列命令：

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>方法2：使用伺服器管理員安裝 Windows Server Backup

若要使用伺服器管理員安裝 Windows Server Backup，請執行下列步驟：

1. 在 [ **伺服器**管理員] 中，按一下 [ **新增角色及功能**]。 [ **新增角色及功能] wizard** 隨即出現。

    ![儀表板](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. 選取 [ **安裝類型** ]，然後按 **[下一步]**。

    ![安裝類型](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 從伺服器集區選取伺服器，然後按 **[下一步]**。 在 [伺服器角色] 中，保留預設選取專案，然後按 **[下一步]**。
4. 選取 [**功能**] 索引標籤**Windows Server Backup** ，然後按 **[下一步**]。

    ![選取功能視窗](./media/backup-azure-system-state-troubleshoot/features.png)

5. 在 [ **確認** ] 索引標籤中，按一下 [ **安裝** ] 開始安裝程式。
6. 在 [ **結果** ] 索引標籤中，它會顯示已成功安裝在 Windows Server 上的 Windows Server Backup 功能。

    ![安裝結果](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>系統磁碟區資訊許可權

確定本機系統具有在安裝 Windows 的磁片區中，[ **系統磁碟區資訊** ] 資料夾的 [完整控制權]。 這通常是 **C:\System 的磁片區資訊**。 如果上述許可權未正確設定，則 Windows Server backup 可能會失敗。

### <a name="dependent-services"></a>相依服務

請確定下列服務處於 [執行中] 狀態：

**服務名稱** | **啟動類型**
--- | ---
 (RPC) 的遠端程序呼叫 | 自動
COM + 事件系統 (EventSystem)  | 自動
系統事件通知服務 (SENS)  | 自動
 (VSS) 的磁片區陰影複製 | 手動
Microsoft 軟體陰影複製提供者 (SWPRV)  | 手動

### <a name="validate-windows-server-backup-status"></a>驗證 Windows Server Backup 狀態

若要驗證 Windows Server Backup 狀態，請執行下列步驟：

- 確定 WSB PowerShell 正在執行

  - `Get-WBJob`從提高許可權的 PowerShell 執行，並確定它不會傳回下列錯誤：

    > [!WARNING]
    > Get-wbjob：詞彙 ' Get-wbjob ' 無法辨識為 Cmdlet、函式、指令檔或可執行程式的名稱。 請檢查名稱拼字，如果名稱含有路徑，請確認路徑正確，然後再試一次。

    - 如果因此錯誤而失敗，請在伺服器電腦上重新安裝 Windows Server Backup 功能，如必要條件的步驟1中所述。

  - 從提升許可權的命令提示字元執行下列命令，以確定 WSB 備份正常運作：

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >以您要用來存放系統狀態備份映射之磁片區的磁碟機號取代 X。

    - 從提高許可權的 PowerShell 執行命令以定期檢查作業的狀態 `Get-WBJob`
    - 備份作業完成後，請執行命令以檢查作業的最終狀態 `Get-WBJob -Previous 1`

如果作業失敗，它會指出會導致 MARS 代理程式系統狀態備份失敗的 WSB 問題。

## <a name="common-errors"></a>常見錯誤

### <a name="vss-writer-timeout-error"></a>VSS 寫入器逾時錯誤

| 徵狀 | 原因 | 解決方案
| -- | -- | --
| -MARS 代理程式失敗，並出現錯誤訊息：「WSB 作業失敗，發生 VSS 錯誤。 檢查 VSS 事件記錄檔以解決失敗狀況」<br/><br/> -VSS 應用程式事件記錄檔中出現下列錯誤記錄檔：「VSS 寫入器已拒絕事件，發生錯誤0x800423f2，寫入器的超時時間已在凍結和解除凍結事件之間過期。」| VSS 寫入器無法及時完成，因為電腦上缺少 CPU 和記憶體資源 <br/><br/> 另一個備份軟體已在使用 VSS 寫入器，因此無法完成此備份的結果快照集操作 | 請等待系統上的 CPU/記憶體釋放，或中止佔用太多記憶體/CPU 的進程，然後再次嘗試操作。 <br/><br/>  等候進行中的備份完成，並在電腦上沒有執行任何備份時，稍後再嘗試操作。

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>磁碟空間不足，無法成長陰影複製

| 徵狀 | 解決方法
| -- | --
| -MARS 代理程式失敗，並出現錯誤訊息：備份失敗，因為陰影複製磁片區無法成長，因為磁片區上的磁碟空間不足，因為磁片區包含系統檔案 <br/><br/> -在 volsnap 系統事件記錄檔中出現下列錯誤/警告記錄：「磁片區 C 上的磁碟空間不足：若要為 C 的陰影複製成長陰影複製存放裝置：由於此失敗，磁片區 C：的陰影複製有可能被刪除」 | -釋放事件記錄檔中反白顯示的磁片區空間，讓陰影複製有足夠的空間可在備份進行時成長 <br/><br/> -設定陰影複製空間時，可以限制陰影複製所使用的空間量。 如需詳細資訊，請參閱這篇[文章](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>EFI 磁碟分割已鎖定

| 徵狀 | 解決方法
| -- | --
| MARS 代理程式失敗，並出現錯誤訊息：「系統狀態備份失敗，因為 EFI 系統磁碟分割已鎖定。 這可能是由協力廠商安全性或備份軟體的系統磁碟分割存取所致」 | -如果問題是由協力廠商安全性軟體所造成，則您需要洽詢防毒軟體廠商，讓他們可以允許 MARS 代理程式 <br/><br/> -如果協力廠商備份軟體正在執行，請等候它完成，然後重試一次

## <a name="next-steps"></a>後續步驟

- 如需有關 Resource Manager 部署中的 Windows 系統狀態的詳細資訊，請參閱 [備份 Windows Server 系統狀態](backup-azure-system-state.md)
