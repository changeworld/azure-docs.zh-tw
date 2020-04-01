---
title: 排除 Azure 備份代理的故障
description: 在本文中,瞭解如何對 Azure 備份代理的安裝和註冊進行故障排除。
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: a15f8a4531bc31dab5b99e125454b0d9c4fd4521
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421282"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>排除 Microsoft Azure 回復服務 (MARS) 代理的故障

本文介紹如何解決在配置、註冊、備份和還原期間可能看到的錯誤。

## <a name="basic-troubleshooting"></a>基本疑難排解

我們建議您在開始對 Microsoft Azure 恢復服務 (MARS) 代理進行故障排除之前檢查以下內容:

- [確保 MARS 代理伺服器是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)。
- [確保 MARS 代理與 Azure 之間存在網路連線](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)。
- 確保 MARS 正在運行(在服務控制台中)。 如果需要,請重新啟動並重試該操作。
- [確保 5% 到 10% 的可用的空間在暫存資料夾位置可用](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)。
- [檢查另一個行程或防病毒軟體是否干擾 Azure 備份](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)。
- 如果計劃備份失敗,但手動備份工作正常,請參閱[備份不按計劃運行](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)。
- 確保您的作業系統具有最新的更新。
- [確保從備份中排除不支援的驅動程式與具有不支援屬性的檔案](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)。
- 確保受保護系統上的時鐘配置為正確的時區。
- [確保 .NET 架構 4.5.2 或更高版本安裝在伺服器上](https://www.microsoft.com/download/details.aspx?id=30653)。
- 如果您嘗試將伺服器重新註冊到保存庫:
  - 確保代理在伺服器上卸載,並且從門戶中刪除代理。
  - 使用最初用於註冊伺服器的相同密碼。
- 對於離線備份,請確保在啟動備份之前在源電腦上和複製電腦上都安裝了 Azure PowerShell 3.7.0。
- 如果備份代理在 Azure 虛擬機器上執行,請參閱[本文](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)。

## <a name="invalid-vault-credentials-provided"></a>提供的保存庫認證無效

**錯誤消息**:提供的無效保管庫憑據。 檔案已損毀或沒有與復原服務關聯的最新認證。 (識別碼：34513)

| 原因 | 建議動作 |
| ---     | ---    |
| **保管庫認證無效** <br/> <br/> 保管庫憑據檔可能已損壞或已過期。 (例如,它們可能在註冊時間之前 48 小時以上下載。| 從 Azure 門戶上的恢復服務保管庫下載新憑據。 (請參閱下載 MARS[代理部分中](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent)的步驟 6。然後根據需要執行以下步驟: <ul><li> 如果已安裝並註冊 MARS,請打開 Microsoft Azure 備份代理 MMC 控制台,然後在 **「操作」** 窗格中選擇 **「註冊伺服器**」以使用新憑據完成註冊。 <br/> <li> 如果新安裝失敗,請嘗試使用新憑據重新安裝。</ul> **注意**:如果下載了多個保管庫憑據檔,則只有最新的檔在接下來的 48 小時內有效。 我們建議您下載新的保管庫憑據檔。
| **代理伺服器/防火牆封鎖註冊** <br/>或 <br/>**無網路連線** <br/><br/> 如果電腦或代理伺服器的網路連接有限,並且您無法確保存取必要的網址,則註冊將失敗。| 請執行下列步驟：<br/> <ul><li> 與您的 IT 團隊合作,確保系統具有互聯網連接。<li> 如果沒有代理伺服器,請確保在註冊代理時未選擇代理選項。 [檢查您的代理伺服器設定](#verifying-proxy-settings-for-windows)。<li> 如果您確實有防火牆/代理伺服器,請與您的網路團隊合作,確保這些網址和 IP 位址具有存取權限:<br/> <br> **網址**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP 位址**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>完成上述故障排除步驟后,請嘗試再次註冊。<br></br> 如果要連接是透過 Azure ExpressRoute 進行的,請確保設定已按照[Azure ExpressRoute 支援](backup-support-matrix-mars-agent.md#azure-expressroute-support)中所述進行配置。
| **防病毒軟體正在阻止註冊** | 如果伺服器上安裝了防病毒軟體,請向這些檔案和資料夾的防病毒掃描添加必要的排除規則: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> 暫存資料夾。 其預設位置為 C:\程式檔\微軟 Azure 恢復服務代理\臨時。 <li> C:\程式檔\微軟 Azure 恢復服務代理\Bin 處的 bin 資料夾。

### <a name="additional-recommendations"></a>其他建議

- 移至 C:/Windows/Temp，並檢查具有 .tmp 副檔名的檔案是否超過 60,000 或 65,000 個。 如果有，請刪除這些檔案。
- 確保機器的日期和時間與本地時區匹配。
- 確保[這些網站](install-mars-agent.md#verify-internet-access)已添加到 Internet 資源管理員中的受信任網站。

### <a name="verifying-proxy-settings-for-windows"></a>認證 Windows 的代理設定

1. 從[系統內部](https://docs.microsoft.com/sysinternals/downloads/psexec)頁面下載 PsExec。
1. 從`psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`提升的命令提示符運行。

   此命令將打開 Internet 資源管理員。
1. 跳到**工具** > **Internet 選項** > **連線** > LAN**設定**。
1. 檢查系統帳戶的代理設置。
1. 如果未配置代理,並且提供代理詳細資訊,請刪除詳細資訊。
1. 如果配置了代理,並且代理詳細資訊不正確,請確保**代理 IP**和**埠**詳細資訊正確。
1. 關閉 Internet Explorer。

## <a name="unable-to-download-vault-credential-file"></a>無法下載保存庫認證檔

| 錯誤   | 建議動作 |
| ---     | ---    |
|無法下載保存庫認證檔案。 (ID: 403) | <ul><li> 請嘗試使用其他瀏覽器下載保管庫認證,或執行以下步驟: <ul><li> 啟動 Internet Explorer。 選擇 F12。 </li><li> 跳到 **「網路」** 選項卡並清除快取和 Cookie。 </li> <li> 重新整理頁面。<br></li></ul> <li> 檢查訂閱是否已禁用/過期。<br></li> <li> 檢查是否有任何防火牆規則阻止下載。 <br></li> <li> 確保尚未用盡保管庫的限制(每個保管庫 50 台計算機)。<br></li>  <li> 確保使用者具有下載保管庫認證和向保管庫註冊伺服器所需的 Azure 備份許可權。 請參考[使用基於角色的存取控制來管理 Azure 備份復原點](backup-rbac-rs-vault.md)。</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份

| 錯誤  | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure 恢復服務代理無法連接到 Microsoft Azure 備份。 (ID: 100050)檢查您的網路設置,並確保您能夠連接到互聯網。<li>(407) 需要 Proxy 驗證。 |代理正在阻止連接。 |  <ul><li>在網際網路瀏覽器,去**工具** > **網際網路選項** > **Security** > 安全**網際網路**。 選擇 **「自訂層級」** 並向下捲軸到「**檔案下載**」 部分。 選取 [啟用]****。<p>您可能必須在 Internet 資源管理員中向信任的網站新增[網址和 IP 位址](install-mars-agent.md#verify-internet-access)。<li>變更設定以使用 Proxy 伺服器。 接著提供 Proxy 伺服器詳細資料。<li> 如果您的電腦的 Internet 存取受限,請確保機器或代理上的防火牆設定允許這些[網址和 IP 位址](install-mars-agent.md#verify-internet-access)。 <li>如果伺服器上安裝了防病毒軟體,請從防病毒掃描中排除這些檔: <ul><li>CBEngine.exe (而不是 dpmra.exe)。<li>CSC.exe (與 .NET Framework 相關)。 伺服器上安裝的每個 .NET 框架版本都有一個 CSC.EXe。 排除受影響伺服器上所有版本的 .NET Framework 的 CSC.exe 檔。 <li>暫存資料夾或緩存位置。 <br>暫存資料夾或快取路徑的預設位置為 C:\程式檔\Microsoft Azure 恢復服務代理\臨時。<li>C:\程式檔\微軟 Azure 恢復服務代理\Bin 處的 bin 資料夾。

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>無法設定安全備份的加密金鑰

| 錯誤 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| <br />無法為安全備份設置加密密鑰。 啟動未完全成功，但是加密複雜密碼已儲存至下列檔案。 |<li>伺服器已經向另一個保存庫註冊。<li>在設定期間，複雜密碼已損毀。| 從保管庫註銷伺服器,然後再次使用新的密碼重新註冊。

## <a name="the-activation-did-not-complete-successfully"></a>啟動沒有成功完成

| 錯誤  | 可能的原因 | 建議動作 |
|---------|---------|---------|
|<br />啟動沒有成功完成。 由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗。 請在一段時間之後重試此作業。 如果問題持續發生， 請連絡 Microsoft 支援服務。     | <li> 臨時資料夾位於空間不足的卷上。 <li> 臨時文件移動不正確。 <li> 遺失 OnlineBackup.KEK 檔案。         | <li>升級到[最新版本的](https://aka.ms/azurebackup_agent)MARS 代理。<li>將暫存資料夾或快取位置移到可用空間介於備份資料總大小的 5% 到 10% 之間的卷。 要正確移動快取位置,請參閱[有關備份文件和資料夾的常見問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>*暫存資料夾或快取路徑的預設位置是 C:\程式檔案\微軟 Azure 復原服務代理\暫存*。        |

## <a name="encryption-passphrase-not-correctly-configured"></a>未正確設定加密複雜密碼

| 錯誤  | 可能的原因 | 建議動作 |
|---------|---------|---------|
| <br />錯誤 34506。 未正確設定儲存於此電腦上的加密複雜密碼。    | <li> 臨時資料夾位於空間不足的卷上。 <li> 臨時文件移動不正確。 <li> 遺失 OnlineBackup.KEK 檔案。        | <li>升級至[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理程式。<li>將暫存資料夾或快取位置移到可用空間介於備份資料總大小的 5% 到 10% 之間的卷。 要正確移動快取位置,請參閱[有關備份文件和資料夾的常見問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>*暫存資料夾或快取路徑的預設位置是 C:\程式檔案\微軟 Azure 復原服務代理\暫存*。         |

## <a name="backups-dont-run-according-to-schedule"></a>備份不按計劃執行

如果計劃備份未自動觸發,但手動備份工作正常,請嘗試以下操作:

- 確保 Windows 伺服器備份計畫不與 Azure 檔案和資料夾備份計畫衝突。

- 確保連線備份狀態設置為 **「啟用**」。 要驗證狀態,請執行以下步驟:

  1. 在工作計劃程式中,展開**Microsoft**並選擇**連線備份**。
  1. 按兩下**Microsoft 線上備份**並轉到 **「觸發器」** 選項卡。
  1. 檢查狀態是否設置為 **「已啟用**」。 如果不是,請選擇 **「編輯**」,選擇「**已啟用**」,然後選擇 **「確定**」 。

- 使用執行工作選擇的使用者帳戶是伺服器上的**SYSTEM**或**本地管理員的群組**。 要驗證使用者帳戶,請轉到 **「常規」** 選項卡並檢查 **「安全**」選項。

- 確保 PowerShell 3.0 或更高版本安裝在伺服器上。 要檢查 PowerShell 版本,請執行此指令`Major`並驗證 版本號是 3 或更高版本:

  `$PSVersionTable.PSVersion`

- 確保此路徑是環境變數的`PSMODULEPATH`一部分:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 如果 PowerShell`LocalMachine`執行策略`restricted`設定為 ,則觸發備份任務的 PowerShell cmdlet 可能會失敗。 在提升模式下執行這些指令,以檢查並執行策略設定為或`Unrestricted``RemoteSigned`:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- 確保沒有丟失或損壞的 PowerShell 模組 MSOnline 備份檔。 如果有任何遺失或損壞的檔,請執行以下步驟:

  1. 從具有正常工作的 MARS 代理的任何電腦中,從 C:*程式檔\Microsoft Azure 恢複服務代理\bin_模組複製 MSOnlineBackup 資料夾。
  1. 在有問題的電腦上,將複製的檔粘貼到同一資料夾位置(C:*程式檔\Microsoft Azure 恢復服務代理\bin_Module)。

     如果電腦上已存在 MSOnlineBackup 資料夾,請將檔粘貼到其中或替換任何現有檔。

> [!TIP]
> 為確保一致地應用更改,請在執行上述步驟后重新啟動伺服器。

## <a name="resource-not-provisioned-in-service-stamp"></a>服務戳中未預配的資源

錯誤 | 可能的原因 | 建議動作
--- | --- | ---
由於內部服務錯誤"資源未預配在服務戳中",當前操作失敗。 請在一段時間之後重試此操作。 (ID: 230006) | 受保護伺服器已重新命名。 | <li> 將伺服器重命名回在保管庫中註冊的原始名稱。 <br> <li> 使用新名稱將伺服器重新註冊到保管庫。


## <a name="troubleshoot-restore-problems"></a>容損毀的正常問題

Azure 備份可能未成功掛接復原磁碟區，即使數分鐘後仍未成功。 在此過程中,您可能會收到錯誤消息。 要正常開始恢復,請執行以下步驟:

1. 如果裝載進程已運行幾分鐘,則取消該進程。

2. 檢查您是否具有最新版本的備份代理。 要檢查版本,請在 MARS 主控台的 **「操作」** 窗格中,選擇 **「關於 Microsoft Azure 恢復服務代理**」。 確認 [版本]**** 號碼等於或高於[這篇文章](https://go.microsoft.com/fwlink/?linkid=229525)中提到的版本。 選擇此連結[可下載最新版本](https://go.microsoft.com/fwLink/?LinkID=288905)。

3. 跳到**裝置管理員** > **儲存控制器**並找到微軟**iSCSI 的傳送器**。 如果找到它,請直接轉到步驟 7。

4. 如果找不到 Microsoft iSCSI 的開機器服務,請嘗試在名為「未知裝置 **」的裝置管理員** > **儲存控制器**下尋找一個項目,該控制器名為 **「未知裝置**」,硬體 ID **ROOT_ISCSIPRT**。

5. 右鍵按一**下 未知裝置**並選擇 **「更新驅動程式軟體**」。

6. 藉由選取 [自動搜尋更新的驅動程式軟體]**** 的選項，以更新驅動程式。 此更新應將**未知裝置**變更為**Microsoft iSCSI 的發行者**:

    ![Azure 備份裝置管理員的螢幕擷取畫面，已反白顯示儲存體控制器](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. 跳到**工作管理員** > **服務(本地端)** > **微軟 iSCSI 的分機服務**:

    ![Azure 備份工作管理員的螢幕擷取畫面，已反白顯示服務 (本機)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. 重新啟動 Microsoft iSCSI 啟動器服務。 為此,請右鍵單擊服務並選擇 **"停止**"。 然後右鍵再次按下它,然後選擇 **「開始**」。

9. 使用 [立即還原][](backup-instant-restore-capability.md) 重試復原。

如果恢復仍然失敗,請重新啟動伺服器或用戶端。 如果不想重新啟動,或者即使在重新啟動伺服器後恢復仍然失敗,請嘗試[從另一台電腦恢復](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

## <a name="troubleshoot-cache-problems"></a>解決快取問題

如果快取資料夾(也稱為臨時資料夾)配置不正確、缺少先決條件或具有受限存取許可權,則備份操作可能會失敗。

### <a name="prerequisites"></a>Prerequisites

對於 MARS 代理操作的成功,快取資料夾需要遵守以下要求:

- [確保一個資料夾位置有 5% 到 10% 的可用卷空間](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [確保暫存資料夾位置有效且可存取](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [確保快取資料夾上的檔案屬性](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [確保配置的捲影複本儲存空間足以進行備份過程](#increase-shadow-copy-storage)
- [確保沒有其他行程(例如防病毒軟體)限制對快取資料夾的存取](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>新增影複本儲存

如果保護數據源所需的捲影副本存儲空間不足,則備份操作可能會失敗。 要解決此問題,請使用 vssadmin 增加受保護卷上的捲影副本存儲空間,如下所示:

- 從提升的指令提示符檢查目前捲影記憶體空間:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- 使用以下指令增加陰影儲存空間:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>封鎖存取快取資料夾的另一個程序或防病毒軟體

如果伺服器上安裝了防病毒軟體,請向這些檔案和資料夾的防病毒掃描添加必要的排除規則:  

- 暫存資料夾。 此預設位置為 C:\程式檔案\微軟 Azure 復原服務代理\劃痕
- C:\程式檔案\微軟 Azure 恢復服務代理\Bin 處的 bin 資料夾
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>常見問題

本節介紹您在使用 MARS 代理時遇到的常見錯誤。

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

錯誤訊息 | 建議的動作 |
-- | --
Microsoft Azure 復原服務代理程式無法存取臨時位置中所儲存的備份總和檢查碼 | 要解決此問題,請執行以下操作並重新啟動伺服器 <br/> - [檢查是否有防病毒或其他行程鎖定暫時位置檔案](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [檢查臨時位置是否有效且可供 mars 代理訪問。](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

錯誤訊息 | 建議的動作 |
-- | --
Microsoft Azure 復原服務代理程式無法存取臨時位置以初始化 VHD | 要解決此問題,請執行以下操作並重新啟動伺服器 <br/> - [檢查是否有防病毒或其他行程鎖定暫時位置檔案](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [檢查臨時位置是否有效且可供 mars 代理訪問。](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>薩爾洛磁碟空間

錯誤訊息 | 建議的動作 |
-- | --
由於暫存資料夾所在的卷中儲存空間不足,備份失敗 | 要解決此問題,請驗證以下步驟並重試操作:<br/>- [確保 MARS 代理伺服器是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [驗證和解決影響備份臨時空間的儲存問題](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

錯誤訊息 | 建議的動作 |
-- | --
找不到檔案中的變更。 這可能是由各種原因所造成。 請重試該作業 | 要解決此問題,請驗證以下步驟並重試操作:<br/> - [確保 MARS 代理伺服器是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [驗證和解決影響備份臨時空間的儲存問題](#prerequisites)

## <a name="next-steps"></a>後續步驟

- 獲取有關[如何使用 Azure 備份代理備份 Windows 伺服器](tutorial-backup-windows-server-to-azure.md)的更多詳細資訊。
- 如果需要還原備份,請參閱[將檔案還原到 Windows 電腦](backup-azure-restore-windows-server.md)。
