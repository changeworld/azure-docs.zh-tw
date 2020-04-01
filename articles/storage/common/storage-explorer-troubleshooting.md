---
title: Azure 儲存體總管疑難排解指南 | Microsoft Docs
description: Azure 儲存資源管理員的除錯技術概述
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: db36033ea524603416f16db27f40d5eefb8bf613
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437110"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure 儲存體總管疑難排解指南

Microsoft Azure 儲存資源管理器是一款獨立應用,它便於在 Windows、macOS 和 Linux 上處理 Azure 儲存數據。 應用可以連接到託管在 Azure、國家雲和 Azure 堆疊上的存儲帳戶。

本指南總結了存儲資源管理器中常見的問題的解決方案。

## <a name="rbac-permissions-issues"></a>RBAC 權限問題

基於角色的訪問控制[RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)通過將許可權集合並到_角色_中,支援 Azure 資源的高度精細訪問管理。 以下是一些策略,使 RBAC 在儲存資源管理器中以最佳方式工作。

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>如何在存儲資源管理器中訪問我的資源?

如果您在透過 RBAC 存取儲存資源時遇到問題,則可能沒有分配相應的角色。 以下各節介紹存儲資源管理器當前存取存儲資源所需的許可權。 如果您不確定是否具有適當的角色或許可權,請與 Azure 帳戶管理員聯繫。

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"讀取:清單/取得儲存帳戶"權限問題

您必須具有列出存儲帳戶的許可權。 要獲得此許可權,必須為 _「讀取器」_ 角色分配。

#### <a name="list-storage-account-keys"></a>列出儲存體帳戶金鑰

存儲資源管理員還可以使用帳戶密鑰對請求進行身份驗證。 您可以通過更強大的角色(如_參與者_角色)訪問帳戶密鑰。

> [!NOTE]
> 訪問金鑰向持有金鑰的任何人授予不受限制的許可權。 因此,我們不建議您向帳戶使用者分發這些密鑰。 如果需要撤銷訪問金鑰,可以從[Azure 門戶](https://portal.azure.com/)重新生成它們。

#### <a name="data-roles"></a>資料角色

必須至少分配一個角色,該角色授予從資源讀取數據訪問許可權。 例如,如果要列出或下載 Blob,至少需要_儲存 Blob 資料讀取器_角色。

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>為什麼我需要管理層角色才能在存儲資源管理器中查看我的資源?

Azure 儲存有兩層存取:_管理與__資料_。 通過管理層訪問訂閱和存儲帳戶。 容器、blob 和其他數據資源通過數據層訪問。 例如,如果要從 Azure 獲取存儲帳戶的清單,則可以向管理終結點發送請求。 如果需要帳戶中的 Blob 容器清單,則可以向相應的服務終結點發送請求。

RBAC 角色可以包含管理或數據層訪問的許可權。 例如,Reader 角色授予對管理層資源的唯讀訪問許可權。

嚴格地說,Reader 角色不提供數據層許可權,並且不需要訪問數據層。

存儲資源管理員通過收集連接到 Azure 資源所需的資訊,從而輕鬆存取資源。 例如,要顯示 Blob 容器,儲存資源管理員向 Blob 服務終結點發送「清單容器」請求。 要取得該終結點,儲存資源管理員將搜索您有權存取的訂閱和儲存帳戶的清單。 要查找訂閱和存儲帳戶,存儲資源管理員還需要訪問管理層。

如果您沒有授予任何管理層許可權的角色,則儲存資源管理員無法獲取連接到資料層所需的資訊。

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>如果我無法從管理員那裡獲得所需的管理層許可權,該怎麼辦?

我們目前沒有與此問題進行RBAC相關的解決方案。 作為解決方法,您可以請求 SAS URI[附加到資源](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri)。

### <a name="recommended-built-in-rbac-roles"></a>建議的內建 RBAC 角色

有幾個內建 RBAC 角色可以提供使用存儲資源管理員所需的許可權。 其中一些角色包括:
- [擁有者](/azure/role-based-access-control/built-in-roles#owner):管理所有內容,包括訪問資源。 **注意**:此角色將為您提供密鑰訪問許可權。
- [參與者](/azure/role-based-access-control/built-in-roles#contributor):管理一切,不包括對資源的訪問。 **注意**:此角色將為您提供密鑰訪問許可權。
- [讀取器](/azure/role-based-access-control/built-in-roles#reader):讀取和列出資源。
- [儲存帳戶參與者](/azure/role-based-access-control/built-in-roles#storage-account-contributor):存儲帳戶的完整管理。 **注意**:此角色將為您提供密鑰訪問許可權。
- [儲存 Blob 資料擁有者](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner):對 Azure 儲存 Blob 容器和資料的完全存取許可權。
- [儲存 Blob 資料參與者](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor):讀取、寫入和刪除 Azure 儲存容器和 Blob。
- [儲存 Blob 資料讀取器](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader):讀取並列出 Azure 儲存容器和 Blob。

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>錯誤:憑證鍊中的自簽署憑證(以及類似的錯誤)

憑證錯誤通常發生在以下情況之一:

- 該應用程式通過_透明代理_連接,這意味著伺服器(如公司伺服器)正在攔截 HTTPS 流量,解密它,然後使用自簽名證書對其進行加密。
- 正在執行的應用程式正在將自簽名 TLS/SSL 憑證注入您收到的 HTTPS 訊息中。 注入證書的應用程式示例包括防病毒和網路流量檢查軟體。

當儲存資源管理員看到自簽名或不受信任的證書時,它不再知道收到的 HTTPS 消息是否已更改。 如果您有自簽署憑證的複本,則可以按照以下步驟指示儲存資源管理員信任它:

1. 獲取證書的 Base-64 編碼 X.509 (.cer) 複本。
2. 跳到**編輯** > **SSL 憑證** > **匯入憑證**,然後使用檔案選取器搜尋、選擇和打開 .cer 檔案。

如果有多個證書(根證書和中間證書),也可能發生此問題。 要修復此錯誤,必須添加兩個證書。

如果您不確定憑證來自何處,請按照以下步驟查找證書:

1. 安裝 OpenSSL。
    * [視窗](https://slproweb.com/products/Win32OpenSSL.html):任何光版本都應該足夠。
    * Mac 和 Linux:應該包含在您的作業系統中。
2. 運行打開 SSL。
    * Windows:開啟安裝目錄,選擇 **/bin/**,然後按兩下**openssl.exe**。
    * Mac 與 Linux:從`openssl`終端執行 。
3. 執行 `s_client -showcerts -connect microsoft.com:443`。
4. 尋找自我簽署憑證。 如果您不確定哪些證書是自簽名的,請注意主題`("s:")`和頒發`("i:")`者 相同的任何位置。
5. 當您找到自簽名證書時,對於每個證書,將從(包括)`-----BEGIN CERTIFICATE-----``-----END CERTIFICATE-----`到的新 .cer 檔的所有內容複製並貼上到檔案中。
6. 開啟儲存資源管理員並轉到**編輯** > **SSL 憑證** > **匯入憑證**。 然後使用檔案選取器搜尋、選擇和打開您建立的 .cer 檔案。

如果您無法通過以下步驟找到任何自簽名證書,請通過反饋工具與我們聯繫。 您還可以使用`--ignore-certificate-errors`標誌從命令行打開儲存資源管理員。 使用此標誌打開時,儲存資源管理員將忽略證書錯誤。

## <a name="sign-in-issues"></a>登入問題

### <a name="blank-sign-in-dialog-box"></a>空白登入對話框

當 Active 目錄聯合服務 (AD FS) 提示儲存資源管理員執行重定向時,最常出現空白登錄對話框,該重定向不受 Electron 的支援。 要解決此問題,可以嘗試使用設備代碼流進行登錄。 若要這樣做，請依照下列步驟執行：

1. 在左側垂直工具列上,打開 **"設置**"。 在「設定」面板中,轉到**應用程式** > **登入**。 開啟**使用裝置碼串流登入**。
2. 打開**連接「對話**框(通過左側垂直欄上的插頭圖示或透過在帳戶面板上選擇 **」添加帳戶**」)。
3. 選擇要登錄的環境。
4. 選取 [登入]****。
5. 遵循下一個面板上的指示。

如果無法登入要使用的帳戶,因為預設瀏覽器已登入到其他帳戶,請執行以下操作之一:

- 手動將連結和程式碼複製到您瀏覽器的隱私工作階段。
- 手動將連結和程式碼複製到其他瀏覽器。

### <a name="reauthentication-loop-or-upn-change"></a>重新驗證迴圈或 UPN 變更

如果您處於重新身份驗證迴圈中或更改了其中一個帳戶的 UPN,請按照以下步驟操作:

1. 刪除所有帳戶,然後關閉存儲資源管理員。
2. 從您的機器中刪除 .IdentityService 資料夾。 在 Windows 中，該資料夾位於 `C:\users\<username>\AppData\Local`。 對於 Mac 和 Linux，您可以在使用者目錄的根目錄中找到此資料夾。
3. 如果您正在運行 Mac 或 Linux,則還需要從作業系統的密鑰庫中刪除 Microsoft.Developer.身份服務條目。 在 Mac 上,金鑰庫是*Gnome 鑰匙串*應用程式。 在 Linux 中,應用程式通常稱為_Keyring_,但名稱可能因您的發行情況而異。

### <a name="conditional-access"></a>條件式存取

由於儲存資源管理員使用的 Azure AD 庫中存在限制,因此在 Windows 10、Linux 或 macOS 上使用儲存資源管理員時不支援條件訪問。

## <a name="mac-keychain-errors"></a>Mac Keychain 錯誤

macOS 鑰匙串有時會進入導致存儲資源管理器身份驗證庫問題的狀態。 要使鑰匙串退出此狀態,請按照以下步驟操作:

1. 關閉 [儲存體總管]。
2. 打開鑰匙串(按命令+空格鍵,鍵入**鑰匙串**,然後按 Enter)。
3. 選擇「登錄」鑰匙串。
4. 選擇掛鎖圖示以鎖定鑰匙串。 (當行程完成時,掛鎖將顯示為鎖定。 這可能需要幾秒鐘的時間,具體取決於您打開的應用)。

    ![掛鎖圖示](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. 開啟儲存體總管。
6. 系統會提示您出現"服務中心想要訪問鑰匙串"之類的消息。 輸入 Mac 管理員帳戶密碼並選擇 **「始終允許**」(或」如果**始終不允許**不可用「則**允許**)。
7. 嘗試登入。

### <a name="general-sign-in-troubleshooting-steps"></a>一般登入疑難排解步驟

* 如果您在 macOS 上,並且登入視窗從未顯示在 **「等待身份認證」** 對話框中,請試[著 以下步驟](#mac-keychain-errors)。
* 重新啟動存儲資源管理員。
* 如果驗證視窗空白，請在關閉驗證對話方塊之前先等待至少一分鐘。
* 確保代理和證書設置已正確配置,適用於您的電腦和存儲資源管理員。
* 如果您正在運行 Windows 並在同一台電腦上有權訪問 Visual Studio 2019 和登入認證,請嘗試登入 Visual Studio 2019。 成功登錄 Visual Studio 2019 後,您可以打開儲存資源管理員,並在帳戶面板中查看您的帳戶。

如果這些方法都不起作用,則[在 GitHub 中打開問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。

### <a name="missing-subscriptions-and-broken-tenants"></a>訂用帳戶遺失和租用戶損毀

如果在成功登錄後無法檢索訂閱,請嘗試以下故障排除方法:

* 確認您的帳戶可存取預期的訂用帳戶。 您可以通過登錄到要使用的 Azure 環境的門戶來驗證訪問許可權。
* 確保已通過正確的 Azure 環境(Azure、Azure 中國 21Vianet、Azure 德國、Azure 美國政府或自定義環境)登錄。
* 如果您在代理伺服器後面,請確保已正確配置存儲資源管理器代理。
* 請嘗試刪除並重新添加帳戶。
* 如果存在"詳細資訊"連結,請檢查正在報告失敗租戶的錯誤消息。 如果您不確定如何回應錯誤訊息,請隨時[在 GitHub 中開啟問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>無法移除附加的帳戶或儲存資源

如果無法透過 UI 刪除附加的帳戶或儲存資源,則可以通過刪除以下資料夾手動刪除所有附加資源:

* Windows：`%AppData%/StorageExplorer`
* macOS：`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux：`~/.config/StorageExplorer`

> [!NOTE]
> 在刪除這些資料夾之前關閉儲存資源管理員。

> [!NOTE]
> 如果您曾經導入過任何 SSL 證書,請`certs`備份 目錄的內容。 稍後，您可以使用備份來重新匯入 SSL 憑證。

## <a name="proxy-issues"></a>Proxy 問題

首先,請確保您輸入的以下資訊正確:

* Proxy URL 和連接埠號碼
* 使用者名稱和密碼,如果代理需要他們

> [!NOTE]
> 儲存資源管理員不支援用於設定代理設定的代理自動設定檔。

### <a name="common-solutions"></a>常見的解決方案

如果仍然遇到問題,請嘗試以下故障排除方法:

* 如果無需使用代理即可連接到互聯網,請驗證存儲資源管理員是否工作而不啟用代理設置。 如果是這種情況，可能是 proxy 設定發生問題。 與管理員合作確定問題。
* 驗證使用代理伺服器的其他應用程式是否按預期工作。
* 驗證是否可以連接到要嘗試使用的 Azure 環境的門戶。
* 確認您可以收到服務端點的回應。 在瀏覽器中輸入其中一個端點 URL。 如果可以連接,應收到無效查詢參數值或類似 XML 回應。
* 如果有其他人也用您的 proxy 伺服器使用儲存體總管，請確認他們可以連線。 如果可以,您可能需要聯繫代理伺服器管理員。

### <a name="tools-for-diagnosing-issues"></a>診斷問題的工具

如果您有網路工具(如 Windows 的 Fiddler),您可以按照以下方式診斷問題:

* 如果必須透過 proxy 工作，您可能必須設定網路工具透過 proxy 連線。
* 檢查網路工具使用的連接埠號碼。
* 輸入本機主機 URL 和網路工具的連接埠號碼，當做儲存體總管的 proxy 設定。 正確執行此操作後,網路工具將開始將儲存資源管理員發出的網路請求記錄到管理和服務終結點。 例如,在瀏覽器`https://cawablobgrs.blob.core.windows.net/`中輸入 Blob 終結點,您將收到類似於以下內容的回應:

  ![程式碼範例](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  此回應表明資源存在,即使您無法訪問它。

### <a name="contact-proxy-server-admin"></a>連絡 proxy 伺服器管理員

如果您的代理設定正確,您可能需要與代理伺服器管理員聯絡,以便:

* 確保代理不會阻止對 Azure 管理或資源終結點的流量。
* 確認 proxy 伺服器使用的驗證通訊協定。 儲存資源管理員目前不支援 NTLM 代理。

## <a name="unable-to-retrieve-children-error-message"></a>「無法擷取子系」錯誤訊息

如果透過代理連線到 Azure,請驗證代理設定是否正確。 如果您從訂閱或帳戶的擁有者被授予對資源的許可權,請驗證您是否具有該資源的讀取或清單許可權。

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>連接字串沒有完整的設定設定

如果您收到此錯誤消息,則可能沒有必要的許可權來獲取存儲帳戶的密鑰。 要確認情況如此,請轉到門戶並找到您的存儲帳戶。 您可以通過右鍵單擊儲存帳戶的節點並選擇 **「在門戶中打開**」來執行此操作。 然後,轉到**訪問密鑰**邊欄選項卡。 如果您沒有檢視金鑰的許可權,您將看到"您無權訪問"消息。 要解決此問題,可以從其他人處獲取帳戶密鑰並通過名稱和密鑰附加,也可以向某人要求 SAS 到存儲帳戶,並用它來附加存儲帳戶。

如果您確實看到帳戶金鑰,可以在 GitHub 中提交問題,以便我們可以説明您解決問題。

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>添加新連接時出錯:TypeError:無法讀取未定義的屬性"版本"

如果在嘗試添加自定義連接時收到此錯誤消息,則存儲在本地憑據管理器中的連接數據可能已損壞。 要解決此問題,請嘗試刪除已損壞的本地連接,然後重新新增它們:

1. 啟動儲存體總管。 從功能表中轉到 **「幫助** > **切換開發人員工具**」。
2. 在開啟的視窗「**應用程式**」 選項卡上,轉到**本地端 (** 左側**file://**)>file:// 。
3. 根據遇到問題的連接類型,查找其密鑰,然後將其值複製到文本編輯器中。 該值是自定義連接名稱的陣列,如下所示:
    * 儲存體帳戶
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blob 容器
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * 檔案共用
        * `StorageExplorer_CustomConnections_Files_v1`
    * 佇列
        * `StorageExplorer_CustomConnections_Queues_v1`
    * 資料表
        * `StorageExplorer_CustomConnections_Tables_v1`
4. 儲存目前連線名稱後,在開發人員工具中將值設定為`[]`。

如果要保留未損壞的連接,可以使用以下步驟查找損壞的連接。 如果您不介意丟失所有現有連接,可以跳過這些步驟,並按照特定於平台的說明清除連接數據。

1. 從文字編輯器中,將每個連接名稱重新添加到開發人員工具,然後檢查連接是否仍然有效。
2. 如果連接工作正常,則未損壞,您可以安全地將其保留在那裡。 如果連接不起作用,請從開發人員工具中刪除其值,然後將其記錄,以便以後可以將其添加回來。
3. 重複上述步驟,直到檢查所有連接。

完成所有連線後,對於未添加回的所有連接名稱,必須清除其損壞的數據(如果有),並使用儲存資源管理員中的標準步驟將其添加回來:

# <a name="windows"></a>[Windows](#tab/Windows)

1. 在 **「開始」** 選單上,搜索**認證管理員**並打開它。
2. 跳到**Windows 認證 。**
3. 在 **「通用認證」** 下,尋找有`<connection_type_key>/<corrupted_connection_name>`鍵的項目(例如, `StorageExplorer_CustomConnections_Accounts_v1/account1`。
4. 刪除這些項目並重新新增連線。

# <a name="macos"></a>[macOS](#tab/macOS)

1. 開啟光碟燈 (指令+ 空白鍵) 並搜尋**鑰匙串存取**。
2. 查找具有鍵的`<connection_type_key>/<corrupted_connection_name>`條目(例如`StorageExplorer_CustomConnections_Accounts_v1/account1`,
3. 刪除這些項目並重新新增連線。

# <a name="linux"></a>[Linux](#tab/Linux)

本地憑據管理因 Linux 發行版而異。 如果您的 Linux 發行版不提供用於本地認證管理的內建 GUI 工具,則可以安裝第三方工具來管理本地認證。 例如,您可以使用[海馬](https://wiki.gnome.org/Apps/Seahorse/),一個開源GUI工具來管理Linux本地憑據。

1. 打開本地憑據管理工具,然後查找已保存的憑據。
2. 查找具有鍵的`<connection_type_key>/<corrupted_connection_name>`條目(例如`StorageExplorer_CustomConnections_Accounts_v1/account1`,
3. 刪除這些項目並重新新增連線。
---

如果您在執行這些步驟後仍遇到此錯誤,或者想要分享您懷疑損壞連接的內容,則在我們的 GitHub 頁面上[開啟問題](https://github.com/microsoft/AzureStorageExplorer/issues)。

## <a name="issues-with-sas-url"></a>SAS URL 問題

如果您透過 SAS 網址 連線到服務並遇到錯誤:

* 請確認 URL 提供讀取或列出資源的必要權限。
* 請確認 URL 尚未過期。
* 如果 SAS URL 是以存取原則為基礎，請確認尚未撤銷存取原則。

如果您不小心使用無效的 SAS URL 連接,現在無法分離,請按照以下步驟操作:

1. 運行儲存資源管理員時,按 F12 打開開發人員工具視窗。
2. 在 **「應用程式」** 選項卡上,選擇左側樹中的**本地存儲** > **file://。**
3. 尋找與有問題的 SAS URI 服務類型相關聯的索引鍵。 例如，如果是 Blob 容器的 SAS URI 不正確，請尋找名為 `StorageExplorer_AddStorageServiceSAS_v1_blob` 的索引鍵。
4. 索引鍵的值應該是 JSON 陣列。 尋找與壞URI關聯的物件,然後將其刪除。
5. 按下 Ctrl+R，重新載入儲存體總管。

## <a name="linux-dependencies"></a>Linux 相依項目

存儲資源管理器 1.10.0 及更高版本可從快照存儲作為快照提供。 存儲資源管理器快照會自動安裝其所有依賴項,並在新版本的快照可用時更新。 安裝存儲資源管理器快照是推薦的安裝方法。

儲存資源管理員需要使用密碼管理員,您可能需要在儲存資源管理員正常工作之前手動連接密碼管理員。 您可以透過執行以下指令將儲存資源管理員連線到系統的密碼管理員:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

您還可以將應用程式下載為 .tar.gz 檔,但必須手動安裝依賴項。

> [!IMPORTANT]
> 僅支援 .tar.gz 下載中提供的儲存資源管理員。 其他分發尚未驗證,可能需要替代或附加包。

這些套件是 Linux 儲存資源管理員的最常見要求:

* [.NET 核心 2.2 執行時](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` 或 `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> 儲存資源管理器版本 1.7.0 和更早版本需要 .NET Core 2.0。 如果安裝了較新版本的 .NET Core,則必須[修補儲存資源管理程式](#patching-storage-explorer-for-newer-versions-of-net-core)。 如果您執行的儲存資源管理器 1.8.0 或更高版本,您應該能夠使用最多 .NET Core 2.2。 超過 2.2 的版本目前尚未驗證為工作。

# <a name="ubuntu-1904"></a>[Ubuntu 19.04](#tab/1904)

1. 下載存儲資源管理器。
2. 安裝[.NET 核心執行時](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current)。
3. 執行以下命令：
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. 下載存儲資源管理器。
2. 安裝[.NET 核心執行時](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current)。
3. 執行以下命令：
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. 下載存儲資源管理器。
2. 安裝[.NET 核心執行時](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current)。
3. 執行以下命令：
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404"></a>[烏本圖 14.04](#tab/1404)

1. 下載存儲資源管理器。
2. 安裝[.NET 核心執行時](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current)。
3. 執行以下命令：
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>修補儲存資源管理員,用於較新版本的 .NET Core

對於儲存資源管理員 1.7.0 或更早版本,您可能需要修補儲存資源管理員使用的 .NET Core 版本:

1. [從 NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)下載流 JonRpc 的版本 1.5.43。 查找頁面右側的"下載包"連結。
2. 下載套件後,將檔案副檔名`.nupkg`從變更`.zip`為 。
3. 解壓縮包。
4. 開啟 `streamjsonrpc.1.5.43/lib/netstandard1.1/` 資料夾。
5. 複製到`StreamJsonRpc.dll`儲存資源管理員資料夾中的以下位置:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Azure 門戶中的「打開資源管理器」不起作用

如果 Azure 門戶上的 **「打開資源管理員」** 按鈕不起作用,請確保使用的是相容的瀏覽器。 以下瀏覽器已過相容性測試:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>後續步驟

如果這些解決方案都不起作用,則[在 GitHub 中開啟問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。 您還可以透過在左下角選擇 **「 向 GitHub 報告問題**」按鈕來執行此操作。

![意見反應](./media/storage-explorer-troubleshooting/feedback-button.PNG)
