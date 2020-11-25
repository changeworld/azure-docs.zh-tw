---
title: '適用于 Azure (preview 的 Data Lake Storage 和 WANdisco 即時資料活頁平臺) '
description: 使用適用于 Azure 的 WANdisco 即時資料活頁 Platform 將內部部署 Hadoop 資料移轉至 Azure Data Lake Storage Gen2。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/06/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: db95f22a17c3776d84f12249693fb23b3d2d94e6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95914434"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>使用適用于 Azure (preview 的 WANdisco 即時資料活頁 Platform 滿足要求的遷移需求) 

使用 [適用于 Azure 的 WANdisco 即時資料活頁 Platform](https://docs.wandisco.com/live-data-platform/docs/landing/)將內部部署 Hadoop 資料移轉至 Azure Data Lake Storage Gen2。 此平臺不需要應用程式停機、消除資料遺失的機率，以及確保即使在內部部署作業繼續運作時，仍可確保資料一致性。  

> [!NOTE]
> 適用于 Azure 的 WANdisco 即時資料活頁 Platform 處於公開預覽狀態。 如需瞭解區域的可用性，請參閱 [支援的區域](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions)。

平臺由兩個服務所組成： [即時資料活頁的遷移程式](https://www.wandisco.com/products/livedata-migrator-for-azure) 可將主動使用的資料從內部部署環境遷移至 azure 儲存體，並使用 [即時資料活頁平面](https://www.wandisco.com/products/livedata-plane-for-azure) 來確保所有修改過的資料或內嵌資料都能以一致的方式複寫。 

> [!div class="mx-imgBorder"]
> ![即時資料平臺總覽圖例](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

您可以使用 Azure 入口網站和 Azure CLI 來管理這兩項服務，並遵循與所有其他 Azure 服務相同的計量付費、隨用隨付計費模型。 即時資料活頁 Platform for Azure 使用量會出現在相同的每月 Azure 帳單上，並提供一致且方便的方式來追蹤和監視您的使用量。

不同 [于將靜態資訊複製到 Azure 資料箱](./data-lake-storage-migrate-on-premises-hdfs-cluster.md)，或使用 [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)之類的 Hadoop 工具來將資料 _離線_ 遷移，您可以在使用 WANdisco 即時資料活頁 for Azure 進行 _線上_ 遷移期間，維護商務系統的完整運作。 即使在將資料移至 Azure 時，仍可讓您的大型資料環境保持運作。

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>適用于 Azure 的 WANdisco 即時資料活頁 Platform 主要功能

[適用于 Azure 的 WANdisco 即時資料活頁 Platform](https://docs.wandisco.com/live-data-platform/docs/landing/) 會使用獨特的廣域網路型共識引擎來達到資料一致性，並進行大規模的資料複寫，同時讓應用程式可以繼續修改複寫中的資料。  

平臺的主要功能包括下列各項：

- **資料一致性**：解決在環境之間遷移大型資料磁片區的挑戰，並讓這些資料在儲存體系統的輸送量遷移之間保持一致，即使它們不斷變更也是一樣。 在 Azure 中直接採用 WANdisco 的獨特、廣域網路支援的共識引擎，以達成資料一致性，並在資料變更時以一致性保證遷移資料。

- **維護作業**：因為應用程式可以在遷移期間繼續建立、修改、讀取和刪除資料，因此不需要中斷商務作業或引入中斷時段，只要將大型資料移轉至 Azure 即可。 繼續操作應用程式、分析基礎結構、內嵌作業和其他處理。

- **驗證結果**：端對端驗證，一旦遷移至 Azure 後，您的資料就可以有效地使用，需要您對它們執行生產應用程式工作負載。 只有即時資料活頁服務會藉由維持資料一致性，而不會產生資料分歧的風險，而不論是否在您的遷移來源或目標上發生變更，都只會提供此服務。 測試和驗證應用程式行為，而不需要風險或變更您的進程和系統。

- **降低複雜性**：不需要建立及管理排程工作，就可以透過自動化來遷移資料來複製資料。 使用與 Azure 的深度整合作為控制平面來管理及監視遷移進度，包括選擇性的資料複寫、Hive 中繼資料、資料安全性和機密性。

- **效率**：維持高輸送量和效能，並輕鬆擴充至海量資料量。 透過頻寬耗用量的控制，您可以確保您可以符合遷移目標，而不會影響其他系統作業。

## <a name="migrate-big-data-faster-without-risk"></a>更快速地遷移大型資料，而不會有風險

適用于 Azure 的 WANdisco 即時資料活頁 Platform 第一個服務是 [適用于 azure 的即時資料活頁遷移](https://www.wandisco.com/products/livedata-migrator-for-azure)程式;將主動使用的資料從內部部署環境遷移至 Azure 儲存體的解決方案。 適用于 Azure 的即時資料活頁遷移程式會完全由 Azure 入口網站或 Azure CLI 來布建和管理，並在內部部署與 Hadoop 叢集一起運作，而不需要任何設定變更、應用程式修改或服務重新開機來立即開始遷移資料。

> [!div class="mx-imgBorder"]
> ![適用于 Azure 架構的即時資料活頁遷移程式](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

龐大的資料移轉可能很複雜，而且頗具挑戰性。 在不中斷商務作業的情況下移動數 pb 的資訊，無法透過離線資料複製技術達成。 [適用于 Azure 的即時資料活頁遷移](https://www.wandisco.com/products/livedata-migrator-for-azure) 程式可提供簡單的部署，並可在應用程式讀取、寫入和修改所要遷移的資料時，建立具有持續資料移轉和複寫的即時資料活頁服務。

只要執行下列三個步驟，就能執行遷移：

1. 從 Azure 入口網站將即時資料活頁遷移者實例布建至您的內部部署 Hadoop 叢集。 不需要叢集變更或停機，應用程式可以繼續運作。

   > [!div class="mx-imgBorder"]
   >![建立即時資料活頁遷移遷移實例](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. 定義目標 Azure Data Lake Storage Gen2 啟用的儲存體帳戶。

   > [!div class="mx-imgBorder"]
   >![建立即時資料活頁遷移目標](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. 定義您想要遷移的資料位置，例如： `/user/hive/warehouse` ，然後開始遷移。

   > [!div class="mx-imgBorder"]
   > ![建立即時資料活頁遷移遷移](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

透過標準 Azure 工具（包括 Azure CLI 和 Azure 入口網站）來監視您的遷移進度，並在整個環境中繼續使用您的內部部署環境。 開始之前，請先檢查這些 [必要條件](https://docs.wandisco.com/live-data-platform/docs/prereq/)。

## <a name="replicate-data-under-active-change"></a>複寫主動變更下的資料

將內部部署資料 lake 至 Azure 的大規模遷移需要進行應用程式測試和驗證。 為了消除風險並將移至 Azure 的成本降到最低，您可以在不產生資料變更的風險下達成此目的。 [適用于 Azure 的即時資料活頁平面](https://www.wandisco.com/products/livedata-plane-for-azure) 會使用 WANdisco 的協調引擎技術來克服這些考慮。

> [!div class="mx-imgBorder"]
> ![適用于 Azure 架構的即時資料活頁平面](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

在初始遷移後，使用適用于 Azure 的即時資料活頁平面，讓您的資料在內部部署 Hadoop 叢集和 Azure 儲存體之間保持一致：

1. 從 Azure 入口網站開始，為內部部署和 Azure 中的 Azure 布建即時資料活頁平面。 不需要變更任何應用程式。
2. 設定複寫規則，以涵蓋您想要保持一致的資料位置，例如： `/user/contoso/sales/region/WA` 。
3. 執行應用程式，以依您的需求，在其中一個位置存取和修改資料，以作為 Hadoop 相容檔案系統。

適用于 Azure 的即時資料活頁平面可保持資料一致，而不會對叢集作業或應用程式效能造成大量的額外負荷。 修改或內嵌資料，並以一致的方式複寫所有變更。

## <a name="next-steps"></a>後續步驟

- 適用于 azure 的[即時資料活頁平臺](https://docs.wandisco.com/live-data-platform/docs/landing/)會像任何其他 Azure 資源一樣使用，現在可供預覽。 

- 瞭解 [必要條件](https://docs.wandisco.com/live-data-platform/docs/prereq/)、規劃您的遷移，並使用適用于 Azure 的即時資料活頁遷移程式快速完成大規模的遷移。

- 試用即時資料活頁遷移程式，而不需要使用 [HDFS 沙箱](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/)的內部部署 Hadoop 叢集。

## <a name="see-also"></a>另請參閱

- [Azure Marketplace 上適用于 Azure 的即時資料活頁遷移程式](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Azure Marketplace 上適用于 Azure 的即時資料活頁平面](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [適用于 Azure 方案和定價的即時資料活頁遷移程式](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [適用于 Azure 方案和定價的即時資料活頁平面](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [適用于 Azure 的即時資料活頁 Platform 常見問題](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [適用于 Azure 的即時資料活頁平臺已知問題](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Azure Data Lake Storage Gen2 簡介](data-lake-storage-introduction.md)