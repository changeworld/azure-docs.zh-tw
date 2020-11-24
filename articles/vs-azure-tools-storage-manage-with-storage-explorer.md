---
title: 開始使用儲存體總管 | Microsoft Docs
description: 使用儲存體總管開始管理 Azure 儲存體資源。 下載並安裝 Azure 儲存體總管、連接至儲存體帳戶或服務等等。
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: be9b2d9a31d4affc9615f5d2f4b2585b7533a0f6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545902"
---
# <a name="get-started-with-storage-explorer"></a>開始使用儲存體總管

## <a name="overview"></a>概觀

Microsoft Azure 儲存體總管是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。 在本文中，您將瞭解幾種連接及管理 Azure 儲存體帳戶的方式。

![Microsoft Azure 儲存體總管][0]

## <a name="prerequisites"></a>必要條件

# <a name="windows"></a>[Windows](#tab/windows)

下列儲存體總管版本的 Windows 支援：

* Windows 10 (建議採用)
* Windows 8
* Windows 7

若為所有的 Windows 版本，儲存體總管至少需要 .NET Framework 4.7.2。

# <a name="macos"></a>[macOS](#tab/macos)

下列版本的 macOS 支援儲存體總管：

* macOS 10.12 的塞拉里昂和更新版本

# <a name="linux"></a>[Linux](#tab/linux)

最常見的 Linux 發行版本的 [貼齊存放區](https://snapcraft.io/storage-explorer) 中有提供儲存體總管。 我們建議此安裝的貼齊存放區。 當新版本發佈到貼齊存放區時，儲存體總管] 嵌入式管理單元會安裝其所有相依性和更新。

如需支援的發行版本，請參閱 [snapd 安裝頁面](https://snapcraft.io/docs/installing-snapd)。

儲存體總管需要使用密碼管理員。 您可能必須手動連接到密碼管理員。 您可以執行下列命令，將儲存體總管連接到系統的密碼管理員：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

儲存體總管也可以從 *gz* 下載取得。 您必須手動安裝相依性。 以下是 Linux 支援 *gz* 安裝的發行版本：

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

*Gz* 安裝可能適用于其他散發套件，但只正式支援這些版本。

如需如何在 Linux 上安裝儲存體總管的詳細說明，請參閱 Azure 儲存體總管疑難排解指南中的 [Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) 相依性。

---

## <a name="download-and-install"></a>下載和安裝

若要下載並安裝儲存體總管，請參閱 [Azure 儲存體總管](https://www.storageexplorer.com)。

## <a name="connect-to-a-storage-account-or-service"></a>連接到儲存體帳戶或服務

儲存體總管提供數種方式來連線至儲存體帳戶。 一般而言，您可以：

* [登入 Azure 以存取您的訂用帳戶及其資源](#sign-in-to-azure)
* [附加特定的儲存體或 CosmosDB 資源](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>登入 Azure

> [!NOTE]
> 若要在登入之後完整存取資源，儲存體總管需要管理 (Azure Resource Manager) 和資料層許可權。 這表示您需要 Azure Active Directory (Azure AD) 許可權，讓您能夠存取您的儲存體帳戶、帳戶中的容器，以及容器中的資料。 如果您只有在資料層有許可權，請考慮 [透過 Azure AD 新增資源](#add-a-resource-via-azure-ad)。 如需儲存體總管所需之特定許可權的詳細資訊，請參閱 [Azure 儲存體總管疑難排解指南](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues)。

1. 在儲存體總管中，選取 [ **View**  >  **Account Management** ] 或選取 [**管理帳戶**] 按鈕。

    ![管理帳戶][1]

1. **帳戶管理** 現在會顯示您已登入的所有 Azure 帳戶。 若要連線到另一個帳戶，請選取 [ **新增帳戶**]。

1. 在 **[連線到 Azure 儲存體]** 中，選取 **azure 環境** 中的 azure 雲端以登入國家/地區雲端或 azure Stack。 選擇您的環境之後，請選取 **[下一步]**。

    ![用來登入的選項][2]

    儲存體總管會開啟頁面供您登入。 如需詳細資訊，請參閱 [將 storage Explorer 連接至 Azure Stack 訂用帳戶或儲存體帳戶](/azure-stack/user/azure-stack-storage-connect-se)。

1. 當您使用 Azure 帳戶成功登入之後，與該帳戶相關聯的帳戶和 Azure 訂用帳戶會出現在 [ **帳戶管理**] 下。 選取 [ **所有** 訂用帳戶]，以在所有或未列出的 Azure 訂用帳戶之間切換您的選取專案。 選取您想要使用的 Azure 訂用帳戶，然後選取 [套用]。

    ![選取 Azure 訂用帳戶][3]

    **EXPLORER** 會顯示與所選 Azure 訂用帳戶相關聯的儲存體帳戶。

    ![已選取的 Azure 訂用帳戶][4]

### <a name="attach-a-specific-resource"></a>附加特定資源

有幾種方式可附加至儲存體總管中的資源：

* 透過[Azure AD 新增資源](#add-a-resource-via-azure-ad)。 如果您只有在資料層有許可權，請使用此選項來新增 blob 容器或 Azure Data Lake Storage Gen2 Blob 儲存體容器。
* [使用連接字串](#use-a-connection-string)。 如果您有儲存體帳戶的連接字串，請使用此選項。 儲存體總管支援金鑰和 [共用存取](./storage/common/storage-sas-overview.md) 簽章連接字串。
* [使用共用存取](#use-a-shared-access-signature-uri)簽章 URI。 如果您有 blob 容器、檔案共用、佇列或資料表的 [共用存取](./storage/common/storage-sas-overview.md) 簽章 URI，請使用它來附加至資源。 若要取得共用存取簽章 URI，您可以使用 [儲存體總管](#generate-a-sas-in-storage-explorer) 或 [Azure 入口網站](https://portal.azure.com)。
* [使用名稱和金鑰](#use-a-name-and-key)。 如果您知道儲存體帳戶的帳戶金鑰，您可以使用此選項快速連接。 在 [儲存體帳戶] 頁面中選取 [Azure 入口網站中的 [**設定**  >  **存取金鑰**]，以尋找您的金鑰。 [Azure portal](https://portal.azure.com)
* [附加至本機模擬器](#attach-to-a-local-emulator)。 如果您使用其中一個可用的 Azure 儲存體模擬器，請使用此選項輕鬆地連接到您的模擬器。
* [使用連接字串連接到 Azure Cosmos DB 帳戶](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)。 如果您有 CosmosDB 實例的連接字串，請使用此選項。
* [依 URI 連接到 Azure Data Lake 存放區](#connect-to-azure-data-lake-store-by-uri)。 如果您有 Azure Data Lake 存放區的 URI，請使用此選項。

#### <a name="add-a-resource-via-azure-ad"></a>透過 Azure AD 新增資源

1. 選取 [ **連線] 符號以** 開啟 **[連接到 Azure 儲存體]**。

    ![連接至 Azure 儲存體選項][9]

1. 如果您尚未這麼做，請使用 [ **新增 Azure 帳戶** ] 選項來登入可存取資源的 azure 帳戶。 在您登入之後，請回到 **[連線至 Azure 儲存體]**。

1. 選取 [透過 **Azure Active Directory (新增資源] Azure AD)**，然後選取 **[下一步]**。

1. 選取 Azure 帳戶和租使用者。 這些值必須能夠存取您想要附加的儲存體資源。 選取 [下一步]。

1. 選擇您要附加的資源類型。 輸入連接所需的資訊。 

   您在此頁面上輸入的資訊取決於您要新增的資源類型。 請務必選擇正確的資源類型。 輸入必要資訊之後，請選取 **[下一步]**。

1. 請檢查 **連接摘要** ，以確定所有資訊都正確無誤。 如果是，請選取 **[連接]**。 否則，請選取 [上一 **步** ] 返回先前的頁面，以修正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會移至代表連接的節點。 資源會出現在 [**本機 & 連接** 的  >  **儲存體帳戶**]  >  (連結的 **容器)**  >  **Blob 容器** 中。 如果儲存體總管無法新增您的連線，或在成功新增連線之後無法存取資料，請參閱 [Azure 儲存體總管疑難排解指南](./storage/common/storage-explorer-troubleshooting.md)。

#### <a name="use-a-connection-string"></a>使用連接字串

1. 選取 [ **連線] 符號以** 開啟 **[連接到 Azure 儲存體]**。

    ![連接至 Azure 儲存體選項][9]

1. 選取 [ **使用連接字串**]，然後選取 **[下一步]**。

1. 選擇您的連接顯示名稱，然後輸入您的連接字串。 然後，選取 [下一步]。

1. 請檢查 **連接摘要** ，以確定所有資訊都正確無誤。 如果是，請選取 **[連接]**。 否則，請選取 [上一 **步** ] 返回先前的頁面，以修正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會移至代表連接的節點。 資源會出現在 [**本機 & 連接** 的  >  **儲存體帳戶**] 底下。 如果儲存體總管無法新增您的連線，或在成功新增連線之後無法存取資料，請參閱 [Azure 儲存體總管疑難排解指南](./storage/common/storage-explorer-troubleshooting.md)。

#### <a name="use-a-shared-access-signature-uri"></a>使用共用存取簽章 URI

1. 選取 [ **連線] 符號以** 開啟 **[連接到 Azure 儲存體]**。

    ![連接至 Azure 儲存體選項][9]

1. 選取 [ **使用共用存取簽章 (SAS) URI**]，然後選取 **[下一步]**。

1. 選擇您的連線顯示名稱，然後輸入共用存取簽章 URI。 您要附加的資源類型的服務端點應自動填滿。 如果您是使用自訂端點，則可能不是。 選取 [下一步]。

1. 請檢查 **連接摘要** ，以確定所有資訊都正確無誤。 如果是，請選取 **[連接]**。 否則，請選取 [上一 **步** ] 返回先前的頁面，以修正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會移至代表連接的節點。 資源會出現在 [**本機 & 連接**  >  的 **儲存體帳戶**] (連結的  >  **容器)** 您所連結  >  *之容器類型的服務節點*。 如果儲存體總管無法新增您的連接，請參閱 [Azure 儲存體總管疑難排解指南](./storage/common/storage-explorer-troubleshooting.md)。 如果您無法在成功新增連接之後存取資料，請參閱疑難排解指南。

#### <a name="use-a-name-and-key"></a>使用名稱和金鑰

1. 選取 [ **連線] 符號以** 開啟 **[連接到 Azure 儲存體]**。

    ![連接至 Azure 儲存體選項][9]

1. 選取 [使用儲存體帳戶名稱和金鑰]，然後選取 [下一步]。

1. 選擇您的連接顯示名稱。

1. 輸入您的儲存體帳戶名稱和其中一個存取金鑰。

1. 選擇要使用的 **儲存體網域** ，然後選取 **[下一步]**。

1. 請檢查 **連接摘要** ，以確定所有資訊都正確無誤。 如果是，請選取 **[連接]**。 否則，請選取 [上一 **步** ] 返回先前的頁面，以修正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會移至代表連接的節點。 資源會出現在 [**本機 & 連接** 的  >  **儲存體帳戶**] 底下。 如果儲存體總管無法新增您的連線，或在成功新增連線之後無法存取資料，請參閱 [Azure 儲存體總管疑難排解指南](./storage/common/storage-explorer-troubleshooting.md)。

#### <a name="attach-to-a-local-emulator"></a>附加至本機模擬器

儲存體總管目前支援兩種官方儲存體模擬器：

* [Azure 儲存體模擬器](storage/common/storage-use-emulator.md) 只 (Windows) 
* [Azurite](https://github.com/azure/azurite) (Windows、MacOS 或 Linux) 

如果您的模擬器正在接聽預設通訊埠，您可以使用 [ **模擬器-預設埠** ] 節點來存取您的模擬器。 尋找 [**本機 &** 連結的儲存體帳戶] 下的 [**模擬器-預設埠**]  >  ****。

如果您想要使用不同的名稱來連接，或您的模擬器未在預設埠上執行，請遵循下列步驟：

1. 啟動您的模擬器。 輸入命令 `AzureStorageEmulator.exe status` ，以顯示每個服務類型的埠。

   > [!IMPORTANT]
   > 儲存體總管不會自動啟動您的模擬器。 您必須手動啟動它。

1. 選取 [ **連線] 符號以** 開啟 **[連接到 Azure 儲存體]**。

    ![連接至 Azure 儲存體選項][9]

1. 選取 [ **附加至本機模擬器**]，然後選取 **[下一步]**。

1. 為您的連線選擇顯示名稱，並輸入模擬器正在接聽的每個服務類型的埠。 **附加至本機模擬器** 會建議大部分模擬器的預設埠值。 檔案 **埠** 是空白的，因為官方模擬器目前都不支援檔案服務。 如果您所使用的模擬器支援檔案，您可以輸入要使用的埠。 然後，選取 [下一步]。

1. 請檢查 **連接摘要** ，並確定所有資訊都正確無誤。 如果是，請選取 **[連接]**。 否則，請選取 [上一 **步** ] 返回先前的頁面，以修正任何不正確的資訊。

成功新增連接之後，資源樹狀結構會移至代表連接的節點。 節點應該會出現在 [**本機 & 連接** 的  >  **儲存體帳戶**] 之下。 如果儲存體總管無法新增您的連線，或在成功新增連線之後無法存取資料，請參閱 [Azure 儲存體總管疑難排解指南](./storage/common/storage-explorer-troubleshooting.md)。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用連接字串連線到 Azure Cosmos DB 帳戶

您可以使用連接字串連接到 Azure Cosmos DB，而不是透過 Azure 訂用帳戶來管理 Azure Cosmos DB 帳戶。 若要連接，請遵循下列步驟：

1. 在 [ **EXPLORER**] 下，展開 [ **本機 & 附加**]，以滑鼠右鍵按一下 [ **Cosmos DB 帳戶**]，然後選取 **[連接到 Azure Cosmos DB**。

    ![使用連接字串連線到 Azure Cosmos DB][21]

1. 選取 Azure Cosmos DB API、輸入您的 **連接字串** 資料，然後選取 **[確定** ] 以連接 Azure Cosmos DB 帳戶。 如需有關如何取出連接字串的詳細資訊，請參閱 [管理 Azure Cosmos 帳戶](./cosmos-db/how-to-manage-database-account.md)。

    ![連接字串][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>以 URI 連線至 Azure Data Lake Store

您可以存取不在訂用帳戶中的資源。 您需要有權存取該資源的人員，才能提供資源 URI 給您。 登入之後，請使用 URI 連接到 Data Lake Store。 若要連接，請遵循下列步驟：

1. 在 [ **EXPLORER**] 下，展開 [ **本機附加 &**]。

1. 以滑鼠右鍵按一下 **Data Lake Storage Gen1**，然後選取 **[連接到 Data Lake Storage Gen1]**。

    ![連接到 Data Lake Store 內容功能表](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. 輸入 URI，然後選取 **[確定]**。 您的 Data Lake Store 會出現在 [ **Data Lake Storage**] 下。

    ![連接到 Data Lake Store 結果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

這個範例會使用 Data Lake Storage Gen1。 Azure Data Lake Storage Gen2 現已開放使用。 如需詳細資訊，請參閱 [什麼是 Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md)。

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>在儲存體總管中產生共用存取簽章<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>帳戶層級共用存取簽章

1. 以滑鼠右鍵按一下您想要共用的儲存體帳戶，然後選取 [ **取得共用存取** 簽章]。

    ![取得共用存取簽章內容功能表選項][14]

1. 在 [ **共用存取** 簽章] 中，指定您要用於此帳戶的時間範圍和許可權，然後選取 [ **建立**]。

    ![取得共用存取簽章][15]

1. 將 **連接字串** 或原始 **查詢字串** 複製到剪貼簿。

### <a name="service-level-shared-access-signature"></a>服務層級共用存取簽章

您可以取得服務層級的共用存取簽章。 如需詳細資訊，請參閱 [取得 blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)。

## <a name="search-for-storage-accounts"></a>搜尋儲存體帳戶

若要尋找儲存體資源，您可以在 [ **瀏覽器** ] 窗格中搜尋。

當您在 [搜尋] 方塊中輸入文字時，儲存體總管會顯示符合您目前為止輸入之搜尋值的所有資源。 此範例示範如何搜尋 **端點**：

![儲存體帳戶搜尋][23]

> [!NOTE]
> 若要加速搜尋，請使用 **帳戶管理** 來取消選取任何未包含您要搜尋之專案的訂用帳戶。 您也可以用滑鼠右鍵按一下節點，然後選取 [ **從這裡搜尋** ] 以開始從特定節點搜尋。
>
>

## <a name="next-steps"></a>後續步驟

* [使用儲存體總管管理 Azure Blob 儲存體資源](vs-azure-tools-storage-explorer-blobs.md)
* [使用 Azure 儲存體總管處理資料](./cosmos-db/storage-explorer.md)
* [使用儲存體總管來管理 Azure Data Lake Store 資源](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png