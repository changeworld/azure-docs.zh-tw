---
title: 針對 Azure 備份代理程式進行疑難排解
description: 在本文中，您將瞭解如何針對 Azure 備份代理程式的安裝和註冊進行疑難排解。
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 4ae4142652d9d38d5bf384e5a10d6eeb7e3cc608
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900377"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>針對 Microsoft Azure 復原服務 (MARS) 代理程式進行疑難排解

本文說明如何解決在設定、註冊、備份和還原期間可能會看到的錯誤。

## <a name="basic-troubleshooting"></a>基本疑難排解

建議您先檢查下列各項，再開始針對 Microsoft Azure 復原服務 (MARS) 代理程式進行疑難排解：

- [確定 MARS 代理程式是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)。
- [確定 MARS 代理程式與 Azure 之間有網路連線能力](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)。
- 確定 MARS 正在 (服務主控台) 中執行。 如有需要，請重新開機，然後重試此操作。
- [請確定暫存檔案夾位置有5% 到10% 的可用磁片區空間](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)。
- [檢查是否有其他處理常式或防毒軟體干擾 Azure 備份](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)。
- 如果備份作業已完成但出現警告，請參閱 [備份作業已完成，但出現警告](#backup-jobs-completed-with-warning)
- 如果排定的備份失敗，但手動備份正常運作，請參閱 [備份不會根據排程執行](#backups-dont-run-according-to-schedule)。
- 確定您的作業系統有最新的更新。
- [請確定不支援的磁片磁碟機和具有不支援屬性的檔案會從備份中排除](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)。
- 請確定受保護系統上的時鐘已設定為正確的時區。
- [確定伺服器上已安裝 .NET Framework 4.5.2 或更新版本](https://www.microsoft.com/download/details.aspx?id=30653)。
- 如果您嘗試將伺服器重新註冊至保存庫：
  - 確定已在伺服器上卸載代理程式，且該代理程式已從入口網站中刪除。
  - 使用最初用來註冊伺服器的相同複雜密碼。
- 針對離線備份，請確定在開始備份之前，會將 Azure PowerShell 3.7.0 安裝在來源和複製電腦上。
- 如果備份代理程式是在 Azure 虛擬機器上執行，請參閱 [這篇文章](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)。

## <a name="invalid-vault-credentials-provided"></a>提供的保存庫認證無效

**錯誤訊息** ：提供的保存庫認證無效。 檔案已損毀或沒有與復原服務關聯的最新認證。 (識別碼：34513)

| 原因 | 建議動作 |
| ---     | ---    |
| **保存庫認證無效** <br/> <br/> 保存庫認證檔案可能已損毀、可能已過期，或副檔名可能與 *vaultCredentials* 不同。  (例如，在註冊時間之前，可能已下載超過48小時。 ) | 從 Azure 入口網站上的復原服務保存庫[下載新的認證](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file)。 然後視需要採取下列步驟： <ul><li> 如果您已經安裝並註冊 MARS，請開啟 Microsoft Azure 備份代理程式 MMC 主控台。 然後選取 [ **動作** ] 窗格中的 [ **註冊伺服器** ]，以使用新的認證來完成註冊。 <br/> <li> 如果新的安裝失敗，請嘗試使用新的認證重新安裝。</ul> **注意** ：如果已下載多個保存庫認證檔案，則只有最新的檔案會在接下來的48小時內生效。 建議您下載新的保存庫認證檔。
| **Proxy 伺服器/防火牆封鎖註冊** <br/>或 <br/>**沒有網際網路連線能力** <br/><br/> 如果您的電腦或 proxy 伺服器具有有限的網際網路連線能力，且您不確定是否有必要 Url 的存取權，則註冊將會失敗。| 請執行下列步驟：<br/> <ul><li> 與您的 IT 小組合作，以確保系統具有網際網路連線能力。<li> 如果您沒有 proxy 伺服器，請確定當您註冊代理程式時，未選取 [proxy] 選項。 [檢查您的 proxy 設定](#verifying-proxy-settings-for-windows)。<li> 如果您有防火牆/proxy 伺服器，請與您的網路團隊合作，以確保這些 Url 和 IP 位址具有存取權：<br/> <br> **URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP 位址**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>完成上述的疑難排解步驟之後，請嘗試再次註冊。<br></br> 如果您的連線是透過 Azure ExpressRoute，請確定已設定 [Azure expressroute 支援](backup-support-matrix-mars-agent.md#azure-expressroute-support)中所述的設定。
| **防毒軟體正在封鎖註冊** | 如果您已在伺服器上安裝防毒軟體，請將必要的排除規則新增至這些檔案和資料夾的防毒軟體掃描： <br/><ul> <li> CBengine.exe <li> CSC.exe<li> 暫存檔案夾。 其預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。 <li> C:\Program Files\Microsoft Azure Recovery Services Agent\Bin. 的 bin 資料夾

### <a name="additional-recommendations"></a>其他建議

- 移至 C:/Windows/Temp，並檢查具有 .tmp 副檔名的檔案是否超過 60,000 或 65,000 個。 如果有，請刪除這些檔案。
- 請確定電腦的日期和時間與當地時區相符。
- 確定已將 [這些網站](install-mars-agent.md#verify-internet-access) 新增至 Internet Explorer 中的信任網站。

### <a name="verifying-proxy-settings-for-windows"></a>驗證 Windows 的 proxy 設定

1. 從 [Sysinternals](/sysinternals/downloads/psexec) 頁面下載 PsExec。
1. `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`從提升許可權的命令提示字元執行。

   此命令會開啟 Internet Explorer。
1. 移至 [ **工具**  >  **網際網路選項** 連線  >  **Connections**  >  **區域網路設定** ]。
1. 檢查系統帳戶的 proxy 設定。
1. 如果未設定 proxy 並提供 proxy 詳細資料，請移除詳細資料。
1. 如果已設定 proxy 且 proxy 詳細資料不正確，請確定 **PROXY IP** 和 **埠** 的詳細資料正確無誤。
1. 關閉 Internet Explorer。

## <a name="unable-to-download-vault-credential-file"></a>無法下載保存庫認證檔

| 錯誤   | 建議動作 |
| ---     | ---    |
|無法下載保存庫認證檔案。  (識別碼： 403)  | <ul><li> 請嘗試使用不同的瀏覽器下載保存庫認證，或執行下列步驟： <ul><li> 啟動 Internet Explorer。 選取 F12。 </li><li> 移至 [ **網路** ] 索引標籤，並清除快取和 cookie。 </li> <li> 重新整理頁面。<br></li></ul> <li> 檢查訂用帳戶是否已停用/過期。<br></li> <li> 檢查是否有任何防火牆規則封鎖下載。 <br></li> <li> 請確定您未在每個保存庫)  (50 部電腦上達到限制。<br></li>  <li> 請確定使用者具有下載保存庫認證所需的 Azure 備份許可權，並向保存庫註冊伺服器。 請參閱 [使用 Azure 角色型存取控制來管理 Azure 備份復原點](backup-rbac-rs-vault.md)。</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份

| 錯誤  | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure 復原服務代理程式無法連接到 Microsoft Azure 備份。  (識別碼： 100050) 檢查您的網路設定，並確定您能夠連線到網際網路。<li>(407) 需要 Proxy 驗證。 |Proxy 正在封鎖連接。 |  <ul><li>在 Internet Explorer 中，移至 **Tools**  >  **internet options**  >  **Security**  >  **internet** 。 選取 [ **自訂層級** ]，並向下滾動至 [檔案 **下載** ] 區段。 選取 [啟用]。<p>您可能也必須在 Internet Explorer 中，將 [url 和 IP 位址](install-mars-agent.md#verify-internet-access) 新增至信任的網站。<li>變更設定以使用 Proxy 伺服器。 接著提供 Proxy 伺服器詳細資料。<li> 如果您的電腦具有有限的網際網路存取權，請確定電腦或 proxy 上的防火牆設定允許這些 [url 和 IP 位址](install-mars-agent.md#verify-internet-access)。 <li>如果您已在伺服器上安裝防毒軟體，請從防毒軟體掃描中排除這些檔案： <ul><li>CBEngine.exe (而不是 dpmra.exe)。<li>CSC.exe (與 .NET Framework 相關)。 伺服器上安裝的每個 .NET Framework 版本都有一個 CSC.exe。 排除受影響伺服器上所有 .NET Framework 版本的 CSC.exe 檔案。 <li>暫存檔案夾或快取位置。 <br>暫存檔案夾或快取路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。<li>C:\Program Files\Microsoft Azure Recovery Services Agent\Bin. 的 bin 資料夾

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>無法使用指定的保存庫認證檔，因為未從與此伺服器相關聯的保存庫下載該檔案

| 錯誤  | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| 無法使用指定的保存庫認證檔，因為未從與此伺服器相關聯的保存庫下載該檔案。  (識別碼： 100110) 請提供適當的保存庫認證。 | 保存庫認證檔來自不同于此伺服器已註冊的保存庫。 | 確定目標電腦和來源電腦均已註冊到相同的復原服務保存庫。 如果目標伺服器已註冊到不同的保存庫，請使用 [ **註冊伺服器** ] 選項來註冊至正確的保存庫。  

## <a name="backup-jobs-completed-with-warning"></a>備份作業已完成，但出現警告

- MARS 代理程式在備份期間逐一查看檔案和資料夾時，可能會遇到可能導致備份標示為已完成但出現警告的各種狀況。 在這些情況下，工作會顯示為已完成，但出現警告。 沒問題，但這表示至少有一個檔案無法備份。 如此一來，工作會略過該檔案，但會在資料來源上備份所有其他有問題的檔案。

  ![備份作業已完成，但出現警告](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- 可能導致備份略過檔案的狀況包括：
  - 不支援的檔案屬性 (例如：在 OneDrive 資料夾、壓縮的資料流程、重新分析點) 。 如需完整清單，請參閱 [支援矩陣](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup)。
  - 檔案系統問題
  - 另一個進程干擾 (例如：在檔案上保存控制碼的防毒軟體可以防止 MARS 代理程式存取檔案) 
  - 應用程式鎖定的檔案  

- 備份服務會在記錄檔中將這些檔案標示為失敗，並具有下列命名慣例： *LastBackupFailedFilesxxxx.txt* 在 *C:\Program Files\Microsoft Azure Recovery service Agent\temp* 資料夾底下。
- 若要解決此問題，請檢查記錄檔以瞭解問題的本質：

  | 錯誤碼             | 原因                                             | 建議                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | 檔案或目錄已損毀且無法讀取。 | 在來源磁片區上執行 **chkdsk** 。                             |
  | 0x80070002、0x80070003 | 系統找不到指定的檔案。         | [確定暫存檔案夾未滿](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)  <br><br>  檢查已設定臨時空間的磁片區是否存在 (未刪除)   <br><br>   [確定已從電腦上安裝的防毒軟體排除 MARS 代理程式](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | 拒絕存取                                    | [檢查防毒軟體或其他協力廠商軟體是否封鎖存取](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | 拒絕存取雲端檔案。                | OneDrive 檔案、Git 檔案或任何其他可在電腦上離線狀態的檔案 |

- 您可以將 [排除規則新增至現有的原則](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) ，以從備份原則中排除不支援、遺失或刪除的檔案，以確保備份成功。

- 避免在最上層資料夾中刪除並重新建立具有相同名稱的受保護資料夾。 這樣做可能會導致備份完成，並出現警告，並出現錯誤，並導致偵測 *到重大的不一致情況，因此無法複寫變更。*  如果您需要刪除和重新建立資料夾，請考慮在受保護的最上層資料夾下的子資料夾中執行此動作。

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>無法設定安全備份的加密金鑰

| 錯誤 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| <br />無法設定安全備份的加密金鑰。 啟動未完全成功，但是加密複雜密碼已儲存至下列檔案。 |<li>伺服器已經向另一個保存庫註冊。<li>在設定期間，複雜密碼已損毀。| 從保存庫取消註冊伺服器，然後使用新的複雜密碼重新註冊。

## <a name="the-activation-did-not-complete-successfully"></a>啟動沒有成功完成

| 錯誤  | 可能的原因 | 建議動作 |
|---------|---------|---------|
|<br />啟動沒有成功完成。 由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗。 請在一段時間之後重試此作業。 如果問題持續發生， 請連絡 Microsoft 支援服務。     | <li> 暫存檔案夾位於沒有足夠空間的磁片區上。 <li> 暫存檔案夾已不正確地移動。 <li> 遺失 OnlineBackup.KEK 檔案。         | <li>升級至 [最新版本](https://aka.ms/azurebackup_agent) 的 MARS 代理程式。<li>將暫存檔案夾或快取位置移至具有可用空間的磁片區，該磁片區的可用空間介於備份資料總大小的5% 到10% 之間。 若要正確地移動快取位置，請參閱 [備份檔案和資料夾的相關](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)常見問題中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>*暫存檔案夾或快取路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch* 。        |

## <a name="encryption-passphrase-not-correctly-configured"></a>未正確設定加密複雜密碼

| 錯誤  | 可能的原因 | 建議動作 |
|---------|---------|---------|
| <br />錯誤 34506。 未正確設定儲存於此電腦上的加密複雜密碼。    | <li> 暫存檔案夾位於沒有足夠空間的磁片區上。 <li> 暫存檔案夾已不正確地移動。 <li> 遺失 OnlineBackup.KEK 檔案。        | <li>升級至[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理程式。<li>將暫存檔案夾或快取位置移至具有可用空間的磁片區，該磁片區的可用空間介於備份資料總大小的5% 到10% 之間。 若要正確地移動快取位置，請參閱 [備份檔案和資料夾的相關](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)常見問題中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>*暫存檔案夾或快取路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch* 。         |

## <a name="backups-dont-run-according-to-schedule"></a>備份不會根據排程執行

如果排程的備份未自動觸發，但手動備份正常運作，請嘗試下列動作：

- 請確定 Windows Server 備份排程不會與 Azure 檔案與資料夾備份排程衝突。

- 請確定 [線上備份] 狀態設定為 [ **啟用** ]。 若要確認狀態，請執行下列步驟：

  1. 在工作排程器中，展開 [ **Microsoft** ]，然後選取 [ **線上備份** ]。
  1. 按兩下 [ **Microsoft microsoft-onlinebackup** ]，然後移至 [ **觸發** 程式] 索引標籤。
  1. 檢查狀態是否設為 [ **已啟用** ]。 如果不是，請選取 [ **編輯** ]，選取 [ **已啟用** ]，然後選取 **[確定]** 。

- 請確定為執行工作而選取的使用者帳戶是伺服器上的 [ **系統** ] 或 [本機系統 **管理員] 群組** 。 若要確認使用者帳戶，請移至 [ **一般** ] 索引標籤，並檢查 **安全性** 選項。

- 確定伺服器上已安裝 PowerShell 3.0 或更新版本。 若要檢查 PowerShell 版本，請執行此命令，並確認 `Major` 版本號碼為3或更新版本：

  `$PSVersionTable.PSVersion`

- 請確定此路徑是 `PSMODULEPATH` 環境變數的一部分：

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 如果的 PowerShell 執行原則 `LocalMachine` 設定為 `restricted` ，則觸發備份工作的 powershell Cmdlet 可能會失敗。 在提高許可權的模式中執行這些命令，以檢查執行原則，並將其設定為 `Unrestricted` 或 `RemoteSigned` ：

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- 請確定沒有任何遺失或損毀的 PowerShell 模組 MSOnlineBackup 檔。 如果有任何遺失或損毀的檔案，請執行下列步驟：

  1. 從具有可正常運作的 MARS 代理程式的任何電腦，將 MSOnlineBackup 資料夾從 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules。
  1. 在有問題的電腦上，將複製的檔案貼到相同的資料夾位置， (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules) 。

     如果電腦上已經有 MSOnlineBackup 資料夾，請將檔案貼到其中，或取代任何現有的檔案。

> [!TIP]
> 若要確保一致地套用變更，請在執行上述步驟之後重新開機伺服器。

## <a name="resource-not-provisioned-in-service-stamp"></a>服務戳記中未布建資源

錯誤 | 可能的原因 | 建議動作
--- | --- | ---
目前的作業失敗，因為發生內部服務錯誤「未在服務戳記中布建資源」。 請在一段時間之後重試此操作。  (識別碼： 230006)  | 受保護的伺服器已重新命名。 | <li> 將伺服器重新命名為使用保存庫註冊的原始名稱。 <br> <li> 使用新名稱重新註冊伺服器至保存庫。

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>因為另一個作業正在進行中，所以無法啟動作業

如果您注意到 **MARS 主控台** 作業歷程記錄中的警告訊息  >  **** ，指出「作業無法啟動，因為另一個作業正在進行中」，這可能是因為工作排程器所觸發的工作重複實例。

![因為另一個作業正在進行中，所以無法啟動作業](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

若要解決此問題：

1. 在 [執行] 視窗中輸入 *taskschd.msc* 來啟動工作排程器嵌入式管理單元
1. 在左窗格中，流覽至 **工作排程器 Library**  ->  **Microsoft**  ->  **microsoft-onlinebackup** 。
1. 針對此文件庫中的每個工作，按兩下工作以開啟 [屬性]，然後執行下列步驟：
    1. 切換到 [設定]  索引標籤。

         ![[設定] 索引標籤](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. **如果工作已在執行中，請變更的選項，則會套用下列規則** 。 選擇 [不要 **啟動新的實例** ]。

         ![將規則變更為不啟動新實例](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>針對還原問題進行疑難排解

Azure 備份可能未成功掛接復原磁碟區，即使數分鐘後仍未成功。 而且，您可能會在處理過程中收到錯誤訊息。 若要開始正常復原，請執行下列步驟：

1. 如果裝載進程已在執行幾分鐘，請將其取消。

2. 檢查您是否有最新版本的備份代理程式。 若要檢查版本，請在 MARS 主控台的 [ **動作** ] 窗格上，選取 [ **關於 Microsoft Azure 復原服務代理程式** ]。 確認 [版本] 號碼等於或高於[這篇文章](https://go.microsoft.com/fwlink/?linkid=229525)中提到的版本。 選取此連結以 [下載最新版本](https://go.microsoft.com/fwLink/?LinkID=288905)。

3. 移至 **裝置管理員**  >  **存放裝置控制器** ，並找出 **Microsoft iSCSI 啟動器** 。 如果您找到它，請直接移至步驟7。

4. 如果您找不到 Microsoft iSCSI 啟動器服務，請嘗試在 [ **Device Manager**  >  具有硬體識別碼 **ROOT\ISCSIPRT** 的 **未知裝置** ] 裝置管理員 **儲存控制器** 中尋找專案。

5. 在 [ **不明裝置** ] 上按一下滑鼠右鍵，然後選取 [ **更新驅動程式軟體** ]。

6. 藉由選取 [自動搜尋更新的驅動程式軟體] 的選項，以更新驅動程式。 此更新應該會將 **未知的裝置** 變更為 **Microsoft iSCSI 啟動器** ：

    ![Azure 備份裝置管理員的螢幕擷取畫面，已反白顯示儲存體控制器](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. 移至 **工作管理員**  >  **服務 (本機)**  >  **Microsoft iSCSI 啟動器服務** ：

    ![Azure 備份工作管理員的螢幕擷取畫面，已反白顯示服務 (本機)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. 重新啟動 Microsoft iSCSI 啟動器服務。 若要這樣做，請以滑鼠右鍵按一下服務，然後選取 [ **停止** ]。 然後再以滑鼠右鍵按一下它，然後選取 [ **啟動** ]。

9. 使用 [立即還原][](backup-instant-restore-capability.md) 重試復原。

如果復原仍然失敗，請重新開機您的伺服器或用戶端。 如果您不想要重新開機，或者即使在重新開機伺服器之後復原仍然失敗，請嘗試 [從另一部電腦復原](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

## <a name="troubleshoot-cache-problems"></a>針對快取問題進行疑難排解

如果快取資料夾 (也稱為暫存檔案夾) 未正確設定、缺少必要條件或有限制存取，則備份作業可能會失敗。

### <a name="prerequisites"></a>必要條件

若要讓 MARS 代理程式作業成功，快取資料夾必須符合下列需求：

- [確定暫存檔案夾位置中有5% 到10% 的可用磁片區空間](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [確定暫存檔案夾位置有效且可供存取](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [確定支援快取資料夾上的檔案屬性](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [確定配置的陰影複製儲存空間足以進行備份程式](#increase-shadow-copy-storage)
- [請確定沒有其他進程 (例如防毒軟體) 限制對快取資料夾的存取](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>增加陰影複製儲存空間

如果沒有足夠的陰影複製儲存空間來保護資料來源，則備份作業可能會失敗。 若要解決此問題，請使用 **vssadmin** 來增加受保護磁片區上的陰影複製儲存空間，如下所示：

- 從提升許可權的命令提示字元中，檢查目前的陰影儲存空間：<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- 使用下列命令來增加陰影儲存空間：<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>另一個進程或防毒軟體封鎖存取快取資料夾

如果您已在伺服器上安裝防毒軟體，請將必要的排除規則新增至這些檔案和資料夾的防毒軟體掃描：  

- 暫存檔案夾。 其預設位置為 `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- Bin 資料夾位於 `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>常見問題

本節涵蓋使用 MARS 代理程式時所遇到的常見錯誤。

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

錯誤訊息 | 建議的動作
--|--
Microsoft Azure 復原服務代理程式無法存取臨時位置中所儲存的備份總和檢查碼 | 若要解決此問題，請執行下列步驟，並重新啟動伺服器。 <br/> - [檢查是否有防毒軟體或其他進程鎖定臨時位置檔案](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [檢查臨時位置是否有效且可供 MARS 代理程式存取。](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

錯誤訊息 | 建議的動作
--|--
Microsoft Azure 復原服務代理程式無法存取臨時位置以初始化 VHD | 若要解決此問題，請執行下列步驟，並重新啟動伺服器。 <br/> - [檢查防毒軟體或其他進程是否正在鎖定臨時位置檔案](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [檢查臨時位置是否有效且可供 MARS 代理程式存取。](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

錯誤訊息 | 建議的動作
--|--
因為暫存資料夾所在磁片區的儲存體不足，所以備份失敗 | 若要解決此問題，請確認下列步驟，然後再次嘗試操作：<br/>- [確定 MARS 代理程式是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [確認並解決影響備份暫存空間的儲存體問題](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

錯誤訊息 | 建議的動作
--|--
找不到檔案中的變更。 這可能是由各種原因所造成。 請重試該作業 | 若要解決此問題，請確認下列步驟，然後再次嘗試操作：<br/> - [確定 MARS 代理程式是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [確認並解決影響備份暫存空間的儲存體問題](#prerequisites)

## <a name="next-steps"></a>下一步

- 取得有關 [如何使用 Azure 備份代理程式來備份 Windows Server](tutorial-backup-windows-server-to-azure.md)的詳細資料。
- 如果您需要還原備份，請參閱將 [檔案還原到 Windows 電腦](backup-azure-restore-windows-server.md)。
