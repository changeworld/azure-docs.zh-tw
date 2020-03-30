---
title: 遷移到 Azure 檔共用
description: 瞭解遷移到 Azure 檔共用並查找遷移指南。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247311"
---
# <a name="migrate-to-azure-file-shares"></a>遷移到 Azure 檔共用

本文介紹遷移到 Azure 檔共用的基本方面。

除了遷移基礎知識外，本文還包含現有個人化遷移指南的清單。 這些遷移指南可説明您將檔移動到 Azure 檔共用中，並按資料當前所在的位置以及計畫遷移到哪些部署模型（僅限雲或混合部署）進行組織。

## <a name="migration-basics"></a>遷移基礎知識

Azure 中有多種不同類型的雲存儲可用。 將檔遷移到 Azure 的一個基本方面是確定哪種 Azure 存儲選項適合您的資料。

Azure 檔共用非常適合通用檔資料。 確實，您使用本地 SMB 或 NFS 共用的任何內容。 使用[Azure 檔同步](storage-sync-files-planning.md)，可以選擇在本地多個 Windows 伺服器上緩存多個 Azure 檔共用的內容。

如果應用程式當前在本機伺服器上運行，則根據應用程式的不同，在 Azure 檔共用中存儲檔可能適合您。 可以提升應用程式以在 Azure 中運行，並使用 Azure 檔共用作為共用存儲。 您還可以考慮此方案[的 Azure 磁片](../../virtual-machines/windows/managed-disks-overview.md)。 對於不依賴于 SMB 或電腦本地訪問其資料或共用訪問的雲誕生應用程式，物件存儲（如[Azure Blob）](../blobs/storage-blobs-overview.md)通常是最佳選擇。

任何遷移的鍵是在將檔從當前存儲位置遷移到 Azure 時捕獲所有適用的檔逼真度。 選擇正確的 Azure 存儲的説明也是 Azure 存儲選項支援多少逼真度以及方案所需的逼真度方面。 通用檔資料傳統上取決於檔中繼資料。 應用程式資料可能不是。 檔有兩個基本元件：

- **資料流程**：檔的資料流程存儲檔內容。
- **檔中繼資料**： 檔中繼資料具有多個子元件：
   * 檔案屬性：例如，唯讀。
   * 檔許可權：稱為*NTFS 許可權*或*檔和資料夾 ACL。*
   * 時間戳記：最值得注意的是*創建和**上次修改*的時間戳記。
   * 替代資料流程：用於存儲大量非標準屬性的空間。

因此，在遷移中，檔逼真度可以定義為在源上存儲所有適用檔資訊的能力、使用遷移工具傳輸它們的能力以及將它們存儲在遷移的目標存儲上的能力。

為確保遷移盡可能順利進行，請確定[適合您需求的最佳複製工具](#migration-toolbox)，並將存儲目標與源相匹配。

考慮到前面的資訊，Azure 中通用檔的目標存儲是什麼[：Azure 檔共用](storage-files-introduction.md)。 與 Azure Blob 中的物件存儲相比，檔中繼資料可以本機存儲在 Azure 檔共用中的檔中。

Azure 檔共用還保留檔和資料夾層次結構。 此外：
* NTFS 許可權可以存儲在檔和資料夾中，因為它們是本地的。
* AD 使用者（或 Azure AD DS 使用者）可以本機訪問 Azure 檔共用。 
    他們使用其當前標識，並根據共用許可權以及檔和資料夾 ACL 獲取存取權限。 當使用者連接到本地檔共用時，行為與不同。
*  替代資料流程是檔逼真度的主要方面，當前無法存儲在 Azure 檔共用中的檔中。
   涉及 Azure 檔同步時，它將保存在本地。

* [瞭解有關 Azure 檔共用的 AD 身份驗證詳細資訊](storage-files-identity-auth-active-directory-enable.md)
* [瞭解有關 Azure 檔共用的 Azure 活動目錄域服務 （AAD DS） 身份驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>移轉指南

下表列出了詳細的遷移指南。

通過：
1. 查找檔當前存儲的源系統的行。
2. 確定是針對混合部署，其中是否使用 Azure 檔同步在本機快取一個或多個 Azure 檔共用的內容，或者是否希望直接在雲中使用 Azure 檔共用。 選擇反映您的決策的目標列。
3. 在源和目標交集中，表儲存格列出了可用的遷移方案。 選擇其中一個可直接連結到詳細的遷移指南。

沒有連結的方案尚未發佈遷移指南。 偶爾檢查此表以獲取更新。 新指南將在可用時發佈。

| 來源 | 目標： </br>混合式部署 | 目標： </br>僅限雲的部署 |
|:---|:--|:--|
| | 工具組合：| 工具組合： |
| Windows Server 2012 R2 和更新版本 | <ul><li>[Azure 檔案同步](storage-sync-files-deployment-guide.md)</li><li>[Azure 檔同步 + 資料框](storage-sync-offline-data-transfer.md)</li><li>存儲遷移服務 + Azure 檔同步</li></ul> | <ul><li>Azure 檔案同步</li><li>Azure 檔同步 + 資料框</li><li>存儲遷移服務 + Azure 檔同步</li><li>RoboCopy</li></ul> |
| Windows 伺服器 2012 及舊版 | <ul><li>Azure 檔同步 + 資料框</li><li>存儲遷移服務 + Azure 檔同步</li></ul> | <ul><li>存儲遷移服務 + Azure 檔同步</li><li>RoboCopy</li></ul> |
| 網路附加存儲 （NAS） | <ul><li>[Azure 檔同步 + RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux / 桑巴 | <ul><li>[RoboCopy = Azure 檔同步](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| 斯托簡單 8100 / 8600 | <ul><li>[Azure 檔同步 = 8020 虛擬裝置](storage-files-migration-storsimple-8000.md)</li></ul> | |
| 斯托簡單 1200 | <ul><li>[Azure 檔案同步](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>遷移工具箱

### <a name="file-copy-tools"></a>檔案複製工具

有幾個微軟和非微軟檔案複製工具可用。 為了為您的遷移方案選擇正確的檔案複製工具，必須考慮三個基本問題：

* 複製工具是否支援給定檔副本的源和目標位置？ 
    * 它是否支援您的網路路徑和/或可用協定（例如 REST/SMB/NFS）以及源和目標存儲位置？
* 複製工具是否保留源/目標位置支援的必要檔逼真度？ 在某些情況下，目標存儲不支援與源相同的逼真度。 您已經決定目標存儲足以滿足您的需要，因此複製工具只需要匹配目的檔案逼真度功能。
* 複製工具是否具有使其適合我的遷移策略的功能？ 
    * 例如，考慮它是否有允許您最小化停機時間的選項。 一個很好的問題是：我可以在同一個上運行此副本多次，由使用者主動訪問的位置嗎？ 如果是這樣，您可以顯著減少停機時間。 將此與僅可在源停止更改時啟動副本的情況進行比較，以確保完整副本。

下表對 Microsoft 工具及其當前是否適合 Azure 檔共用進行分類：

| 建議 | 工具 | 支援 Azure 檔共用 | 保留檔逼真度 |
| :-: | :-- | :---- | :---- |
|![是，建議。](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | 支援。 Azure 檔共用可以裝載為網路磁碟機。 | 全保真* |
|![是，建議。](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 檔案同步 | 本機集成到 Azure 檔共用中。 | 全保真* |
|![是，建議。](media/storage-files-migration-overview/circle-green-checkmark.png)| 存放裝置移轉服務 (SMS) | 間接支援。 Azure 檔共用可以裝載為 SMS 目標伺服器上的網路磁碟機。 | 全保真* |
|![不完全推薦。](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure 資料箱 | 支援。 | 不復制中繼資料。 [可與 Azure 檔同步結合使用](storage-sync-offline-data-transfer.md)。 |
|![不建議使用。](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | 支援。 | 不復制中繼資料。 |
|![不建議使用。](media/storage-files-migration-overview/circle-red-x.png)| Azure 儲存體總管 | 支援。 | 不復制中繼資料。 |
|![不建議使用。](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | 支援。 | 不復制中繼資料。 |
|||||

*\*完全逼真度：滿足或超過 Azure 檔共用功能。*

### <a name="migration-helper-tools"></a>遷移説明工具

本節列出了説明規劃和執行遷移的工具。

* **RoboCopy，來自微軟公司**

    檔遷移的最適用的複製工具之一，是微軟 Windows 的一部分。 由於此工具中的許多選項，[主要 RoboCopy 文檔](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)是一個有用的來源。

* **樹的大小，來自JAM軟體有限公司**

    Azure 檔同步主要隨專案數（檔和資料夾）進行擴展，而與 TiB 總量一樣， 則較少。 該工具可用於確定 Windows Server 卷上的檔和資料夾數。 此外，它可用於在[Azure 檔同步部署](storage-sync-files-deployment-guide.md)之前創建透視圖 - 之後，當雲分層參與，您不僅希望看到專案數，還希望看到伺服器緩存使用最多的目錄。
    此工具（經過測試的版本 4.4.1）與雲分層檔相容。 在正常操作期間，它不會導致分層檔的調用。


## <a name="next-steps"></a>後續步驟

1. 創建計畫，為之部署 Azure 檔共用（僅限雲或混合共用）而努力。
2. 查看可用遷移指南的清單，查找與 Azure 檔共用的源和部署相匹配的詳細指南。

本文中提及的 Azure 檔技術提供了更多資訊：

* [Azure 檔共用概述](storage-files-introduction.md)
* [規劃 Azure 檔同步部署](storage-sync-files-planning.md)
* [Azure 檔同步：雲分層](storage-sync-cloud-tiering.md)
