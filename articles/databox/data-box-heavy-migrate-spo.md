---
title: 使用 Azure Data Box Heavy 將檔案共用內容移至 SharePoint Online
description: 透過本教學課程來了解如何使用 Azure Data Box Heavy 將檔案共用內容遷移至 SharePoint Online
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: how-to
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: a87181e7d1c776648781a2672dfd73ac2eaab1b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396853"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>使用 Azure Data Box Heavy 將檔案共用內容遷移至 SharePoint Online

使用 Azure Data Box Heavy 和 SharePoint 移轉工具 (SPMT) 可輕鬆地將檔案共用內容遷移至 SharePoint Online 和 OneDrive。 藉由使用 Data Box Heavy，您便不用再依賴廣域網路 (WAN) 連結來傳輸資料。

Microsoft Azure 資料箱服務可讓您從 Microsoft Azure 入口網站訂購裝置。 然後，您就可以將數 TB 的資料從伺服器複製到裝置。 將裝置寄回 Microsoft 後，資料便會複製到 Azure 中。 根據您想要傳輸的資料大小，可供選擇的項目如下：

- [資料箱磁碟](https://docs.microsoft.com/azure/databox/data-box-disk-overview)，每筆訂單會有 35 TB 的可用容量，適用於中小型資料集。
- [資料箱](https://docs.microsoft.com/azure/databox/data-box-overview)，每個裝置會有 80 TB 的可用容量，適用於中大型資料集。
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview)，每個裝置會有 770 TB 的可用容量，適用於大型資料集。

本文會專門探討如何使用 Data Box Heavy 將檔案共用內容遷移至 SharePoint Online。

## <a name="requirements-and-costs"></a>需求和成本

### <a name="for-data-box-heavy"></a>針對 Data Box Heavy

- Data Box Heavy 僅適用於 Enterprise 合約 (EA)、雲端解決方案提供者 (CSP) 或 Azure 贊助的供應項目。 如果您的訂用帳戶不屬於上述任何一種類型，請連絡 Microsoft 支援服務以升級您的訂用帳戶，或參閱 [Azure 訂用帳戶定價](https://azure.microsoft.com/pricing/)。
- Data Box Heavy 有使用費。 請務必檢閱 [Data Box Heavy 定價](https://azure.microsoft.com/pricing/details/databox/heavy/)。


### <a name="for-sharepoint-online"></a>針對 SharePoint Online

- 請檢閱 [SharePoint 移轉工具 (SPMT) 的最低需求](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)。

## <a name="workflow-overview"></a>工作流程概觀

此工作流程會要求您在 Azure Data Box Heavy 以及 SharePoint Online 上執行步驟。
下列步驟與 Azure Data Box Heavy 有關。

1. 訂購 Azure Data Box Heavy。
2. 接收並設定裝置。
3. 將資料從內部部署檔案共用複製到裝置上供 Azure 檔案儲存體使用的資料夾。
4. 複製完成後，請根據指示將裝置寄回。
5. 等待資料完整上傳至 Azure。

下列步驟與 SharePoint Online 有關。

6. 在 Azure 入口網站中建立 VM，並在其上掛接 Azure 檔案共用。
7. 在 Azure VM 上安裝 SPMT 工具。
8. 使用 Azure 檔案共用作為「來源」  來執行 SPMT 工具。
9. 完成工具的最後一個步驟。
10. 驗證並確認資料。

## <a name="use-data-box-heavy-to-copy-data"></a>使用 Data Box Heavy 來複製資料

請採取下列步驟將資料複製到 Data Box Heavy。

1. [訂購 Data Box Heavy](data-box-heavy-deploy-ordered.md)。
2. 收到 Data Box Heavy 後，請[設定 Data Box Heavy](data-box-heavy-deploy-set-up.md)。 用纜線將裝置上的兩個節點接好，並進行設定。
3. [將資料複製到 Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md)。 複製時請務必：

    - 只使用 Data Box Heavy 中的 *StorageAccountName_AzFile* 資料夾來複製資料。 這是因為您想要讓資料最終進入到 Azure 檔案共用，而不是進入區塊 Blob 或分頁 Blob。
    - 將檔案複製到 *StorageAccountName_AzFile* 資料夾內的資料夾。 *StorageAccountName_AzFile* 資料夾內的子資料夾會建立檔案共用。 直接複製到 *StorageAccountName_AzFile* 資料夾的檔案將會失敗，並以區塊 Blob 的形式上傳。 這是您會在下一個步驟中於 VM 上掛接的檔案共用。
    - 將資料複製到 Data Box Heavy 的兩個節點中。
3. 在裝置上執行[準備寄送](data-box-heavy-deploy-picked-up.md#prepare-to-ship)。 「準備寄送」若能成功，便可確保檔案也能成功上傳至 Azure。
4. [返還裝置](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back)。
5. [確認資料有上傳至 Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure)。

## <a name="use-spmt-to-migrate-data"></a>使用 SPMT 來遷移資料

從 Azure 資料小組得知資料已確實複製完成後，請繼續將資料遷移至 SharePoint Online。

如需最佳效能和連線能力，建議您建立 Azure 虛擬機器 (VM)。

1. 登入 Azure 入口網站，然後[建立虛擬機器](../virtual-machines/windows/quick-create-portal.md)。
2. [將 Azure 檔案共用掛接到 VM 上](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer)。
3. [下載 SharePoint 移轉工具](https://spmtreleasescus.blob.core.windows.net/install/default.htm)並將其安裝在 Azure VM 上。
4. 啟動 SharePoint 移轉工具。 按一下 [登 **入** ]，然後輸入您的工作或學校帳戶的使用者名稱和密碼。
5. 出現**您的資料在哪？** 提示時，選取 [檔案共用]  。 輸入資料所在 Azure 檔案共用的路徑。
6. 和平常一樣遵循其餘的提示來進行，包括您的目標位置。 如需詳細資訊，請移至[如何使用 SharePoint 移轉工具](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)。

> [!IMPORTANT]
> - 無論資料是否已位於 Azure 中，將資料擷取到 SharePoint Online 的速度會受到許多因素影響。 了解這些因素將有助於您規劃移轉效率並將其最大化。  如需詳細資訊，請移至 [SharePoint Online 和 OneDrive 移轉速度](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed)。
> - 將資料遷移至 SharePoint Online 時，會有遺失現有檔案權限的風險。 您也可能會遺失某些中繼資料，例如「建立者」  和「日期修改者」  。

## <a name="next-steps"></a>後續步驟

[訂購 Data Box Heavy](./data-box-heavy-deploy-ordered.md)