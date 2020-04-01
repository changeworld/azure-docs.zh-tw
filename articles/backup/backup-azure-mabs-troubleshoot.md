---
title: 針對 Azure 備份伺服器進行疑難排解
description: 針對安裝、註冊「Azure 備份伺服器」以及備份和還原應用程式工作負載進行疑難排解。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: deff49a7d00a335c396a6fa36d3846ef353331c5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421285"
---
# <a name="troubleshoot-azure-backup-server"></a>針對 Azure 備份伺服器進行疑難排解

針對使用「Azure 備份伺服器」時所遇到的錯誤，請使用下表中的資訊進行疑難排解。

## <a name="basic-troubleshooting"></a>基本疑難排解

我們建議您在開始對 Microsoft Azure 備份伺服器 (MABS) 進行故障排除之前執行以下驗證:

- [確保 Microsoft Azure 回復服務 (MARS) 代理是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [確保 MARS 代理程式和 Azure 之間具有網路連線能力](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- 確保 Microsoft Azure 復原服務正在執行中 (在服務主控台中)。 如有必要,重新啟動並重試操作
- [確保草稿資料夾位置具有 5-10% 的磁碟區空間可供使用](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- 如果註冊失敗,請確保試著安裝 Azure 備份伺服器的伺服器尚未註冊到其他保管庫
- 若推送安裝失敗，請檢查是否已有 DPM 代理程式。 若已有，請解除安裝代理程式，然後重試安裝
- [確保沒有其他程序或防毒軟體干擾 Azure 備份](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- 確保 SQL 代理服務正在執行,並在 MABS 伺服器中設定為自動<br>

## <a name="invalid-vault-credentials-provided"></a>提供的保存庫認證無效

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 註冊至保存庫 | 提供的保存庫認證無效。 檔案已損毀或沒有與復原服務關聯的最新認證。 | 建議的動作： <br> <ul><li> 從保存庫下載最新的認證檔案，然後再試一次。 <br>(或)</li> <li> 如果前述動作沒有用，請嘗試將認證下載至不同的本機目錄，或建立新的保存庫。 <br>(或)</li> <li> 請嘗試更新[本文](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#invalid-vault-credentials-provided)中所述的日期和時間設置。 <br>(或)</li> <li> 檢查看看 c:\windows\temp 中的檔案數是否超過 65000 個。 將過時檔案移至另一個位置，或刪除 [Temp] 資料夾中的項目。 <br>(或)</li> <li> 檢查憑證的狀態。 <br> a. 開啟 [管理電腦憑證]**** (在 [控制台] 中)。 <br> b. 展開**個人**節點與子節點**憑證**。<br> c.  刪除憑證**Windows Azure 工具**。 <br> d. 在「Azure 備份」用戶端中重試註冊。 <br> (或) </li> <li> 檢查看看是否已有任何適當的群組原則。 </li></ul> |

## <a name="replica-is-inconsistent"></a>複本不一致

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| Backup | 複本不一致 | 確認已開啟「保護群組」精靈中的自動一致性檢查選項。 有關副本不一致的原因和相關建議的詳細資訊,請參閱文章[副本不一致](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2006/cc161593(v=technet.10))。<br> <ol><li> 如果是系統狀態/BMR 備份，請確認受保護伺服器上已安裝 Windows Server Backup。</li><li> 檢查 DPM/「Microsoft Azure 備份伺服器」上 DPM 儲存體集區中的空間相關問題，並視需要配置儲存體。</li><li> 檢查受保護伺服器上「磁碟區陰影複製服務」的狀態。 如果它處於停用狀態，請將它設定為手動啟動。 在伺服器上啟動該服務。 然後返回 DPM/「Microsoft Azure 備份伺服器」主控台，並開始一致性檢查作業的同步處理。</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>線上復原點建立失敗

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| Backup | 線上復原點建立失敗 | **錯誤訊息**:Windows Azure 備份代理無法創建所選卷的快照。 <br> **解決方法**:嘗試增加副本和恢復點卷中的空間。<br> <br> **錯誤訊息**: Windows Azure 備份代理無法連線到 OBEngine 服務 <br> **因應措施**：請確認電腦上正在執行的服務清單中是否存在 OBEngine。 如果 OBEngine 服務並未執行，請使用 "net start OBEngine" 命令來啟動 OBEngine 服務。 <br> <br> **錯誤訊息**:未設定此伺服器的加密密碼。 請設定加密複雜密碼。 <br> **解決方法**:嘗試配置加密密碼短語。 如果失敗，請執行下列步驟： <br> <ol><li>確認臨時位置存在。 也就是登錄 **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config** 中所提到名稱為 **ScratchLocation** 的位置應該要存在。</li><li> 如果臨時位置存在，請嘗試使用舊的複雜密碼來重新註冊。 「每次您設定加密複雜密碼時，請都將它儲存在安全的位置。」**</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>原始和外部 DPM 伺服器必須註冊到同一保管庫

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 還原 | **錯誤代碼**: CBPServer 註冊 VaultT 與當前/ Vault 認證不符合 錯誤: 100110 <br/> <br/>**錯誤訊息**:原始和外部 DPM 伺服器必須註冊到同一保管庫 | **原因**:當您嘗試使用外部 DPM 恢復選項將檔從原始伺服器還原到備用伺服器時,以及正在恢復的伺服器以及從中備份數據的原始伺服器未與同一恢復服務保管庫關聯時,將發生此問題。<br/> <br/>**解決方法**要解決此問題,請確保原始伺服器和備用伺服器都註冊到同一保管庫。|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>VMware VM 的線上復原點建立作業失敗

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| Backup | VMware VM 的線上復原點建立作業失敗。 DPM 在嘗試取得變更追蹤資訊時，VMware 發生錯誤。 錯誤代碼 ─檔案故障 (ID 33621) |  <ol><li> 針對受影響的 VM，在 VMWare 上重設 CTK。</li> <li>確認 VMWare 上沒有獨立磁碟。</li> <li>停止對受影響的 VM 的保護,然後使用 **「刷新**」按鈕重新保護。 </li><li>針對受影響的 VM 執行 CC。</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>代理程式作業因為伺服器上的 DPM 代理程式協調員服務發生通訊錯誤而失敗

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 將代理程式推送至受保護的伺服器 | 代理程式作業因為 \<ServerName> 上的「DPM 代理程式協調員」服務發生通訊錯誤而失敗。 | **如果產品所示的建議動作沒有用，請執行下列步驟**： <ul><li> 如果要從不受信任的網域連接電腦,請按照[以下步驟操作](https://docs.microsoft.com/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019)。 <br> (或) </li><li> 如果要從受信任的域連接計算機,請使用[本博客](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726)中概述的步驟進行故障排除。 <br>(或)</li><li> 嘗試停用防毒功能來作為疑難排解步驟。 如果這能解決問題，請修改[這篇文章](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12))中建議的防毒設定。</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>安裝程式無法更新登錄中繼資料

| 作業 | 錯誤詳細資料 | 因應措施 |
|-----------|---------------|------------|
|安裝 | 安裝程式無法更新登錄中繼資料。 此更新失敗會導致儲存體過度耗用。 為了避免此更新 ReFS 修剪註冊表項。 | 調整註冊表鍵**SYSTEM_目前控制集\控制\檔案系統\參考開啟內聯修剪**。 將 Dword 值設為 1。 |
|安裝 | 安裝程式無法更新登錄中繼資料。 此更新失敗會導致儲存體過度耗用。 若要避免此問題，請更新 Volume SnapOptimization 登錄項目。 | 建立註冊表鍵**軟體_微軟資料保護管理器\配置\VolSnapOptimization_WriteIds**與空字串值。 |

## <a name="registration-and-agent-related-issues"></a>註冊與代理程式相關問題

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 將代理程式推送至受保護的伺服器 | 為伺服器指定的認證無效。 | **如果產品中顯示的建議操作不起作用,請執行以下步驟**: <br> 如[這篇文章](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)所指定，嘗試手動將保護代理程式安裝在生產伺服器上。|
| Azure 備份代理程式無法連線到 Azure 備份服務 (ID：100050) | Azure 備份代理程式無法連線到 Azure 備份服務。 | **如果產品中顯示的建議操作不起作用,請執行以下步驟**: <br>1. 從提升的提示符執行以下指令: **psexec-i-s"c:程式檔案\Internet 資源管理員_iexplore.exe**。 這會開啟 Internet Explorer 視窗。 <br/> 2. 轉到**工具** > **網際網路選項** > **連接** > **區網設定**。 <br/> 3. 更改設定以使用代理伺服器。 接著提供 Proxy 伺服器詳細資料。<br/> 4. 如果您的電腦的網際網路存取有限,請確保機器或代理上的防火牆設定允許這些[網址](install-mars-agent.md#verify-internet-access)和[IP 位址](install-mars-agent.md#verify-internet-access)。|
| Azure 備份代理程式安裝失敗 | Microsoft Azure 復原服務安裝失敗。 「Microsoft Azure 復原服務」安裝作業對系統所做的所有變更都已復原。 (ID：4024) | 手動安裝「Azure 代理程式」。

## <a name="configuring-protection-group"></a>設定保護群組

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 設定保護群組 | DPM 無法列舉受保護電腦 (受保護的電腦名稱) 上的應用程式元件。 | 在相關資料來源/元件層級的設定保護群組 UI 畫面上，選取 [重新整理]****。 |
| 設定保護群組 | 無法設定保護 | 如果受保護的伺服器是 SQL Server，請確認是否已將 sysadmin 角色權限提供給受保護電腦上的系統帳戶 (NTAuthority\System)，如[這篇文章](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757977(v=sc.12))所述。
| 設定保護群組 | 此保護群組在儲存體集區中的可用空間不足。 | 新增至儲存體集區的磁碟[不應包含分割區](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12))。 刪除磁碟上的任何現有磁碟區。 然後將它們新增至儲存體集區。|
| 原則變更 |無法修改備份原則。 錯誤：由於發生內部服務錯誤 [0x29834]，導致目前的作業失敗。 請在一段時間之後重試此作業。 如果問題持續發生， 請連絡 Microsoft 支援服務。 | **原因:**<br/>在三種情況下會發生此錯誤：已啟用安全性設定時、嘗試將保留範圍縮減至低於先前指定的最小值時，以及使用不支援的版本時。 (不支援的版本係指低於「Microsoft Azure 備份伺服器」2.0.9052 版和「Azure 備份伺服器」更新 1 的版本)。 <br/>**建議的操作:**<br/> 若要繼續進行原則相關的更新，請將保留期設定成大於所指定的最短保留期。 (最短保留期就每日而言是 7 天、就每週而言是 4 週、就每月而言是 3 個月，或就每年而言則是 1 年)。 <br><br>(選擇性) 另一個慣用的方法是更新備份代理程式和「Azure 備份伺服器」，以利用所有安全性更新。 |

## <a name="backup"></a>Backup

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| Backup | 執行作業時發生非預期的錯誤。 裝置未就緒。 | **如果產品所示的建議動作沒有用，請執行下列步驟：** <br> <ul><li>針對保護群組中的項目，將「陰影複製儲存」空間設定為無限，然後執行一致性檢查。<br></li> (或) <li>嘗試刪除現有保護群組，並建立多個新群組。 每個新保護群組中都應該有一個個別項目。</li></ul> |
| Backup | 如果您只要備份系統狀態，請確認受保護的電腦上有足夠的可用空間可儲存系統狀態備份。 | <ol><li>確認受保護電腦上已安裝 Windows Server Backup。</li><li>確認受保護電腦上有足夠的空間可儲存系統狀態。 進行這項確認的最簡單方式就是移至受保護的電腦、開啟 Windows Server Backup、一路點選選項，然後選取 [BMR]。 接著，UI 會告訴您需要多少空間。 開啟**WSB** > **本地** > **備份計劃** > **選擇備份設定** > **完整伺服器**(顯示大小)。 使用此大小進行驗證。</li></ol>
| Backup | BMR 的備份故障 | 如果 BMR 大小很大，請將一些應用程式檔案移到作業系統磁碟機後再重試。 |
| Backup | 在新 Microsoft Azure 備份伺服器上重新保護 VMware VM 的選項未顯示為可用添加。 | VMWare 屬性指向已停用的舊「Microsoft Azure 備份伺服器」執行個體。 若要解決此問題：<br><ol><li>在 VCenter (SC-VMM 的對等項目) 中，移至 [摘要]**** 索引標籤，然後移至 [自訂屬性]****。</li>  <li>從 [DPMServer]**** 值中刪除舊的「Microsoft Azure 備份伺服器」名稱。</li>  <li>返回新的「Microsoft Azure 備份伺服器」，然後修改 PG。  選取 [重新整理]**** 按鈕之後，就會顯示 VM 並含有可供新增到保護的核取方塊。</li></ol> |
| Backup | 存取檔案/共用資料夾時發生錯誤 | 嘗試修改防病毒設定,如本文中的建議[,在 DPM 伺服器上執行防病毒軟體](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12))。|

## <a name="change-passphrase"></a>變更複雜密碼

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 變更複雜密碼 |輸入的安全性 PIN 碼不正確。 請提供正確的安全性 PIN 碼以完成此作業。 |**原因:**<br/> 當您在執行重要作業 (例如變更複雜密碼) 時輸入無效或過期的安全性 PIN 碼時，就會發生此錯誤。 <br/>**建議的操作:**<br/> 若要完成作業，您必須輸入有效的安全性 PIN 碼。 若要取得 PIN 碼，請登入 Azure 入口網站並移至 [復原服務保存庫]。 然後轉到**設定** > **屬性** > **產生安全性 PIN**。 請使用這個 PIN 碼來變更複雜密碼。 |
| 變更複雜密碼 |作業失敗。 識別碼：120002 |**原因:**<br/>當已啟用安全性設定，或您嘗試變更複雜密碼，以及使用不支援的版本時，就會發生此錯誤。<br/>**建議的操作:**<br/> 若要變更複雜密碼，您必須先將備份代理程式更新至最基本版本，亦即 2.0.9052。 您也需要將「Azure 備份伺服器」更新至最基本的更新 1，然後輸入有效的安全性 PIN 碼。 若要取得 PIN 碼，請登入 Azure 入口網站並移至 [復原服務保存庫]。 然後轉到**設定** > **屬性** > **產生安全性 PIN**。 請使用這個 PIN 碼來變更複雜密碼。 |

## <a name="configure-email-notifications"></a>設定電子郵件通知

| 作業 | 錯誤詳細資料 | 因應措施 |
| --- | --- | --- |
| 使用 Office 365 帳戶來設定電子郵件通知 |錯誤 ID：2013| **原因:**<br> 嘗試使用 Office 365 帳戶 <br>**建議的操作:**<ol><li> 首先要確保 DPM 伺服器的「允許在接收連接器上匿名中繼」在 Exchange 上設置。 有關如何設定此功能的詳細資訊,請參閱[在接收連接器上允許匿名中繼](https://docs.microsoft.com/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019)。</li> <li> 如果您無法使用內部 SMTP 轉送，而需要使用 Office 365 伺服器來進行設定，您可以將 IIS 設定為轉送。 將 DPM 伺服器設定為[使用 IIS 將 SMTP 中繼到 O365。](https://docs.microsoft.com/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019)<br><br>  請務必使用使用者\@domain.com 格式,*而不是*網域\使用者。<br><br><li>將 DPM 指向為使用本機伺服器名稱作為 SMTP 伺服器、連接埠 587。 然後將它指向應作為電子郵件來源的使用者電子郵件。<li> DPM SMTP 設定頁面上的使用者名稱和密碼應該用於 DPM 所在網域的網域帳戶。 </li><br> 變更 SMTP 伺服器位址時，請對新設定進行變更、關閉 [設定] 方塊，然後重新開啟它來確定它反映新的值。  只是變更和測試不一定會讓新設定生效，因此以這種方式測試是最佳做法。<br><br>在進行此程序的期間，您可以隨時關閉 DPM 主控台並編輯下列登錄機碼來清除這些設定W：**HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> 刪除 SMTPPassword 和 SMTPUserName 機碼**。 您可以在再次啟動它時，將它們加回到 UI 中。

## <a name="common-issues"></a>常見問題

本節介紹在使用 Azure 備份伺服器時可能會遇到的常見錯誤。

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

錯誤訊息 | 建議的動作 |
-- | --
備份失敗，因為磁碟備份複本無效或遺漏。 | 要解決此問題,請驗證以下步驟並重試操作: <br/> 1. 建立磁碟復原點<br/> 2. 對資料源執行一致性檢查 <br/> 3. 停止資料來源保護,然後重新設定此資料來源的保護

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

錯誤訊息 | 建議的動作 |
-- | --
源卷快照失敗,因為副本上的元數據無效。 | 建立此資料來源的磁碟復原點,然後重試線上備份

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

錯誤訊息 | 建議的動作 |
-- | --
由於數據源副本不一致,源卷快照失敗。 | 在此資料來源執行一致性檢查,然後重試

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

錯誤訊息 | 建議的動作 |
-- | --
備份失敗，因為無法複製磁碟備份複本。| 確保取消安裝所有以前的磁碟備份複本檔 (.vhdx),並且連線備份期間沒有磁碟到磁碟備份
