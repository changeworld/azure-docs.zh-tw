---
title: 針對 Azure 備份代理程式進行疑難排解
description: 在本文中，您將瞭解如何針對 Azure 備份代理程式的安裝和註冊進行疑難排解。
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 24169356600c25e664221af397051bb0fec3e459
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673066"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>針對 Microsoft Azure 復原服務（MARS）代理程式進行疑難排解

本文說明如何解決在設定、註冊、備份和還原期間可能會看到的錯誤。

## <a name="basic-troubleshooting"></a>基本疑難排解

我們建議您在開始針對 Microsoft Azure 復原服務（MARS）代理程式進行疑難排解之前，先檢查下列事項：

- [確定 MARS 代理程式是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)。
- [請確定 MARS 代理程式和 Azure 之間具有網路連線能力](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)。
- 確定 MARS 正在執行（在服務主控台中）。 如有需要，請重新開機，然後再次嘗試操作。
- [請確定暫存檔案夾位置中有5% 到10% 的可用磁片區空間](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)。
- [檢查另一個進程或防毒軟體是否干擾 Azure 備份](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)。
- 如果排定的備份失敗，但手動備份可運作，請參閱[備份不會根據排程執行](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)。
- 請確定您的 OS 有最新的更新。
- [請確定不支援的磁片磁碟機和具有不受支援屬性的檔案已從備份中排除](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)。
- 確保受保護系統上的時鐘已設定為正確的時區。
- [確定伺服器上已安裝 .NET Framework 4.5.2 或更新版本](https://www.microsoft.com/download/details.aspx?id=30653)。
- 如果您正嘗試將伺服器重新註冊至保存庫：
  - 請確認代理程式已在伺服器上卸載，而且已從入口網站刪除。
  - 使用一開始用來註冊伺服器的相同複雜密碼。
- 針對離線備份，請確定在開始備份之前，已在來源和複本電腦上安裝 Azure PowerShell 3.7.0。
- 如果備份代理程式是在 Azure 虛擬機器上執行，請參閱[這篇文章](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)。

## <a name="invalid-vault-credentials-provided"></a>提供的保存庫認證無效

**錯誤訊息**：提供的保存庫認證無效。 檔案已損毀或沒有與復原服務關聯的最新認證。 (識別碼：34513）

| 原因 | 建議動作 |
| ---     | ---    |
| **保存庫認證無效** <br/> <br/> 保存庫認證檔案可能已損毀或可能已過期。 （例如，在註冊時間之前，可能已下載超過48小時）。| 從 Azure 入口網站上的復原服務保存庫下載新的認證。 （請參閱[下載 MARS 代理程式](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent)一節中的步驟6）。然後適當地採取下列步驟： <ul><li> 如果您已經安裝並註冊 MARS，請開啟 Microsoft Azure 備份代理程式 MMC 主控台，然後選取 [**動作**] 窗格中的 [**註冊伺服器**]，以新的認證完成註冊。 <br/> <li> 如果新安裝失敗，請嘗試使用新的認證重新安裝。</ul> **注意**：如果已下載多個保存庫認證檔案，則在接下來的48小時內只有最新的檔案有效。 我們建議您下載新的保存庫認證檔。
| **Proxy 伺服器/防火牆封鎖註冊** <br/>或 <br/>**沒有網際網路連線能力** <br/><br/> 如果您的電腦或 proxy 伺服器具有有限的網際網路連線能力，而且您不確定存取所需的 Url 時，註冊將會失敗。| 請採取下列步驟：<br/> <ul><li> 與您的 IT 小組合作，以確保系統具有網際網路連線能力。<li> 如果您沒有 proxy 伺服器，請確定您在註冊代理程式時未選取 [proxy] 選項。 [檢查您的 proxy 設定](#verifying-proxy-settings-for-windows)。<li> 如果您有防火牆/proxy 伺服器，請與您的網路小組合作，以確保這些 Url 和 IP 位址有存取權：<br/> <br> **URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> . microsoftonline.com <br> . windows.net <br>**IP 位址**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>完成上述的疑難排解步驟之後，請嘗試再次註冊。<br></br> 如果您是透過 Azure ExpressRoute 進行連線，請確定設定已依照[Azure expressroute 支援](backup-support-matrix-mars-agent.md#azure-expressroute-support)中的說明進行設定。
| **防毒軟體正在封鎖註冊** | 如果您已在伺服器上安裝防毒軟體，請在下列檔案和資料夾的防毒程式掃描中新增必要的排除規則： <br/><ul> <li> CBengine.exe <li> CSC .exe<li> 暫存檔案夾。 其預設位置為 C:\Program Files\Microsoft Azure Recovery Services Agent\scratch。 <li> C:\Program Files\Microsoft Azure Recovery Services Agent\Bin. 的 bin 資料夾

### <a name="additional-recommendations"></a>其他建議

- 移至 C:/Windows/Temp，並檢查具有 .tmp 副檔名的檔案是否超過 60,000 或 65,000 個。 如果有，請刪除這些檔案。
- 確定電腦的日期和時間符合當地時區。
- 請確定[這些網站](install-mars-agent.md#verify-internet-access)已新增至您在 Internet Explorer 的信任網站中。

### <a name="verifying-proxy-settings-for-windows"></a>正在驗證 Windows 的 proxy 設定

1. 從 [ [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) ] 頁面下載 PsExec。
1. 從提高許可權的命令提示字元執行 `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`。

   此命令將會開啟 Internet Explorer。
1. 移至 [**工具**] [ > ] [**網際網路選項**] **[ > 連線**] > **LAN 設定**。
1. 檢查系統帳戶的 proxy 設定。
1. 如果未設定 proxy，並提供 proxy 詳細資料，請移除詳細資料。
1. 如果已設定 proxy，而 proxy 詳細資料不正確，請確定**PROXY IP**和**埠**詳細資料正確。
1. 關閉 Internet Explorer。

## <a name="unable-to-download-vault-credential-file"></a>無法下載保存庫認證檔

| Error   | 建議動作 |
| ---     | ---    |
|無法下載保存庫認證檔案。 (識別碼：403) | <ul><li> 請嘗試使用不同的瀏覽器來下載保存庫認證，或採取下列步驟： <ul><li> 啟動 Internet Explorer。 選取 [F12]。 </li><li> 移至 [**網路**] 索引標籤，並清除 [快取] 和 [cookie]。 </li> <li> 重新整理頁面。<br></li></ul> <li> 檢查訂用帳戶是否已停用/過期。<br></li> <li> 檢查是否有任何防火牆規則封鎖該下載。 <br></li> <li> 請確定您沒有用盡保存庫的限制（每個保存庫50部電腦）。<br></li>  <li> 請確定使用者具有下載保存庫認證所需的 Azure 備份許可權，並向保存庫註冊伺服器。 請參閱[使用以角色為基礎的存取控制來管理 Azure 備份復原點](backup-rbac-rs-vault.md)。</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份

| Error  | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure 復原服務代理程式無法連接到 Microsoft Azure 備份。 (識別碼：100050）請檢查您的網路設定，並確定您能夠連線到網際網路。<li>（407）需要 Proxy 驗證。 |Proxy 正在封鎖連接。 |  <ul><li>在 Internet Explorer 中，移至 **工具**  > **網際網路選項**  > **安全性** > **網際網路**。 選取 [**自訂層級**]，並向下卷到 [檔案**下載**] 區段。 選取 [啟用]。<p>您也可能需要在 Internet Explorer 中將[url 和 IP 位址](install-mars-agent.md#verify-internet-access)新增至信任的網站。<li>變更設定以使用 Proxy 伺服器。 接著提供 Proxy 伺服器詳細資料。<li> 如果您的電腦具有有限的網際網路存取權，請確定電腦或 proxy 上的防火牆設定允許這些[url 和 IP 位址](install-mars-agent.md#verify-internet-access)。 <li>如果您已在伺服器上安裝防毒軟體，請將這些檔案從防毒軟體掃描中排除： <ul><li>CBEngine.exe (而不是 dpmra.exe)。<li>CSC.exe (與 .NET Framework 相關)。 伺服器上安裝的每個 .NET Framework 版本都有一個 CSC .exe。 在受影響的伺服器上排除所有 .NET Framework 版本的 CSC .exe 檔案。 <li>暫存檔案夾或快取位置。 <br>暫存檔案夾或快取路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\scratch。<li>C:\Program Files\Microsoft Azure Recovery Services Agent\Bin. 的 bin 資料夾

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>無法設定安全備份的加密金鑰

| Error | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| <br />無法設定安全備份的加密金鑰。 啟用未完全成功，但是加密複雜密碼已儲存至下列檔案。 |<li>伺服器已經向另一個保存庫註冊。<li>在設定期間，複雜密碼已損毀。| 從保存庫取消註冊伺服器，然後使用新的複雜密碼重新註冊。

## <a name="the-activation-did-not-complete-successfully"></a>啟動沒有成功完成

| Error  | 可能的原因 | 建議動作 |
|---------|---------|---------|
|<br />啟動沒有成功完成。 由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗。 請在一段時間之後重試此作業。 如果問題持續發生， 請連絡 Microsoft 支援服務。     | <li> 暫存檔案夾所在的磁片區沒有足夠的空間。 <li> 暫存資料夾的移動不正確。 <li> 遺失 OnlineBackup.KEK 檔案。         | <li>升級至[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理程式。<li>將暫存檔案夾或快取位置移至磁片區，其可用空間介於備份資料大小總計的5% 和10% 之間。 若要正確地移動快取位置，請參閱[有關備份檔案和資料夾的常見問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>*暫存檔案夾或快取路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*。        |

## <a name="encryption-passphrase-not-correctly-configured"></a>未正確設定加密複雜密碼

| Error  | 可能的原因 | 建議動作 |
|---------|---------|---------|
| <br />錯誤 34506。 未正確設定儲存在這部電腦上的加密複雜密碼。    | <li> 暫存檔案夾所在的磁片區沒有足夠的空間。 <li> 暫存資料夾的移動不正確。 <li> 遺失 OnlineBackup.KEK 檔案。        | <li>升級至[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理程式。<li>將暫存檔案夾或快取位置移至磁片區，其可用空間介於備份資料大小總計的5% 和10% 之間。 若要正確地移動快取位置，請參閱[有關備份檔案和資料夾的常見問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>*暫存檔案夾或快取路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*。         |

## <a name="backups-dont-run-according-to-schedule"></a>備份不會根據排程執行

如果未自動觸發排定的備份，但手動備份正常運作，請嘗試下列動作：

- 請確定 Windows Server backup 排程不會與 Azure 檔案和資料夾備份排程衝突。

- 確定 [線上備份] 狀態設定為 [**啟用**]。 若要確認狀態，請執行下列步驟：

  1. 在工作排程器中，展開 [ **Microsoft** ]，然後選取 [**線上備份**]。
  1. 按兩下 [Onlinebackup.kek]，然後移至 [**觸發** **程式**] 索引標籤。
  1. 檢查狀態是否設定為 [**已啟用**]。 如果不是，請選取 [**編輯**]，選取 [**已啟用**]，然後選取 **[確定]** 。

- 請確定選取要執行此工作的使用者帳戶是伺服器上的**系統**或**本機系統管理員群組**。 若要確認使用者帳戶，請移至 [**一般**] 索引標籤，並檢查**安全性**選項。

- 請確定伺服器上已安裝 PowerShell 3.0 或更新版本。 若要檢查 PowerShell 版本，請執行此命令，並確認 `Major` 版本號碼為3或更新版本：

  `$PSVersionTable.PSVersion`

- 請確定此路徑是 `PSMODULEPATH` 環境變數的一部分：

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 如果 `LocalMachine` 的 PowerShell 執行原則設定為 `restricted`，則觸發備份工作的 PowerShell Cmdlet 可能會失敗。 在提高許可權的模式中執行這些命令，以檢查執行原則，並將其設定為 `Unrestricted` 或 `RemoteSigned`：

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- 請確定沒有任何遺失或損毀的 PowerShell 模組 MSOnlineBackup 檔。 如果有任何遺失或損毀的檔案，請採取下列步驟：

  1. 從具有正常運作之 MARS 代理程式的任何電腦，複製 MSOnlineBackup 資料夾，從 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules。
  1. 在有問題的電腦上，將複製的檔案貼到相同的資料夾位置（C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules）。

     如果電腦上已有 MSOnlineBackup 資料夾，請將檔案貼入其中，或取代任何現有的檔案。

> [!TIP]
> 若要確保一致地套用變更，請在執行上述步驟之後重新開機伺服器。

## <a name="troubleshoot-restore-problems"></a>針對還原問題進行疑難排解

Azure 備份可能未成功掛接復原磁碟區，即使數分鐘後仍未成功。 而且，您可能會在處理過程中收到錯誤訊息。 若要開始正常復原，請採取下列步驟：

1. 如果掛接進程已執行數分鐘，請將其取消。

2. 檢查您是否有最新版本的備份代理程式。 若要檢查版本，請在 MARS 主控台的 [**動作**] 窗格中，選取 [**關於 Microsoft Azure 復原服務代理程式**]。 確認 [版本] 號碼等於或高於[這篇文章](https://go.microsoft.com/fwlink/?linkid=229525)中提到的版本。 選取此連結以[下載最新版本](https://go.microsoft.com/fwLink/?LinkID=288905)。

3. 移至**Device Manager** > **儲存體控制器**，並找出 [ **Microsoft iSCSI 啟動器**]。 如果您找到它，請直接移至步驟7。

4. 如果找不到 Microsoft iSCSI 啟動器服務，請嘗試在 [ **Device Manager** > **存放控制器**] 下尋找名為 [具有硬體識別碼**ROOT\ISCSIPRT**的**未知裝置**] 的專案。

5. 以滑鼠右鍵按一下 [**未知的裝置**]，然後選取 [**更新驅動程式軟體**]。

6. 藉由選取 [自動搜尋更新的驅動程式軟體] 的選項，以更新驅動程式。 此更新應將**未知裝置**變更為**Microsoft iSCSI 啟動器**：

    ![Azure 備份裝置管理員的螢幕擷取畫面，已反白顯示儲存體控制器](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. 移至 [**工作管理員**] **[ > 服務（本機）** ] > **Microsoft iSCSI 啟動器服務**：

    ![Azure 備份工作管理員的螢幕擷取畫面，已反白顯示服務 (本機)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. 重新啟動 Microsoft iSCSI 啟動器服務。 若要這麼做，請以滑鼠右鍵按一下服務，然後選取 [**停止**]。 然後以滑鼠右鍵按一下它，然後選取 [**啟動**]。

9. 使用 [立即還原](backup-instant-restore-capability.md) 重試復原。

如果復原仍然失敗，請重新開機您的伺服器或用戶端。 如果您不想要重新開機，或者即使在重新開機伺服器之後復原仍然失敗，請嘗試[從另一部電腦](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)修復。

## <a name="troubleshoot-cache-problems"></a>針對快取問題進行疑難排解

如果快取資料夾（也稱為「暫存檔案夾」）的設定不正確、缺少必要條件或限制存取，則備份作業可能會失敗。

### <a name="prerequisites"></a>必要條件

若要讓 MARS 代理程式作業成功，快取資料夾必須符合下列需求：

- [請確定暫存檔案夾位置中有5% 到10% 的可用磁片區空間](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [確定暫存檔案夾位置有效且可供存取](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [確保支援快取資料夾上的檔案屬性](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [確保已配置的陰影複製儲存空間足以進行備份程式](#increase-shadow-copy-storage)
- [請確定沒有其他進程（例如防毒軟體）限制對快取資料夾的存取](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>增加陰影複製儲存空間

如果保護資料來源所需的陰影複製儲存空間不足，備份作業可能會失敗。 若要解決此問題，請使用 vssadmin 來增加受保護磁片區上的陰影複製儲存空間，如下所示：

- 從提高許可權的命令提示字元中，檢查目前的陰影儲存空間：<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- 使用下列命令來增加陰影儲存空間：<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>另一個進程或防毒軟體封鎖了對快取資料夾的存取

如果您已在伺服器上安裝防毒軟體，請在下列檔案和資料夾的防毒程式掃描中新增必要的排除規則：  

- 暫存檔案夾。 其預設位置為 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- C:\Program Files\Microsoft Azure Recovery Services Agent\Bin 的 bin 資料夾
- CBengine.exe
- CSC .exe

## <a name="common-issues"></a>常見問題

本節涵蓋使用 MARS 代理程式時所遇到的常見錯誤。

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

錯誤訊息 | 建議的動作 |
-- | --
Microsoft Azure 復原服務代理程式無法存取臨時位置中所儲存的備份總和檢查碼 | 若要解決此問題，請執行下列，然後重新開機伺服器。 <br/> - [檢查是否有防毒軟體或其他進程鎖定臨時位置](#another-process-or-antivirus-software-blocking-access-to-cache-folder)檔案<br/> - [檢查臨時位置是否有效，以及是否可供 mars 代理程式存取。](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

錯誤訊息 | 建議的動作 |
-- | --
Microsoft Azure 復原服務代理程式無法存取臨時位置以初始化 VHD | 若要解決此問題，請執行下列，然後重新開機伺服器。 <br/> - [檢查是否有防毒軟體或其他進程鎖定臨時位置](#another-process-or-antivirus-software-blocking-access-to-cache-folder)檔案<br/> - [檢查臨時位置是否有效，以及是否可供 mars 代理程式存取。](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

錯誤訊息 | 建議的動作 |
-- | --
備份失敗，因為暫存資料夾所在的磁片區中的儲存空間不足 | 若要解決此問題，請確認下列步驟，然後重試此作業：<br/>- [確保 MARS 代理程式是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [確認並解決影響備份臨時空間的儲存體問題](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

錯誤訊息 | 建議的動作 |
-- | --
找不到檔案中的變更。 這可能是由各種原因所造成。 請重試該作業 | 若要解決此問題，請確認下列步驟，然後重試此作業：<br/> - [確保 MARS 代理程式是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [確認並解決影響備份臨時空間的儲存體問題](#prerequisites)

## <a name="next-steps"></a>後續步驟

- 取得有關[如何使用 Azure 備份代理程式來備份 Windows Server](tutorial-backup-windows-server-to-azure.md)的詳細資訊。
- 如果您需要還原備份，請參閱將[檔案還原至 Windows 電腦](backup-azure-restore-windows-server.md)。
