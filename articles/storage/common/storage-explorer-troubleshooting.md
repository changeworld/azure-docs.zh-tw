---
title: Azure 儲存體總管疑難排解指南 | Microsoft Docs
description: Azure 儲存體總管的偵錯工具技術總覽
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 8bffe0c3871eae12f3b875a96301136d11dfc516
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783788"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure 儲存體總管疑難排解指南

Microsoft Azure 儲存體總管是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。 應用程式可以連線至裝載於 Azure、國家雲端和 Azure Stack 上的儲存體帳戶。

本指南摘要說明儲存體總管中常見的問題解決方案。

## <a name="azure-rbac-permissions-issues"></a>Azure RBAC 許可權問題

Azure 角色型存取控制 [AZURE RBAC](../../role-based-access-control/overview.md) 藉由將許可權集合合併為 _角色_ ，啟用對 Azure 資源的高度細微存取管理。 以下是讓 Azure RBAC 在儲存體總管中以最佳方式運作的一些策略。

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>如何? 在儲存體總管中存取我的資源？

如果您在透過 Azure RBAC 存取儲存體資源時發生問題，您可能未獲指派適當的角色。 下列各節說明目前存取儲存體資源所需的許可權儲存體總管。 如果您不確定您有適當的角色或許可權，請洽詢您的 Azure 帳戶管理員。

#### <a name="read-listget-storage-accounts-permissions-issue"></a>「讀取：列出/取得儲存體帳戶 (s) 」許可權問題

您必須擁有列出儲存體帳戶的許可權。 若要取得此許可權，您必須被指派「 _讀者_ 」角色。

#### <a name="list-storage-account-keys"></a>列出儲存體帳戶金鑰

儲存體總管也可以使用帳戶金鑰來驗證要求。 您可以透過更強大的角色取得帳戶金鑰的存取權，例如「 _參與者_ 」角色。

> [!NOTE]
> 存取金鑰會將不受限制的許可權授與給持有這些許可權的任何人。 因此，我們不建議您將這些金鑰交給帳戶使用者。 如果您需要撤銷存取金鑰，您可以從 [Azure 入口網站](https://portal.azure.com/)重新產生它們。

#### <a name="data-roles"></a>資料角色

您必須至少指派一個角色，以授與從資源讀取資料的存取權。 例如，如果您想要列出或下載 blob，則至少需要 _儲存體 Blob 資料讀取器_ 角色。

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>為什麼我需要管理層角色才能查看儲存體總管中的資源？

Azure 儲存體有兩層存取權： _管理_ 和 _資料_ 。 訂用帳戶和儲存體帳戶是透過管理層來存取。 您可以透過資料層存取容器、blob 和其他資料資源。 例如，如果您想要從 Azure 取得儲存體帳戶的清單，請將要求傳送至管理端點。 如果您想要帳戶中的 blob 容器清單，請將要求傳送至適當的服務端點。

Azure 角色可授與您管理或資料層存取權的許可權。 例如，讀取者角色會授與管理層資源的唯讀存取權。

嚴格來說，「讀者」角色不提供資料層許可權，也不需要存取資料層。

儲存體總管可透過收集連接至 Azure 資源所需的資訊，讓您輕鬆地存取您的資源。 例如，若要顯示您的 blob 容器，儲存體總管將「列出容器」要求傳送至 blob 服務端點。 為了取得該端點，儲存體總管會搜尋您可存取的訂用帳戶和儲存體帳戶清單。 若要尋找您的訂用帳戶和儲存體帳戶，儲存體總管也需要存取管理層。

如果您沒有授與任何管理層許可權的角色，儲存體總管無法取得連接到資料層所需的資訊。

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>如果我無法取得我的系統管理員所需的管理層許可權，該怎麼辦？

如果您想要存取 blob 容器或佇列，您可以使用您的 Azure 認證來附加至這些資源。

1. 開啟 [連線] 對話方塊。
2. 選取 [透過 Azure Active Directory (新增資源] Azure AD) 。 按一下 [下一步]。
3. 選取與您要附加之資源相關聯的使用者帳戶和租使用者。 按一下 [下一步]。
4. 選取資源類型、輸入資源的 URL，然後輸入唯一的連接顯示名稱。 按一下 [下一步]。 按一下 [連線]。

針對其他資源類型，我們目前沒有 Azure RBAC 相關的解決方案。 因應措施是，您可以要求 SAS URI 以 [附加至您的資源](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#use-a-shared-access-signature-uri)。

### <a name="recommended-azure-built-in-roles"></a>建議的 Azure 內建角色

有數個 Azure 內建角色可提供使用儲存體總管所需的許可權。 其中一些角色包括：
- [擁有](../../role-based-access-control/built-in-roles.md#owner)者：管理所有專案，包括對資源的存取權。 **注意** ：此角色可讓您存取金鑰。
- [參與者](../../role-based-access-control/built-in-roles.md#contributor)：管理所有專案，但不包括對資源的存取權。 **注意** ：此角色可讓您存取金鑰。
- [讀取器](../../role-based-access-control/built-in-roles.md#reader)：讀取和列出資源。
- [儲存體帳戶參與者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)：儲存體帳戶的完整管理。 **注意** ：此角色可讓您存取金鑰。
- [儲存體 Blob 資料擁有](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)者： Azure 儲存體 Blob 容器和資料的完整存取權。
- [儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：讀取、寫入和刪除 Azure 儲存體的容器和 blob。
- [儲存體 Blob 資料讀取器](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)：讀取和列出 Azure 儲存體的容器和 blob。

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>錯誤：憑證鏈中的自我簽署憑證 (和類似的錯誤) 

憑證錯誤通常會在下列其中一種情況下發生：

- 應用程式是透過透明的 _proxy_ 進行連線。 這表示伺服器 (例如您的公司伺服器) 正在攔截 HTTPS 流量、解密，然後使用自我簽署憑證將它加密。
- 您正在執行的應用程式，會將自我簽署的 TLS/SSL 憑證插入您收到的 HTTPS 訊息中。 插入憑證的應用程式範例包括防毒軟體和網路流量檢查軟體。

當儲存體總管看到自我簽署或未受信任的憑證時，就不再知道是否已變更接收的 HTTPS 訊息。 如果您有一份自我簽署憑證，您可以依照下列步驟來指示儲存體總管信任它：

1. 取得憑證的64編碼 x.509 ( .cer) 複本。
2. 移至 [ **編輯**  >  **SSL 憑證** 匯  >  **入憑證** ]，然後使用檔案選擇器來尋找、選取及開啟 .cer 檔案。

如果有多個憑證 (根和中繼) ，也可能會發生此問題。 若要修正這個錯誤，必須加入這兩個憑證。

如果您不確定憑證的來源，請遵循下列步驟來尋找它：

1. 安裝 OpenSSL。
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html)：任何燈光版本都必須足夠。
    * Mac 和 Linux：應該包含在您的作業系統中。
2. 執行 OpenSSL。
    * Windows：開啟安裝目錄，選取 [ **/bin/** ]，然後按兩下 [ **openssl.exe** ]。
    * Mac 和 Linux： `openssl` 從終端機執行。
3. 執行 `s_client -showcerts -connect microsoft.com:443`。
4. 尋找自我簽署憑證。 如果您不確定哪些憑證是自我簽署的憑證，請記下主體 `("s:")` 和簽發者的任何位置 `("i:")` 。
5. 當您找到自我簽署的憑證時，請將所有內容從 (複製並貼上，並將) 加入至 `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` 新的 .cer 檔案。
6. 開啟儲存體總管並移至 [ **編輯**  >  **SSL 憑證** 匯  >  **入憑證** ]。 然後使用檔案選擇器來尋找、選取及開啟您所建立的 .cer 檔案。

如果您無法依照下列步驟找到任何自我簽署的憑證，請透過意見反應工具來聯繫我們。 您也可以使用旗標，從命令列開啟儲存體總管 `--ignore-certificate-errors` 。 以這個旗標開啟時，儲存體總管會忽略憑證錯誤。

## <a name="sign-in-issues"></a>登入問題

### <a name="blank-sign-in-dialog-box"></a>空白登入對話方塊

當 Active Directory 同盟服務 (AD FS) 提示儲存體總管執行重新導向（Electron 不支援）時，通常會發生空白登入對話方塊。 若要解決此問題，您可以嘗試使用裝置程式碼流程進行登入。 若要這樣做，請依照下列步驟執行：

1. 在左垂直工具列上，開啟 [ **設定** ]。 在 [設定] 面板中，移至 [ **應用程式** 登  >  **入** ]。 啟用 [ **使用裝置程式碼流程登入** ]。
2. 開啟 [ **連接** ] 對話方塊 (透過左側垂直列上的插頭圖示，或選取 [帳戶] 面板上的 [ **新增帳戶** ]) 。
3. 選擇您要登入的環境。
4. 選取 [登入]。
5. 遵循下一個面板上的指示。

如果您無法登入您想要使用的帳戶，因為您的預設瀏覽器已登入不同的帳戶，請執行下列其中一項動作：

- 手動將連結和程式碼複製到您瀏覽器的隱私工作階段。
- 手動將連結和程式碼複製到其他瀏覽器。

### <a name="reauthentication-loop-or-upn-change"></a>重新驗證迴圈或 UPN 變更

如果您是在重新驗證的迴圈中，或已變更其中一個帳戶的 UPN，請遵循下列步驟：

1. 移除所有帳戶，然後關閉儲存體總管。
2. 從您的機器中刪除 .IdentityService 資料夾。 在 Windows 中，該資料夾位於 `C:\users\<username>\AppData\Local`。 對於 Mac 和 Linux，您可以在使用者目錄的根目錄中找到此資料夾。
3. 如果您執行的是 Mac 或 Linux，也需要從作業系統的金鑰儲存區中刪除 IdentityService 專案。 在 Mac 上，金鑰儲存區是 *Gnome Keychain* 應用程式。 在 Linux 中，應用程式通常稱為 _Keyring_ ，但名稱可能會隨著您的散發而有所不同。

### <a name="conditional-access"></a>條件式存取

因為儲存體總管所使用的 Azure AD 程式庫有限制，所以在 Windows 10、Linux 或 macOS 上使用儲存體總管時，不支援條件式存取。

## <a name="mac-keychain-errors"></a>Mac Keychain 錯誤

MacOS Keychain 有時會輸入導致儲存體總管 authentication library 發生問題的狀態。 若要取得此狀態的 Keychain，請遵循下列步驟：

1. 關閉 [儲存體總管]。
2. 開啟 Keychain (按下命令 + 空格鍵，輸入 **Keychain** ，然後按 enter 鍵) 。
3. 選取 [登入] Keychain。
4. 選取掛鎖圖示來鎖定 Keychain。  (當程式完成時，會鎖定掛鎖。 這可能需要幾秒鐘的時間，視您) 開啟的應用程式而定。

    ![掛鎖圖示](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. 開啟儲存體總管。
6. 系統會提示您輸入「服務中樞想要存取 Keychain」之類的訊息。 輸入您的 Mac 系統管理員帳戶密碼，然後選取 [ **一律** 允許] (，或 [ **允許** ] **) 不提供** [允許]。
7. 嘗試登入。

### <a name="general-sign-in-troubleshooting-steps"></a>一般登入疑難排解步驟

* 如果您是在 macOS，而且登入視窗永遠不會出現在 [ **等待驗證** ] 對話方塊中，請嘗試 [下列步驟](#mac-keychain-errors)。
* 重新開機儲存體總管。
* 如果驗證視窗空白，請在關閉驗證對話方塊之前先等待至少一分鐘。
* 請確定您的電腦和儲存體總管的 proxy 和憑證設定都已正確設定。
* 如果您執行的是 Windows，並且可以存取相同電腦上的 Visual Studio 2019 和登入認證，請嘗試登入 Visual Studio 2019。 成功登入 Visual Studio 2019 之後，您可以在 [帳戶] 面板中開啟儲存體總管並查看您的帳戶。

如果這些方法都無法運作，請 [在 GitHub 中開啟問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。

### <a name="missing-subscriptions-and-broken-tenants"></a>訂用帳戶遺失和租用戶損毀

如果您在成功登入後無法取得訂用帳戶，請嘗試下列疑難排解方法：

* 確認您的帳戶可存取預期的訂用帳戶。 您可以登入您想要使用的 Azure 環境入口網站，以驗證您的存取權。
* 請確定您已透過正確的 Azure 環境 (Azure、Azure 中國的世紀、Azure 德國、Azure 美國政府或自訂環境) 登入。
* 如果您是在 proxy 伺服器後方，請確定您已正確設定儲存體總管 proxy。
* 請嘗試移除並重新新增帳戶。
* 如果有 [詳細資訊] 連結，請檢查哪些錯誤訊息會回報給失敗的租使用者。 如果您不確定如何回應錯誤訊息，歡迎 [在 GitHub 中開啟問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>無法移除附加的帳戶或儲存體資源

如果您無法透過 UI 來移除附加的帳戶或儲存體資源，您可以刪除下列資料夾以手動刪除所有附加的資源：

* Windows：`%AppData%/StorageExplorer`
* macOS：`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux：`~/.config/StorageExplorer`

> [!NOTE]
> 在刪除這些資料夾之前，請先關閉儲存體總管。

> [!NOTE]
> 如果您曾經匯入任何 SSL 憑證，請備份目錄的內容 `certs` 。 稍後，您可以使用備份來重新匯入 SSL 憑證。

## <a name="proxy-issues"></a>Proxy 問題

首先，請確定您輸入的下列資訊是正確的：

* Proxy URL 和連接埠號碼
* 使用者名稱和密碼（如果 proxy 需要）

> [!NOTE]
> 儲存體總管不支援 proxy 自動設定檔案來設定 proxy 設定。

### <a name="common-solutions"></a>常見的解決方案

如果您仍然遇到問題，請嘗試下列疑難排解方法：

* 如果您可以連線到網際網路而不使用 proxy，請確認儲存體總管在未啟用 proxy 設定的情況下運作。 如果是這種情況，可能是 proxy 設定發生問題。 請與您的系統管理員合作以找出問題。
* 確認其他使用 proxy 伺服器的應用程式如預期般運作。
* 確認您可以連線到您想要使用的 Azure 環境入口網站。
* 確認您可以收到服務端點的回應。 在瀏覽器中輸入其中一個端點 URL。 如果您可以連接，您應該會收到 InvalidQueryParameterValue 或類似的 XML 回應。
* 如果有其他人也用您的 proxy 伺服器使用儲存體總管，請確認他們可以連線。 如果可以，您可能必須聯絡 proxy 伺服器系統管理員。

### <a name="tools-for-diagnosing-issues"></a>診斷問題的工具

如果您有網路工具，例如 Windows 的 Fiddler，您可以如下所示診斷問題：

* 如果必須透過 proxy 工作，您可能必須設定網路工具透過 proxy 連線。
* 檢查網路工具使用的連接埠號碼。
* 輸入本機主機 URL 和網路工具的連接埠號碼，當做儲存體總管的 proxy 設定。 當您這樣做時，您的網路工具就會開始記錄儲存體總管到管理和服務端點所提出的網路要求。 例如， `https://cawablobgrs.blob.core.windows.net/` 在瀏覽器中輸入您的 blob 端點，您會收到類似下列的回應：

  ![程式碼範例](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  此回應會建議資源存在，即使您無法存取也一樣。

### <a name="contact-proxy-server-admin"></a>連絡 proxy 伺服器管理員

如果您的 proxy 設定正確，您可能必須洽詢 proxy 伺服器管理員，才能：

* 請確定您的 proxy 不會封鎖 Azure 管理或資源端點的流量。
* 確認 proxy 伺服器使用的驗證通訊協定。 儲存體總管目前不支援 NTLM proxy。

## <a name="unable-to-retrieve-children-error-message"></a>「無法擷取子系」錯誤訊息

如果您是透過 proxy 連接到 Azure，請確認您的 proxy 設定是否正確。 如果您被授與訂用帳戶或帳戶擁有者的資源存取權，請確認您擁有該資源的讀取或列出許可權。

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>連接字串沒有完整的設定

如果您收到此錯誤訊息，可能是您沒有取得儲存體帳戶金鑰的必要許可權。 若要確認這是這種情況，請移至入口網站，並找出您的儲存體帳戶。 若要這麼做，您可以在儲存體帳戶的節點上按一下滑鼠右鍵，然後選取 [ **在入口網站中開啟** ]。 然後，移至 [ **存取金鑰** ] 分頁。 如果您沒有查看金鑰的許可權，您會看到「您沒有存取權」訊息。 若要解決此問題，您可以從其他人取得帳戶金鑰，並透過名稱和金鑰附加，或者您可以要求使用者提供儲存體帳戶的 SAS，並用它來附加儲存體帳戶。

如果您確實看到帳戶金鑰，請在 GitHub 中提出問題，讓我們可以協助您解決問題。

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>新增連接時發生錯誤： TypeError：無法讀取未定義的屬性 ' version '

如果您在嘗試新增自訂連接時收到這個錯誤訊息，儲存在本機認證管理員中的連接資料可能已損毀。 若要解決此問題，請嘗試刪除您損毀的本機連線，然後重新新增這些連接：

1. 啟動儲存體總管。 從功能表中， **移至 [** 說明]  >  **切換開發人員工具** 。
2. 在開啟的視窗中，于 [ **應用程式** ] 索引標籤上，移至 [ **本機儲存體** ] (左側) > **file://** ]。
3. 根據您遇到問題的連線類型，尋找其索引鍵，然後將其值複製到文字編輯器中。 值是您自訂連接名稱的陣列，如下所示：
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
4. 儲存目前的連接名稱之後，請將 [開發人員工具] 中的值設定為 `[]` 。

如果您想要保留未損毀的連接，您可以使用下列步驟來找出損毀的連接。 如果您不介意遺失所有現有的連接，可以略過這些步驟，並遵循平臺特定的指示來清除您的連接資料。

1. 從文字編輯器，將每個連接名稱重新加入開發人員工具，然後檢查連接是否仍在運作中。
2. 如果連線正常運作，它就不會損毀，您可以放心地將它留在那裡。 如果連接無法運作，請從開發人員工具中移除其值，並加以記錄，讓您稍後可以將它新增回來。
3. 重複直到您已檢查所有連接為止。

在完成所有連線之後，對於所有未新增的連接名稱，您必須清除其損毀的資料 (如果有任何) ，並使用儲存體總管中的標準步驟來將它們新增回來：

# <a name="windows"></a>[Windows](#tab/Windows)

1. 在 [ **開始** ] 功能表上，搜尋 **認證管理員** 並加以開啟。
2. 移至 [ **Windows 認證** ]。
3. 在 [ **一般認證** ] 底下，尋找具有 `<connection_type_key>/<corrupted_connection_name>` 金鑰 (的專案，例如 `StorageExplorer_CustomConnections_Accounts_v1/account1`) 。
4. 刪除這些專案，然後重新加入連接。

# <a name="macos"></a>[macOS](#tab/macOS)

1. 開啟焦點 (命令 + 空格鍵) 並搜尋 **Keychain 存取** 。
2. 尋找具有 `<connection_type_key>/<corrupted_connection_name>` 金鑰 (的專案，例如 `StorageExplorer_CustomConnections_Accounts_v1/account1`) 。
3. 刪除這些專案，然後重新加入連接。

# <a name="linux"></a>[Linux](#tab/Linux)

本機認證管理會根據 Linux 發行版本而有所不同。 如果您的 Linux 散發套件未提供內建的 GUI 工具來進行本機認證管理，您可以安裝協力廠商工具來管理本機認證。 例如，您可以使用 [Seahorse](https://wiki.gnome.org/Apps/Seahorse/)，這是一個開放原始碼 GUI 工具，可用來管理 Linux 本機認證。

1. 開啟您的本機認證管理工具，並尋找您儲存的認證。
2. 尋找具有 `<connection_type_key>/<corrupted_connection_name>` 金鑰 (的專案，例如 `StorageExplorer_CustomConnections_Accounts_v1/account1`) 。
3. 刪除這些專案，然後重新加入連接。
---

如果您在執行這些步驟之後仍遇到此錯誤，或如果您想要共用您懷疑已損毀的連線，請在我們的 GitHub 頁面上 [開啟問題](https://github.com/microsoft/AzureStorageExplorer/issues) 。

## <a name="issues-with-sas-url"></a>SAS URL 問題

如果您是透過 SAS URL 連接到服務，而且發生錯誤：

* 請確認 URL 提供讀取或列出資源的必要權限。
* 請確認 URL 尚未過期。
* 如果 SAS URL 是以存取原則為基礎，請確認尚未撤銷存取原則。

如果您不小心使用不正確 SAS URL 附加，且現在無法中斷連結，請遵循下列步驟：

1. 當您執行儲存體總管時，請按 F12 開啟 [開發人員工具] 視窗。
2. 在 [ **應用程式** ] 索引標籤上，選取左側樹狀結構中的 [ **本機儲存體**  >  **file://** ]。
3. 尋找與有問題的 SAS URI 服務類型相關聯的索引鍵。 例如，如果是 Blob 容器的 SAS URI 不正確，請尋找名為 `StorageExplorer_AddStorageServiceSAS_v1_blob` 的索引鍵。
4. 索引鍵的值應該是 JSON 陣列。 找出與錯誤 URI 相關聯的物件，然後將其刪除。
5. 按下 Ctrl+R，重新載入儲存體總管。

## <a name="linux-dependencies"></a>Linux 相依項目

### <a name="snap"></a>單元

儲存體總管1.10.0 和更新版本可作為貼齊存放區的貼齊。 儲存體總管的嵌入式管理單元會自動安裝其所有相依性，並在有新版本的嵌入式管理單元可供使用時進行更新。 安裝儲存體總管的貼齊是建議的安裝方法。

儲存體總管需要使用密碼管理員，您可能需要先手動連接，儲存體總管才能正常運作。 您可以執行下列命令，將儲存體總管連接到系統的密碼管理員：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>gz 檔案

您也可以將應用程式下載為 gz 檔案，但您必須手動安裝相依性。

只有下列版本的 Ubuntu 支援 gz 下載中提供的儲存體總管。 儲存體總管可能適用于其他 Linux 發行版本，但尚未正式支援。

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

儲存體總管需要在您的系統上安裝 .NET Core。 我們建議使用 .NET Core 2.1，但儲存體總管也會與2.2 搭配運作。

> [!NOTE]
> 儲存體總管版本1.7.0 和更早版本需要 .NET Core 2.0。 如果您已安裝較新版本的 .NET Core，將必須 [修補儲存體總管](#patching-storage-explorer-for-newer-versions-of-net-core)。 如果您正在執行儲存體總管1.8.0 或更新版本，則至少需要 .NET Core 2.1。

# <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. 下載儲存體總管 gz 檔案。
2. 安裝 [.Net Core 運行](/dotnet/core/install/linux)時間：
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. 下載儲存體總管 gz 檔案。
2. 安裝 [.Net Core 運行](/dotnet/core/install/linux)時間：
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. 下載儲存體總管 gz 檔案。
2. 安裝 [.Net Core 運行](/dotnet/core/install/linux)時間：
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

儲存體總管所需的許多程式庫都預先安裝了標準的 Ubuntu 標準安裝。 自訂環境可能遺失其中一些程式庫。 如果您在啟動儲存體總管時發生問題，建議您確定您的系統上已安裝下列套件：

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- ibsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- >xdg-公用程式

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>針對較新版本的 .NET Core 修補儲存體總管

針對儲存體總管1.7.0 或更早版本，您可能必須修補儲存體總管所使用的 .NET Core 版本：

1. [從 NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)下載 StreamJsonRpc 的版本1.5.43。 尋找頁面右側的 [下載套件] 連結。
2. 下載封裝之後，請將其副檔名從變更 `.nupkg` 為 `.zip` 。
3. 將套件解壓縮。
4. 開啟 `streamjsonrpc.1.5.43/lib/netstandard1.1/` 資料夾。
5. 複製 `StreamJsonRpc.dll` 到儲存體總管資料夾中的下列位置：
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Azure 入口網站中的「在 Explorer 中開啟」無法運作

如果 Azure 入口網站上的 [ **在 Explorer 中開啟** ] 按鈕無法運作，請確定您使用的是相容的瀏覽器。 下列瀏覽器已測試相容性：
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>後續步驟

如果這些解決方案都不適用，請 [在 GitHub 中開啟問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。 您也可以在左下角選取 [回報 **問題至 GitHub** ] 按鈕，以執行此動作。

![意見反應](./media/storage-explorer-troubleshooting/feedback-button.PNG)