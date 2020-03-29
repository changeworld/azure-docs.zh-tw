---
title: 故障排除系統狀態備份
description: 在本文中，瞭解如何在本地 Windows 伺服器的系統狀態備份中解決問題。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969582"
---
# <a name="troubleshoot-system-state-backup"></a>故障排除系統狀態備份

本文介紹了在使用系統狀態備份時可能會遇到的問題的解決方案。

## <a name="basic-troubleshooting"></a>基本疑難排解

我們建議您在開始對系統狀態備份進行故障排除之前執行以下驗證：

- [確保 Microsoft Azure 恢復服務 （MARS） 代理是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [確保 MARS 代理程式和 Azure 之間具有網路連線能力](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- 確保 Microsoft Azure 復原服務正在執行中 (在服務主控台中)。 如有必要，重新開機並重試操作
- [確保草稿資料夾位置具有 5-10% 的磁碟區空間可供使用](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [檢查是否有其他程序或防毒軟體在干擾 Azure 備份](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [已排程的備份失敗，但可以手動備份](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- 確保您的作業系統已更新到最新版本
- [確保從備份中排除不受支援的磁碟機和具有不支援屬性的檔](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 確保受保護系統上的**系統時鐘**已設定為正確的時區 <br>
- [確定伺服器至少具有 .Net Framework 4.5.2 版和更新版本](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 如果您嘗試將**伺服器重新註冊**到保存庫，則： <br>
  - 確保代理在伺服器上卸載，並且從門戶中刪除 <br>
  - 使用與一開始登錄伺服器時所用的相同複雜密碼 <br>
- 如果這是離線備份，請確保在開始離線備份操作之前在源和複製電腦上安裝 Azure PowerShell 版本 3.7.0
- [備份代理在 Azure 虛擬機器上運行時的注意事項](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>限制

- Microsoft 不建議使用系統狀態復原來復原到不同的硬體
- 系統狀態備份當前支援"本地"Windows 伺服器。 此功能不適用於 Azure VM。

## <a name="prerequisites"></a>Prerequisites

在使用 Azure 備份對系統狀態備份進行故障排除之前，請執行以下先決條件檢查。  

### <a name="verify-windows-server-backup-is-installed"></a>驗證安裝了 Windows 伺服器備份

確保在伺服器中安裝並啟用 Windows 伺服器備份。 要檢查安裝狀態，請運行此 PowerShell 命令：

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

如果輸出顯示 **"安裝狀態****"為可用**，則意味著 Windows Server 備份功能可用於安裝，但未安裝在伺服器上。 但是，如果未安裝 Windows 伺服器備份，請使用以下方法之一安裝它。

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>方法 1：使用 PowerShell 安裝 Windows 伺服器備份

要使用 PowerShell 安裝 Windows 伺服器備份，請運行以下命令：

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>方法 2：使用伺服器管理員安裝 Windows 伺服器備份

要使用伺服器管理員安裝 Windows 伺服器備份，請執行以下步驟：

1. 在 **"伺服器管理員"中**，按一下"**添加角色和功能**"。 將顯示 **"添加角色和功能"嚮導**。

    ![儀表板](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. 選擇**安裝類型**，然後按一下 **"下一步**"。

    ![安裝類型](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 從伺服器池中選擇伺服器，然後按一下 **"下一步**"。 在"伺服器角色"中，保留預設選擇，然後按一下"**下一步**"。
4. 在**功能**中選擇**Windows 伺服器備份**選項卡，然後按一下 **"下一步**"。

    ![特性](./media/backup-azure-system-state-troubleshoot/features.png)

5. 在 **"確認"** 選項卡中，按一下"**安裝**"以啟動安裝過程。
6. 在 **"結果"** 選項卡中，將顯示 Windows 伺服器備份功能已成功安裝在 Windows 伺服器上。

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>系統磁碟區資訊許可權

確保本地系統完全控制位於安裝 Windows 的卷中的 **"系統磁碟區資訊"** 資料夾。 通常這是**C：\系統磁碟區資訊**。 如果上述許可權設置不正確，Windows 伺服器備份可能會失敗

### <a name="dependent-services"></a>從屬服務

確保以下服務處於運行狀態：

**服務名稱** | **啟動類型**
--- | ---
遠端程式呼叫（RPC） | 自動
COM+ 事件系統（事件系統） | 自動
系統事件通知服務（SENS） | 自動
卷影副本（VSS） | 手動
微軟軟體影子複製供應商（SWPRV） | 手動

### <a name="validate-windows-server-backup-status"></a>驗證 Windows 伺服器備份狀態

要驗證 Windows 伺服器備份狀態，請執行以下步驟：

- 確保 WSB 電源外殼正在運行

  - 從`Get-WBJob`提升的 PowerShell 運行並確保它不會返回以下錯誤：

    > [!WARNING]
    > 獲取 WBJob： 術語"獲取-WBJob"不被識別為 Cmdlet、函數、指令檔或可操作程式的名稱。 請檢查名稱拼字，如果名稱含有路徑，請確認路徑正確，然後再試一次。

    - 如果此錯誤失敗，則按照先決條件的步驟 1 中所述，在伺服器電腦上重新安裝 Windows 伺服器備份功能。

  - 通過從提升的命令提示符運行以下命令，確保 WSB 備份正常工作：

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >將 X 替換為要存儲系統狀態備份映射的卷的磁碟機號。

    - 通過從提升的 PowerShell 運行`Get-WBJob`命令來定期檢查作業的狀態
    - 備份作業完成後，通過運行`Get-WBJob -Previous 1`命令檢查作業的最終狀態

如果作業失敗，它指示 WSB 問題將導致 MARS 代理系統狀態備份失敗。

## <a name="common-errors"></a>常見錯誤

### <a name="vss-writer-timeout-error"></a>VSS 編寫器逾時錯誤

| 徵狀 | 原因 | 解決方案
| -- | -- | --
| - MARS 代理失敗，錯誤訊息："WSB 作業失敗 VSS 錯誤。 檢查 VSS 事件日誌以解決故障"<br/><br/> - VSS 應用程式事件日誌中存在以下錯誤日誌："VSS 編寫器已拒絕錯誤為 0x800423f2 的事件，編寫器的超時在凍結和解凍事件之間過期。| 由於電腦上缺少 CPU 和記憶體資源，VSS 編寫器無法及時完成 <br/><br/> 另一個備份軟體已在使用 VSS 編寫器，因此無法為此備份完成快照操作 | 等待 CPU/記憶體在系統上釋放或中止佔用太多記憶體/CPU 的進程，然後重試該操作。 <br/><br/>  等待正在進行的備份完成，並在以後電腦上未運行備份時嘗試操作。

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>磁碟空間不足，無法擴展卷影副本

| 徵狀 | 解決方案
| -- | --
| - MARS 代理失敗，出現錯誤訊息：由於包含系統檔的卷上的磁碟空間不足，卷影副本卷無法增長，因此備份失敗 <br/><br/> - volsnap 系統事件日誌中存在以下錯誤/警告日誌："卷 C 上磁碟空間不足：用於擴展 C 卷卷副本的卷影副本存儲：由於此故障，C 卷的所有卷影副本都有被刪除的風險" | - 釋放事件日誌中突出顯示的卷中的空間，以便在備份過程中有足夠的空間來擴展卷影副本 <br/><br/> - 在配置卷影複製空間時，我們可以限制用於卷影複製的空間量。 有關詳細資訊，請參閱[本文](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>EFI 分區鎖定

| 徵狀 | 解決方案
| -- | --
| MARS 代理失敗，出現錯誤訊息："當 EFI 系統磁碟分割鎖定時，系統狀態備份失敗。 這可能是由於協力廠商安全或備份軟體的系統磁碟分割訪問" | - 如果問題是由於協力廠商安全軟體，那麼您需要聯繫防病毒供應商，以便他們可以允許 MARS 代理 <br/><br/> - 如果協力廠商備份軟體正在運行，則等待它完成，然後重試備份

## <a name="next-steps"></a>後續步驟

- 有關資源管理器部署中的 Windows 系統狀態的詳細資訊，請參閱[備份 Windows 伺服器系統狀態](backup-azure-system-state.md)
