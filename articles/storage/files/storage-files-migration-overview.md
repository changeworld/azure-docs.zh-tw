---
title: 遷移至 Azure 檔案共用
description: 瞭解遷移到 Azure 檔共享並查找遷移指南。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685979"
---
# <a name="migrate-to-azure-file-shares"></a>遷移至 Azure 檔案共用

本文介紹遷移到 Azure 檔共用的基本方面。

本文包含遷移基礎知識和遷移指南表。 這些指南可説明您將文件移動到 Azure 檔共享中。 指南根據資料所在的位置以及要移動到的部署模型(僅限雲或混合部署)進行組織。

## <a name="migration-basics"></a>遷移基礎知識

Azure 具有多種可用類型的雲端儲存。 檔遷移到 Azure 的一個基本方面是確定哪種 Azure 儲存選項適合您的資料。

[Azure 檔案共享](storage-files-introduction.md)適用於通用文件數據。 此資料包括您使用本地 SMB 或 NFS 共用的任何內容。 使用[Azure 檔案同步](storage-sync-files-planning.md),可以在本地運行 Windows 伺服器的伺服器上快取多個 Azure 檔案共享的內容。

對於當前在本地伺服器上運行的應用,將檔案存儲在 Azure 檔共享中可能是一個不錯的選擇。 您可以將應用移動到 Azure,並使用 Azure 檔共享作為共用存儲。 您可以考慮這個專案[的 Azure 磁碟](../../virtual-machines/windows/managed-disks-overview.md)。

某些雲端應用不依賴於 SMB 或電腦本地數據訪問或共享訪問。 對於這些應用,像[Azure Blob](../blobs/storage-blobs-overview.md)這樣的物件存儲通常是最佳選擇。

任何遷移的鍵是在將檔從當前存儲位置移動到 Azure 時捕獲所有適用的檔保真度。 Azure 儲存選項支援的保真度以及方案所需的多少也可以説明您選擇正確的 Azure 儲存。 通用文件數據傳統上取決於檔元數據。 應用數據可能不是。

下面是檔案的兩個基本元件:

- **資料流**:檔案的資料流程儲存檔案內容。
- **檔案中繼資料**:檔中繼資料:
   * 檔案屬性(如唯讀)
   * 檔案權限,可以稱為*NTFS 權限*或*檔案與資料夾 ACL*
   * 時間戳,最顯著的是創建和上次修改的時間戳
   * 替代資料流程,用於儲存大量非標準屬性的空間

移動的檔案保存度可以定義為:

- 將所有適用的檔案資訊存儲在源上。
- 使用遷移工具傳輸檔。
- 將檔案存儲在遷移的目標存儲中。

為確保遷移順利進行,請確定[適合您需求的最佳複製工具](#migration-toolbox),並將存儲目標與源相匹配。

考慮到前面的資訊,您可以看到 Azure 中一般檔案的目標儲存是[Azure 檔案共享](storage-files-introduction.md)。

與 Azure Blob 中的物件存儲不同,Azure 檔案共用可以本機儲存檔元數據。 Azure 檔案共用還保留檔和資料夾層次結構、屬性和許可權。 NTFS 許可權可以存儲在檔和資料夾上,因為它們是本地的。

Active Directory 的使用者可以本機訪問 Azure 檔共用(其本地域控制器)。 Azure 活動目錄域服務 (Azure AD DS) 的使用者也是如此。 每個使用其當前識別根據共享許可權以及文件和資料夾 ACL 訪問。 此行為類似於連接到本地檔共享的使用者。

替代資料流是檔保真度的主要方面,當前無法存儲在 Azure 檔共享中的檔中。 使用 Azure 檔同步時,它將儲存在本地。

詳細瞭解 Azure 檔案分享的[Azure AD 認證](storage-files-identity-auth-active-directory-enable.md)與 Azure AD [DS 認證](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="migration-guides"></a>移轉指南

下表列出了詳細的遷移指南。

如何使用表:

1. 查找檔案目前儲存的源系統的行。

1. 選擇以下目標之一:

   - 使用 Azure 檔案同步在本地快取 Azure 檔案分享內容的混合部署
   - 雲中的 Azure 檔案分享

   選擇與您的選擇相匹配的目標列。

1. 在源和目標交集中,表單元格列出了可用的遷移方案。 選擇一個可直接連結到詳細遷移指南。

沒有連結的方案還沒有發佈的遷移指南。 偶爾檢查此表以獲取更新。 新指南將在可用時發佈。

| 來源 | 目標： </br>混合式部署 | 目標： </br>只限雲的部署 |
|:---|:--|:--|
| | 工具組合:| 工具組合: |
| Windows 伺服器 2012 R2 及更高版本 | <ul><li>[Azure 檔案同步](storage-sync-files-deployment-guide.md)</li><li>[Azure 檔案同步與 Azure 資料框](storage-sync-offline-data-transfer.md)</li><li>Azure 檔案同步和儲存遷移服務</li></ul> | <ul><li>Azure 檔案同步</li><li>Azure 檔案同步與資料框</li><li>Azure 檔案同步和儲存遷移服務</li><li>RoboCopy</li></ul> |
| Windows 伺服器 2012 及更早版本 | <ul><li>Azure 檔案同步與資料框</li><li>Azure 檔案同步和儲存遷移服務</li></ul> | <ul><li>Azure 檔案同步和儲存遷移服務</li><li>RoboCopy</li></ul> |
| 網路連線儲存 (NAS) | <ul><li>[Azure 檔案同步與 RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux 或桑巴 | <ul><li>[Azure 檔案同步與 RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| 微軟 Azure StorSimple 雲端設備 8100 或 StorSimple 雲端設備 8600 | <ul><li>[Azure 檔案同步和 StorSimple 雲端裝置 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 雲設備 1200 | <ul><li>[Azure 檔案同步](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>移轉工具箱

### <a name="file-copy-tools"></a>檔案複製工具

微軟和其他公司提供了多種文件複製工具。 要為您的遷移方案選擇正確的工具,必須考慮以下基本問題:

* 該工具是否支援檔案複本的來源位置和目標位置?

* 該工具是否支援來源和目標儲存位置之間的網路路徑或可用協定(如 REST、SMB 或 NFS)?

* 該工具是否保留源和目標位置支援的必要檔保真度?

    在某些情況下,目標存儲不支援與源相同的保真度。 如果目標存儲足以滿足您的需要,則該工具必須僅匹配目標的檔保真功能。

* 該工具是否具有使其適合您的遷移策略的功能?

    例如,請考慮該工具是否允許您將停機時間降至最低。
    
    當工具支援將源鏡像到目標的選項時,通常可以在源保持可訪問時在同一源和目標上運行多次。

    首次運行該工具時,它會複製大部分數據。 此初始運行可能會持續一段時間。 對於業務流程,使數據源離線的時間通常比您希望的要長。

    以將源鏡像到目標(如**使用 robocopy /MIR),** 可以在同一源和目標上再次執行該工具。 運行速度要快得多,因為它只需要傳輸上次運行後發生的源更改。 以這種方式重新運行複製工具可以顯著減少停機時間。

下表對 Microsoft 工具及其目前是否適合 Azure 檔案分享進行分類:

| 建議 | 工具 | 支援 Azure 檔案分享 | 儲存檔案保存 |
| :-: | :-- | :---- | :---- |
|![是,建議](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | 支援。 Azure 檔案共用可以裝載為網路驅動器。 | 完全保真度。 |
|![是,建議](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 檔案同步 | 本機集成到 Azure 檔共享中。 | 完全保真度。 |
|![是,建議](media/storage-files-migration-overview/circle-green-checkmark.png)| 存放裝置移轉服務 | 間接支援。 Azure 檔案共享可以載入為 SMS 目標伺服器上的網路驅動器。 | 完全保真度。 |
|![不完全推薦](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| 資料箱 | 支援。 | 不複製元數據。 [資料框可與 Azure 檔案同步一起使用](storage-sync-offline-data-transfer.md)。 |
|![不建議使用](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | 支援。 | 不複製元數據。 |
|![不建議使用](media/storage-files-migration-overview/circle-red-x.png)| Azure 儲存體總管 | 支援。 | 不複製元數據。 |
|![不建議使用](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | 支援。 | 不複製元數據。 |
|||||

*\*完全保真度:滿足或超過 Azure 檔共用功能。*

### <a name="migration-helper-tools"></a>移轉說明工具

本節介紹可説明您規劃和運行遷移的工具。

#### <a name="robocopy-from-microsoft-corporation"></a>微軟公司的RoboCopy

RoboCopy 是最適合檔遷移的工具之一。 它是 Windows 的一部分。 主要[RoboCopy 文件](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)是此工具的許多選項的有用資源。

#### <a name="treesize-from-jam-software-gmbh"></a>來自 JAM 軟體有限公司的樹大小

Azure 檔案同步主要隨專案數(文件和資料夾)數進行縮放,而不是使用總存儲量進行縮放。 樹大小工具允許您確定 Windows 伺服器卷上的項目數。

可以使用 該工具在[Azure 檔同步部署](storage-sync-files-deployment-guide.md)之前創建透視圖。 您還可以在部署後參與雲分層時使用它。 在這種情況下,您將看到專案數以及伺服器緩存最多的目錄。

該工具的測試版本為版本 4.4.1。 它與雲分層文件相容。 該工具在正常操作期間不會導致分層文件的調用。

## <a name="next-steps"></a>後續步驟

1. 創建需要部署 Azure 檔共用(僅限雲或混合)的計畫。
1. 查看可用遷移指南的清單,查找與 Azure 檔共用的源和部署相匹配的詳細指南。

以下是本文中提到的有關 Azure 檔技術的詳細資訊:

* [Azure 檔案分享概述](storage-files-introduction.md)
* [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
* [Azure 檔案同步:雲分層](storage-sync-cloud-tiering.md)
