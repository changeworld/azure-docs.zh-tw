---
title: 遷移至 Azure 檔案共用
description: 瞭解如何遷移至 Azure 檔案共用，並尋找您的遷移指南。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: e3ecf29be94074f51ead3173f997154df6dfa88f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785607"
---
# <a name="migrate-to-azure-file-shares"></a>遷移至 Azure 檔案共用

本文涵蓋遷移至 Azure 檔案共用的基本層面。

本文包含遷移的基本概念和一份遷移指南的表格。 這些指南可協助您將檔案移至 Azure 檔案共用。 這些指南是根據您的資料所在位置，以及您要移往哪些部署模型 (僅限雲端或混合式) 來進行組織。

## <a name="migration-basics"></a>遷移基本概念

Azure 有多種可用的雲端儲存體類型。 檔案遷移至 Azure 的基本層面是決定哪一個 Azure 儲存體選項適合您的資料。

[Azure 檔案共用](storage-files-introduction.md) 適用于一般用途的檔案資料。 此資料包含您使用內部部署 SMB 或 NFS 共用的任何內容。 使用 [Azure 檔案同步](storage-sync-files-planning.md)，您可以在執行 Windows Server 內部部署的伺服器上快取數個 Azure 檔案共用的內容。

針對目前在內部部署伺服器上執行的應用程式，在 Azure 檔案共用中儲存檔案可能是不錯的選擇。 您可以將應用程式移至 Azure，並使用 Azure 檔案共用作為共用儲存體。 您也可以在此案例中考慮 [Azure 磁片](../../virtual-machines/managed-disks-overview.md) 。

某些雲端應用程式不會相依于 SMB 或電腦本機資料存取或共用存取。 針對這些應用程式，物件儲存體（例如 [Azure blob](../blobs/storage-blobs-overview.md) ）通常是最佳選擇。

任何遷移的關鍵是在將您的檔案從其目前的儲存位置移至 Azure 時，捕捉所有適用的檔案精確度。 Azure 儲存體選項支援的精確度和您的案例所需的精確度，也可協助您挑選正確的 Azure 儲存體。 一般用途的檔案資料通常取決於檔案中繼資料。 應用程式資料可能不是。

以下是檔案的兩個基本元件：

- **資料流程** ：檔案的資料流程會儲存檔案內容。
- 檔案 **中繼資料** ：檔案中繼資料具有下列子元件：
   * 檔案屬性，例如唯讀
   * 檔案許可權，可稱為 *NTFS 許可權* 或檔案 *和資料夾 acl*
   * 時間戳記，最特別是建立和上次修改時間戳記
   * 替代資料流程，這是儲存較大量非標準屬性的空間

您可以將遷移中的檔案精確度定義為：

- 將所有適用的檔案資訊儲存在來源上。
- 使用遷移工具來傳送檔案。
- 在遷移的目標儲存體中儲存檔案。

為了確保您的遷移順利進行，請找出 [最適合您需求的複製工具](#migration-toolbox) ，並將儲存體目標與來源進行比對。

將先前的資訊納入考慮，您可以看到 Azure 中一般用途檔案的目標儲存體是 [azure 檔案共用](storage-files-introduction.md)。

不同于 Azure blob 中的物件儲存體，Azure 檔案共用可以原生方式儲存檔案中繼資料。 Azure 檔案共用也會保留檔案和資料夾階層、屬性和許可權。 NTFS 許可權可以儲存在檔案和資料夾中，因為它們是在內部部署。

Active Directory 的使用者（也就是其內部部署網域控制站）可以原生存取 Azure 檔案共用。 因此，Azure Active Directory Domain Services (Azure AD DS) 的使用者可以使用。 每個都使用其目前的身分識別，根據共用許可權以及檔案和資料夾 Acl 來取得存取權。 此行為類似于連接至內部部署檔案共用的使用者。

替代的資料流程是檔案精確度的主要層面，目前無法儲存在 Azure 檔案共用中的檔案。 使用 Azure 檔案同步時，它會保留在內部部署。

深入瞭解 Azure 檔案共用 [Azure AD 驗證](storage-files-identity-auth-active-directory-enable.md) 和 [Azure AD DS 驗證](storage-files-identity-auth-active-directory-domain-service-enable.md) 。

## <a name="migration-guides"></a>移轉指南

下表列出詳細的遷移指南。

如何使用資料表：

1. 找出您的檔案目前儲存所在來源系統的資料列。

1. 選擇下列其中一個目標：

   - 使用 Azure 檔案同步來快取內部部署 Azure 檔案共用內容的混合式部署
   - 雲端中的 Azure 檔案共用

   選取符合您選擇的目標資料行。

1. 在來源和目標的交集內，資料表資料格會列出可用的遷移案例。 選取其中一個可直接連結到詳細的「遷移指南」。

沒有連結的案例還沒有已發佈的遷移指南。 請偶爾檢查此資料表是否有更新。 當新指南可用時，將會發佈新的指南。

| 來源 | 目標： </br>混合式部署 | 目標： </br>僅限雲端部署 |
|:---|:--|:--|
| | 工具組合：| 工具組合： |
| Windows Server 2012 R2 和更新版本 | <ul><li>[Azure 檔案同步](storage-sync-files-deployment-guide.md)</li><li>[Azure 檔案同步和 Azure 資料箱](storage-sync-offline-data-transfer.md)</li><li>[在雲端中 Azure 檔案同步和預先植入的檔案](storage-sync-offline-data-transfer.md#azure-file-sync-and-pre-seeded-files-in-the-cloud)</li><li>Azure 檔案同步和儲存體遷移服務</li></ul> | <ul><li>Azure 檔案同步</li><li>Azure 檔案同步和資料箱</li><li>Azure 檔案同步和儲存體遷移服務</li><li>RoboCopy</li></ul> |
| Windows Server 2012 及更早版本 | <ul><li>Azure 檔案同步和資料箱</li><li>Azure 檔案同步和儲存體遷移服務</li></ul> | <ul><li>Azure 檔案同步和儲存體遷移服務</li><li>RoboCopy</li></ul> |
| 連接網路的存放裝置 (NAS)  | <ul><li>[Azure 檔案同步和 RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux 或 Samba | <ul><li>[Azure 檔案同步和 RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Microsoft Azure StorSimple 雲端設備8100或 StorSimple 雲端設備8600 | <ul><li>[Azure 檔案同步和 StorSimple 雲端設備8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 雲端設備1200 | <ul><li>[Azure 檔案同步](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>遷移工具箱

### <a name="file-copy-tools"></a>檔案複製工具

有數個檔案複製工具可供 Microsoft 和其他人使用。 若要為您的遷移案例選取適當的工具，您必須考慮下列基本問題：

* 工具是否支援檔案複製的來源和目標位置？

* 工具是否支援您的網路路徑或可用的通訊協定 (例如來源和目標儲存位置之間的 REST、SMB 或 NFS) ？

* 此工具是否會保留您的來源和目標位置所支援的檔案精確度？

    在某些情況下，您的目標儲存體不支援和來源相同的精確度。 如果目標儲存體滿足您的需求，此工具必須只符合目標的檔案精確度功能。

* 此工具是否有可讓它符合您的遷移策略的功能？

    例如，請考慮此工具是否可讓您將停機時間降至最低。
    
    當工具支援將來源鏡像至目標的選項時，您通常可以在相同的來源和目標上多次執行它，而來源仍然可供存取。

    當您第一次執行此工具時，它會複製大量資料。 此初始執行可能會持續一段時間。 它通常會比您想要讓資料來源離線進行商務程式的時間還長。

    藉由將來源鏡像至目標 (，如同 **robocopy/MIR** ) ，您可以在相同的來源和目標上重新執行此工具。 執行速度會更快，因為它只需要傳輸在上一次執行之後發生的來源變更。 以這種方式重新執行複製工具可以大幅減少停機時間。

下錶針對 Azure 檔案共用分類 Microsoft 工具和其目前的適用性：

| 建議 | 工具 | 支援 Azure 檔案共用 | 保留檔案精確度 |
| :-: | :-- | :---- | :---- |
|![是，建議使用](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | 支援。 Azure 檔案共用可裝載為網路磁碟機機。 | 完整的精確度。 * |
|![是，建議使用](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 檔案同步 | 原生整合至 Azure 檔案共用。 | 完整的精確度。 * |
|![是，建議使用](media/storage-files-migration-overview/circle-green-checkmark.png)| 存放裝置移轉服務 | 間接支援。 您可以將 Azure 檔案共用掛接為 SMS 目標伺服器上的網路磁碟機機。 | 完整的精確度。 * |
|![是，建議使用](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy，10.4 版或更新版本| 支援。 | 完整的精確度。 * |
|![是，建議使用](media/storage-files-migration-overview/circle-green-checkmark.png)| 資料箱 | 支援。 | 資料箱現在完全支援中繼資料。 [資料箱也可以搭配 Azure 檔案同步一起使用](storage-sync-offline-data-transfer.md)。 |
|![不是完全建議](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure 儲存體總管，版本1.14 | 支援。 | 不會複製 Acl。 支援時間戳記。  |
|![不建議使用](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | 支援。 | 不會複製中繼資料。 |
|||||

*\* 完全精確度：符合或超過 Azure 檔案共用功能。*

### <a name="migration-helper-tools"></a>遷移 helper 工具

本節說明可協助您規劃和執行遷移的工具。

#### <a name="robocopy-from-microsoft-corporation"></a>Microsoft Corporation 的 RoboCopy

RoboCopy 是最適合檔案遷移的工具之一。 它是 Windows 的一部分。 主要 [RoboCopy 檔](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 是這項工具許多選項的實用資源。

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize from 卡住 Software GmbH

Azure 檔案同步的調整主要是 (檔案和) 資料夾的專案數，而不是總儲存空間數量。 TreeSize 工具可讓您判斷 Windows Server 磁片區上的專案數。

您可以使用此工具來建立 [Azure 檔案同步部署](storage-sync-files-deployment-guide.md)之前的觀點。 您也可以在部署雲端階層處理時使用它。 在該案例中，您會看到專案數目，以及哪些目錄最常使用您的伺服器快取。

測試的工具版本是4.4.1 版。 它與雲端階層式檔案相容。 此工具不會在其正常操作期間重新叫用階層式檔案。

## <a name="next-steps"></a>後續步驟

1. 建立 Azure 檔案共用部署 (僅限雲端或您想要的混合式) 的方案。
1. 請參閱可用的遷移指南清單，以尋找符合您的 Azure 檔案共用來源和部署的詳細指南。

以下是本文中所述 Azure 檔案儲存體技術的詳細資訊：

* [Azure 檔案共用總覽](storage-files-introduction.md)
* [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
* [Azure 檔案同步：雲端分層](storage-sync-cloud-tiering.md)
