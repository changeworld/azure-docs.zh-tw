---
title: 遷移至 Azure 檔案共用
description: 瞭解如何遷移至 Azure 檔案共用並尋找您的遷移指南。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81685979"
---
# <a name="migrate-to-azure-file-shares"></a>遷移至 Azure 檔案共用

本文涵蓋遷移至 Azure 檔案共用的基本層面。

本文包含遷移的基本概念，以及一份遷移指南的表格。 這些指南可協助您將檔案移至 Azure 檔案共用。 這些指南會根據您的資料所在位置，以及您要移至哪一個部署模型（僅限雲端或混合式）來進行組織。

## <a name="migration-basics"></a>遷移基本概念

Azure 有多種可用的雲端儲存體類型。 將檔案遷移至 Azure 的基本層面是判斷哪一個 Azure 儲存體選項適合您的資料。

[Azure 檔案共用](storage-files-introduction.md)適用于一般用途的檔案資料。 此資料包含您使用內部部署 SMB 或 NFS 共用的任何專案。 使用[Azure 檔案同步](storage-sync-files-planning.md)，您可以在執行 Windows Server 內部部署的伺服器上快取數個 Azure 檔案共用的內容。

針對目前在內部部署伺服器上執行的應用程式，將檔案儲存在 Azure 檔案共用中可能是不錯的選擇。 您可以將應用程式移至 Azure，並使用 Azure 檔案共用作為共用存放裝置。 在此案例中，您也可以考慮[Azure 磁片](../../virtual-machines/windows/managed-disks-overview.md)。

有些雲端應用程式不依存于 SMB 或電腦本機資料存取或共用存取。 對於這些應用程式而言， [Azure blob](../blobs/storage-blobs-overview.md)這類物件儲存體通常是最佳選擇。

任何遷移的關鍵，是要在將檔案從其目前的儲存位置移至 Azure 時，捕捉所有適用的檔案精確度。 Azure 儲存體選項支援的精確度，以及您的案例所需的程度也可協助您挑選正確的 Azure 儲存體。 一般用途的檔案資料通常取決於檔案中繼資料。 應用程式資料可能不是。

以下是檔案的兩個基本元件：

- **資料流程**：檔案的資料流程會儲存檔案內容。
- 檔案**中繼資料**：檔案中繼資料具有下列子元件：
   * 檔案屬性（例如唯讀）
   * 檔案許可權，可以稱為*NTFS 許可權*或檔案*和資料夾 acl* 。
   * 時間戳記，最值得注意的是建立和上次修改的時間戳記
   * 替代的資料流程，這是用來儲存大量非標準屬性的空間

您可以將遷移中的檔案精確度定義為下列功能：

- 將所有適用的檔案資訊儲存在來源上。
- 使用遷移工具傳輸檔案。
- 將檔案儲存在遷移的目標儲存體中。

為確保您的遷移順利進行，請根據您的需求找出[最佳的複製工具](#migration-toolbox)，並將儲存體目標與您的來源比對。

將先前的資訊納入考慮，您可以看到 Azure 中一般用途檔案的目標儲存體是[azure 檔案共用](storage-files-introduction.md)。

不同于 Azure blob 中的物件儲存體，Azure 檔案共用可以原生方式儲存檔案中繼資料。 Azure 檔案共用也會保留檔案和資料夾階層、屬性和許可權。 NTFS 許可權可以儲存在檔案和資料夾中，因為它們是在內部部署。

Active Directory 的使用者，也就是其內部部署網域控制站，可以原生方式存取 Azure 檔案共用。 因此，使用者可以 Azure Active Directory Domain Services （Azure AD DS）。 每個都會使用其目前的身分識別，根據共用許可權以及檔案和資料夾 Acl 來取得存取權。 此行為類似于連接到內部部署檔案共用的使用者。

替代資料流程是檔案精確度的主要層面，目前無法儲存在 Azure 檔案共用中的檔案。 使用 Azure 檔案同步時，它會保留在內部部署。

深入瞭解適用于 Azure 檔案共用的[Azure AD 驗證](storage-files-identity-auth-active-directory-enable.md)和[Azure AD DS 驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="migration-guides"></a>移轉指南

下表列出詳細的遷移指南。

如何使用資料表：

1. 找出檔案目前儲存所在之來源系統的資料列。

1. 選擇下列其中一個目標：

   - 使用 Azure 檔案同步來快取內部部署 Azure 檔案共用內容的混合式部署
   - 雲端中的 Azure 檔案共用

   選取符合您選擇的目標資料行。

1. 在來源和目標的交集中，資料表資料格會列出可用的遷移案例。 選取其中一個直接連結至詳細的遷移指南。

沒有連結的案例還沒有已發佈的遷移指南。 偶爾檢查此資料表的更新。 新的指南會在可用時發行。

| 來源 | 目標： </br>混合式部署 | 目標： </br>僅限雲端部署 |
|:---|:--|:--|
| | 工具組合：| 工具組合： |
| Windows Server 2012 R2 和更新版本 | <ul><li>[Azure 檔案同步](storage-sync-files-deployment-guide.md)</li><li>[Azure 檔案同步和 Azure 資料箱](storage-sync-offline-data-transfer.md)</li><li>Azure 檔案同步和儲存體遷移服務</li></ul> | <ul><li>Azure 檔案同步</li><li>Azure 檔案同步和資料箱</li><li>Azure 檔案同步和儲存體遷移服務</li><li>RoboCopy</li></ul> |
| Windows Server 2012 和更早版本 | <ul><li>Azure 檔案同步和資料箱</li><li>Azure 檔案同步和儲存體遷移服務</li></ul> | <ul><li>Azure 檔案同步和儲存體遷移服務</li><li>RoboCopy</li></ul> |
| 網路連接存放裝置（NAS） | <ul><li>[Azure 檔案同步和 RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux 或 Samba | <ul><li>[Azure 檔案同步和 RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Microsoft Azure StorSimple 雲端設備8100或 StorSimple 雲端設備8600 | <ul><li>[Azure 檔案同步和 StorSimple 雲端設備8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 雲端設備1200 | <ul><li>[Azure 檔案同步](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>遷移工具箱

### <a name="file-copy-tools"></a>檔案複製工具

有數個檔案複製工具可供 Microsoft 和其他使用者使用。 若要為您的遷移案例選取適當的工具，您必須考慮下列基本問題：

* 此工具是否支援檔案複製的來源和目標位置？

* 此工具支援來源與目標儲存位置之間的網路路徑或可用的通訊協定（例如 REST、SMB 或 NFS）嗎？

* 此工具會保留來源和目標位置所支援的必要檔案精確度嗎？

    在某些情況下，您的目標儲存體不支援與來源相同的精確度。 如果目標儲存體滿足您的需求，此工具必須僅符合目標的檔案精確度功能。

* 此工具具有可讓它符合您的遷移策略的功能嗎？

    例如，請考慮此工具是否能讓您將停機時間降到最低。
    
    當工具支援將來源鏡像至目標的選項時，您通常可以在相同的來源和目標上多次執行它，而來源則保持可存取狀態。

    第一次執行此工具時，它會複製大量資料。 這個初始執行可能會持續一段時間。 這通常會持續比您想要讓商務程式的資料來源離線。

    藉由將來源鏡像至目標（如同**robocopy/MIR**），您可以在該相同的來源和目標上再次執行此工具。 執行速度會更快，因為它只需要傳輸在上一次執行之後發生的來源變更。 以這種方式重新執行複製工具可以大幅減少停機時間。

下表將分類 Microsoft 工具及其目前適用于 Azure 檔案共用的適用性：

| 建議 | 工具 | 支援 Azure 檔案共用 | 檔案精確度的保留 |
| :-: | :-- | :---- | :---- |
|![是，建議使用](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | 支援。 Azure 檔案共用可以裝載為網路磁碟機機。 | 完全精確度。 * |
|![是，建議使用](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 檔案同步 | 原本就整合到 Azure 檔案共用。 | 完全精確度。 * |
|![是，建議使用](media/storage-files-migration-overview/circle-green-checkmark.png)| 存放裝置移轉服務 | 間接支援。 您可以將 Azure 檔案共用掛接為 SMS 目標伺服器上的網路磁碟機機。 | 完全精確度。 * |
|![未完全建議](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| 資料箱 | 支援。 | 不會複製中繼資料。 [資料箱可以與 Azure 檔案同步搭配使用](storage-sync-offline-data-transfer.md)。 |
|![不建議使用](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | 支援。 | 不會複製中繼資料。 |
|![不建議使用](media/storage-files-migration-overview/circle-red-x.png)| Azure 儲存體總管 | 支援。 | 不會複製中繼資料。 |
|![不建議使用](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | 支援。 | 不會複製中繼資料。 |
|||||

*\*完整精確度：符合或超過 Azure 檔案共用功能。*

### <a name="migration-helper-tools"></a>遷移 helper 工具

本節說明可協助您規劃和執行遷移的工具。

#### <a name="robocopy-from-microsoft-corporation"></a>來自 Microsoft Corporation 的 RoboCopy

RoboCopy 是最適用于檔案遷移的其中一項工具。 它是 Windows 的一部分。 主要[RoboCopy 檔](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)是此工具的許多選項的實用資源。

#### <a name="treesize-from-jam-software-gmbh"></a>從卡紙軟體 GmbH TreeSize

Azure 檔案同步主要是以專案（檔案和資料夾）的數目，而不是總儲存體數量來調整。 TreeSize 工具可讓您判斷 Windows Server 磁片區上的專案數。

您可以使用工具來建立[Azure 檔案同步部署](storage-sync-files-deployment-guide.md)之前的觀點。 當雲端階層處理部署之後，您也可以使用它。 在這種情況下，您會看到專案數目，以及哪些目錄最常使用您的伺服器快取。

已測試的工具版本為版本4.4.1。 它與雲端階層式檔案相容。 此工具不會在正常作業期間重新叫用分層檔案。

## <a name="next-steps"></a>後續步驟

1. 建立您想要部署 Azure 檔案共用（僅限雲端或混合式）的方案。
1. 請參閱可用的遷移指南清單，尋找符合 Azure 檔案共用來源和部署的詳細指南。

以下是本文中所述 Azure 檔案儲存體技術的詳細資訊：

* [Azure 檔案共用總覽](storage-files-introduction.md)
* [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
* [Azure 檔案同步：雲端階層處理](storage-sync-cloud-tiering.md)
