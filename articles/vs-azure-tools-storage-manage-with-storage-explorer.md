---
title: 開始使用儲存體總管 | Microsoft Docs
description: 使用儲存體總管來管理 Azure 儲存體資源
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279789"
---
# <a name="get-started-with-storage-explorer"></a>開始使用儲存體總管

## <a name="overview"></a>總覽

Microsoft Azure 存儲資源管理器是一款獨立應用，它便於在 Windows、macOS 和 Linux 上處理 Azure 存儲資料。 在本文中，您將瞭解連接到和管理 Azure 存儲帳戶的幾種方法。

![Microsoft Azure 儲存體總管][0]

## <a name="prerequisites"></a>Prerequisites

# <a name="windows"></a>[Windows](#tab/windows)

以下版本的 Windows 支援存儲資源管理器：

* Windows 10 (建議採用)
* Windows 8
* Windows 7

對於所有版本的 Windows，存儲資源管理器需要 .NET 框架 4.6.2 或更高版本。

# <a name="macos"></a>[macOS](#tab/macos)

以下版本的 macOS 支援存儲資源管理器：

* macOS 10.12 塞拉和更高版本

# <a name="linux"></a>[Linux](#tab/linux)

存儲資源管理器在[快照存儲](https://snapcraft.io/storage-explorer)中可用於 Linux 的最常見的發行版本。 我們建議為此安裝使用快照存儲。 當新版本發佈到快照存儲時，存儲資源管理器快照將安裝其所有依賴項和更新。

有關支援的分發，請參閱[已捕捉的安裝頁](https://snapcraft.io/docs/installing-snapd)。

存儲資源管理器需要使用密碼管理器。 您可能需要手動連接到密碼管理器。 您可以通過運行以下命令將存儲資源管理器連接到系統的密碼管理器：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

存儲資源管理器也可作為 *.tar.gz*下載提供。 您必須手動安裝依賴項。 Linux 支援 *.tar.gz*安裝的以下發行版：

* 烏本圖 18.04 x 64
* 烏本圖 16.04 x 64
* Ubuntu 14.04 x64

*.tar.gz*安裝可能用於其他發行版本，但僅正式支援這些列出的發行版本。

有關在 Linux 上安裝存儲資源管理器的更多説明，請參閱 Azure 存儲資源管理器故障排除指南中的[Linux 依賴項](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)。

---

## <a name="download-and-install"></a>下載並安裝

要下載並安裝存儲資源管理器，請參閱[Azure 存儲資源管理器](https://www.storageexplorer.com)。

## <a name="connect-to-a-storage-account-or-service"></a>連接到儲存體帳戶或服務

儲存體總管提供數種方式來連線至儲存體帳戶。 通常，您可以：

* [登錄到 Azure 以訪問訂閱及其資源](#sign-in-to-azure)
* [附加特定的存儲或 CosmosDB 資源](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>登入 Azure

> [!NOTE]
> 要在登錄後完全訪問資源，存儲資源管理器需要管理（Azure 資源管理器）和資料層許可權。 這意味著您需要 Azure 活動目錄 （Azure AD） 許可權，這些許可權允許您訪問存儲帳戶、帳戶中的容器和容器中的資料。 如果僅在資料層具有許可權，請考慮[通過 Azure AD 添加資源](#add-a-resource-via-azure-ad)。 有關存儲資源管理器所需的特定許可權的詳細資訊，請參閱[Azure 存儲資源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)。

1. 在"存儲資源管理器"中，選擇 **"查看** > **帳戶管理**"或"**管理帳戶**"按鈕。

    ![管理帳戶][1]

1. **帳戶管理**現在顯示已登錄的所有 Azure 帳戶。 要連接到其他帳戶，請選擇"**添加帳戶**"。

1. 在 **"連接到 Azure 存儲**"中，從**Azure 環境**中選擇 Azure 雲以登錄到國家雲或 Azure 堆疊。 選擇環境後，選擇 **"下一步**"。

    ![登錄選項][2]

    存儲資源管理器將打開一個頁面，供您登錄。 有關詳細資訊，請參閱[將存儲資源管理器連接到 Azure 堆疊訂閱或存儲帳戶](/azure-stack/user/azure-stack-storage-connect-se)。

1. 使用 Azure 帳戶成功登錄後，與該帳戶關聯的帳戶和 Azure 訂閱將顯示在 **"帳戶管理**"下。 選擇 **"所有訂閱**"以在列出的所有訂閱或未列出的 Azure 訂閱之間切換所選內容。 選取您想要使用的 Azure 訂用帳戶，然後選取 [套用]****。

    ![選取 Azure 訂用帳戶][3]

    **資源管理器**顯示與所選 Azure 訂閱關聯的存儲帳戶。

    ![已選取的 Azure 訂用帳戶][4]

### <a name="attach-a-specific-resource"></a>附加特定資源

有幾種方法可以附加到存儲資源管理器中的資源：

* [通過 Azure AD 添加資源](#add-a-resource-via-azure-ad)。 如果僅在資料層具有許可權，請使用此選項添加 Blob 容器或 Azure 資料湖存儲 Gen2 Blob 存儲容器。
* [使用連接字串](#use-a-connection-string)。 如果與存儲帳戶具有連接字串，請使用此選項。 存儲資源管理器同時支援金鑰和[共用訪問簽名](storage/common/storage-dotnet-shared-access-signature-part-1.md)連接字串。
* [使用共用訪問簽名 URI](#use-a-shared-access-signature-uri)。 如果對 Blob 容器、檔共用、佇列或表具有[共用訪問簽名 URI，](storage/common/storage-dotnet-shared-access-signature-part-1.md)請使用它附加到資源。 要獲取共用訪問簽名 URI，可以使用[存儲資源管理器](#generate-a-sas-in-storage-explorer)或[Azure 門戶](https://portal.azure.com)。
* [使用名稱和鍵](#use-a-name-and-key)。 如果您知道存儲帳戶的任一帳戶金鑰，則可以使用此選項快速連接。 通過在[Azure 門戶](https://portal.azure.com)中選擇 **"設置** > **訪問金鑰**"，在存儲帳戶頁中查找金鑰。
* [附加到本地模擬器](#attach-to-a-local-emulator)。 如果使用可用的 Azure 存儲模擬器之一，請使用此選項輕鬆連接到模擬器。
* [使用連接字串連接到 Azure Cosmos DB 帳戶](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)。 如果與 CosmosDB 實例具有連接字串，請使用此選項。
* [通過 URI 連接到 Azure 資料湖存儲](#connect-to-azure-data-lake-store-by-uri)。 如果您有到 Azure 資料湖存儲的 URI，請使用此選項。

#### <a name="add-a-resource-via-azure-ad"></a>通過 Azure AD 添加資源

1. 選擇**連接**符號以打開**連接到 Azure 存儲**。

    ![連接至 Azure 儲存體選項][9]

1. 如果尚未這樣做，請使用 **"添加 Azure 帳戶"** 選項登錄到有權訪問資源的 Azure 帳戶。 登錄後，返回到**連接到 Azure 存儲**。

1. 選擇**通過 Azure 活動目錄 （Azure AD） 添加資源**，然後選擇 **"下一步**"。

1. 選擇 Azure 帳戶和租戶。 這些值必須有權訪問要附加到的存儲資源。 選取 [下一步]****。

1. 選擇要附加的資源類型。 輸入連接所需的資訊。 

   您在此頁上輸入的資訊取決於要添加的資源類型。 請確保選擇正確的資源類型。 輸入所需資訊後，選擇 **"下一步**"。

1. 查看**連接摘要**以確保所有資訊都正確。 如果是，請選擇"**連接**"。 否則，選擇 **"返回"** 以返回到以前的頁面以修復任何不正確的資訊。

成功添加連接後，資源樹將轉到表示連接的節點。 資源顯示在**本地&附加** > **存儲帳戶** > **（附加容器）Blob** > **容器**下。 如果存儲資源管理器無法添加連接，或者在成功添加連接後無法訪問資料，請參閱[Azure 存儲資源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-connection-string"></a>使用連接字串

1. 選擇**連接**符號以打開**連接到 Azure 存儲**。

    ![連接至 Azure 儲存體選項][9]

1. 選擇 **"使用連接字串**"，然後選擇 **"下一步**"。

1. 為連接選擇顯示名稱並輸入連接字串。 然後，選擇 **"下一步**"。

1. 查看**連接摘要**以確保所有資訊都正確。 如果是，請選擇"**連接**"。 否則，選擇 **"返回"** 以返回到以前的頁面以修復任何不正確的資訊。

成功添加連接後，資源樹將轉到表示連接的節點。 資源將顯示在**本地&附加** > **存儲帳戶**下。 如果存儲資源管理器無法添加連接，或者在成功添加連接後無法訪問資料，請參閱[Azure 存儲資源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-shared-access-signature-uri"></a>使用共用訪問簽名 URI

1. 選擇**連接**符號以打開**連接到 Azure 存儲**。

    ![連接至 Azure 儲存體選項][9]

1. 選擇 **"使用共用訪問簽名 （SAS） URI"，** 然後選擇 **"下一步**"。

1. 選擇連接的顯示名稱，然後輸入共用訪問簽名 URI。 要附加的資源類型的服務終結點應自動填滿。 如果使用自訂終結點，則可能不是。 選取 [下一步]****。

1. 查看**連接摘要**以確保所有資訊都正確。 如果是，請選擇"**連接**"。 否則，選擇 **"返回"** 以返回到以前的頁面以修復任何不正確的資訊。

成功添加連接後，資源樹將轉到表示連接的節點。 資源顯示在**本地&附加** > **存儲帳戶** > **（附加容器）** > 下，即*您附加的容器類型的服務節點*。 如果存儲資源管理器無法添加連接，請參閱[Azure 存儲資源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。 如果成功添加連接後無法訪問資料，請參閱故障排除指南。

#### <a name="use-a-name-and-key"></a>使用名稱和鍵

1. 選擇**連接**符號以打開**連接到 Azure 存儲**。

    ![連接至 Azure 儲存體選項][9]

1. 選擇 **"使用存儲帳戶名稱和金鑰**"，然後選擇 **"下一步**"。

1. 為連接選擇顯示名稱。

1. 輸入存儲帳戶名稱及其任一訪問金鑰。

1. 選擇要使用的**存儲域**，然後選擇 **"下一步**"。

1. 查看**連接摘要**以確保所有資訊都正確。 如果是，請選擇"**連接**"。 否則，選擇 **"返回"** 以返回到以前的頁面以修復任何不正確的資訊。

成功添加連接後，資源樹將轉到表示連接的節點。 資源將顯示在**本地&附加** > **存儲帳戶**下。 如果存儲資源管理器無法添加連接，或者在成功添加連接後無法訪問資料，請參閱[Azure 存儲資源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="attach-to-a-local-emulator"></a>連接到本地模擬器

存儲資源管理器目前支援兩個官方存儲模擬器：

* [Azure 存儲模擬器](storage/common/storage-use-emulator.md)（僅限 Windows）
* [Azurite（Windows、macOS](https://github.com/azure/azurite)或 Linux）

如果模擬器正在偵聽預設埠，則可以使用**模擬器 - 預設埠**節點訪問模擬器。 查找**模擬器 -** **本地&附加** > **存儲帳戶**下的預設埠。

如果要為連接使用不同的名稱，或者您的模擬器未在預設埠上運行，請按照以下步驟操作：

1. 啟動模擬器。 輸入顯示每種`AzureStorageEmulator.exe status`服務類型的埠的命令。

   > [!IMPORTANT]
   > 存儲資源管理器不會自動啟動模擬器。 您必須手動啟動它。

1. 選擇**連接**符號以打開**連接到 Azure 存儲**。

    ![連接至 Azure 儲存體選項][9]

1. 選擇 **"附加到本地模擬器**"，然後選擇 **"下一步**"。

1. 為連接選擇顯示名稱，然後輸入模擬器正在偵聽的每個服務類型的埠。 **附加到本地模擬器**會建議大多數模擬器的預設埠值。 **檔埠**為空，因為當前兩個官方模擬程式都不支援檔服務。 如果您使用的模擬器確實支援檔，則可以輸入要使用的埠。 然後，選擇 **"下一步**"。

1. 查看**連接摘要**並確保所有資訊都正確無誤。 如果是，請選擇"**連接**"。 否則，選擇 **"返回"** 以返回到以前的頁面以修復任何不正確的資訊。

成功添加連接後，資源樹將轉到表示連接的節點。 該節點應顯示在**本地&附加** > **存儲帳戶**下。 如果存儲資源管理器無法添加連接，或者在成功添加連接後無法訪問資料，請參閱[Azure 存儲資源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用連接字串連線到 Azure Cosmos DB 帳戶

可以使用連接字串連接到 Azure Cosmos DB，而不是通過 Azure 訂閱管理 Azure Cosmos DB 帳戶。 若要連接，請遵循下列步驟：

1. 在 **"資源管理器"** 下，展開**本地&附加**，按右鍵**Cosmos DB 帳戶**，然後選擇"**連接到 Azure 宇宙資料庫**"。

    ![使用連接字串連線到 Azure Cosmos DB][21]

1. 選擇 Azure 宇宙資料庫 API，輸入**連接字串**資料，然後選擇 **"確定"** 以連接 Azure Cosmos 資料庫帳戶。 有關如何檢索連接字串的資訊，請參閱管理 Azure [Cosmos 帳戶](https://docs.microsoft.com/azure/cosmos-db/manage-account)。

    ![連接字串][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>以 URI 連線至 Azure Data Lake Store

您可以訪問訂閱中不包含的資源。 您需要有權訪問該資源的人員為您提供資源 URI。 登錄後，請使用 URI 連接到資料湖存儲。 若要連接，請遵循下列步驟：

1. 在**探索者**下，展開**附加的本地&。**

1. 按右鍵 **"資料存儲第 1 代**"，然後選擇 **"連接到資料存儲第 1 代**"。

    ![連接到資料湖存儲內容功能表](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. 輸入 URI，然後選擇 **"確定**"。 您的資料湖存儲顯示在 **"資料湖存儲"** 下。

    ![連接到資料湖存儲結果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

此示例使用資料湖存儲第 1 代。 Azure 資料存儲第 2 代現已可用。 有關詳細資訊，請參閱什麼是[Azure 資料存儲第 1 代](./data-lake-store/data-lake-store-overview.md)。

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>在存儲資源管理器中生成共用訪問簽名<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>帳戶級共用訪問簽名

1. 按右鍵要共用的存儲帳戶，然後選擇 **"獲取共用訪問簽名**"。

    ![獲取共用訪問簽名內容功能表選項][14]

1. 在 **"共用訪問簽名**"中，指定帳戶所需的時間範圍和許可權，然後選擇 **"創建**"。

    ![獲取共用訪問簽名][15]

1. 將**連接字串**或原始**查詢字串**複製到剪貼簿。

### <a name="service-level-shared-access-signature"></a>服務等級共用訪問簽名

您可以在服務等級獲取共用訪問簽名。 有關詳細資訊，請參閱獲取[Blob 容器的 SAS。](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>搜尋儲存體帳戶

要查找存儲資源，可以在 **"資源管理器"** 窗格中搜索。

當您在搜索框中輸入文本時，存儲資源管理器將顯示與您輸入到該點搜索值匹配的所有資源。 此示例顯示對**終結點**的搜索 ：

![儲存體帳戶搜尋][23]

> [!NOTE]
> 要加快搜索速度，請使用 **"帳戶管理"** 取消選擇不包含要搜索的專案的任何訂閱。 您還可以按右鍵某個節點，然後選擇 **"從這裡搜索**"以開始從特定節點進行搜索。
>
>

## <a name="next-steps"></a>後續步驟

* [使用存儲資源管理器管理 Azure Blob 存儲資源](vs-azure-tools-storage-explorer-blobs.md)
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
