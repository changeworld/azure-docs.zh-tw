---
title: Avere vFXT for Azure 的嚴重損壞修復指導方針
description: 如何保護 Avere vFXT for Azure 中的資料免于意外刪除或中斷
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75966656"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Avere vFXT for Azure 的嚴重損壞修復指導方針

本文概述保護 Avere vFXT for Azure 工作流程的策略，並提供備份資料的指引，讓您可以從事故或中斷中復原。

Avere vFXT for Azure 會暫時將資料儲存在其快取中。 資料會長期儲存在後端儲存體系統中-內部部署硬體系統、Azure Blob 儲存體容器或兩者。

若要防止中斷和資料可能遺失，請考慮下列四個領域：

* 當 Avere vFXT for Azure 系統變得無法使用時，保護停機
* 保護叢集中的資料
* 保護後端 NAS 硬體儲存體中的資料
* 保護後端 Azure Blob 雲端儲存體中的資料

每 Avere vFXT for Azure 客戶都必須建立自己的全方位嚴重損壞修復計畫，其中包含這些專案的方案。 您也可以在與 vFXT 叢集搭配使用的應用程式中建立恢復功能。 請參閱 [後續步驟](#next-steps) 中的連結以取得協助。

## <a name="protect-against-downtime"></a>防止停機

Avere vFXT for Azure 產品內建了冗余：

* 叢集具有高可用性，而且個別叢集節點可以在最短中斷時進行容錯移轉。
* 在快取中變更的資料會定期寫入至後端核心檔 (硬體 NAS 或 Azure Blob) 以進行長期儲存。

每個 Avere vFXT for Azure 叢集都必須位於單一可用性區域中，但您可以使用位於不同區域或不同區域的重複叢集，以在發生區域性中斷時快速提供存取。

如果您擔心無法存取資料，您也可以在多個區域中放置儲存體容器。 不過，請記住，區域之間的交易具有的延遲和成本高於區域內的交易。

## <a name="protect-data-in-the-cluster-cache"></a>保護叢集中的資料

快取的資料一律會在正常關機之前寫入至核心檔，但在未受控制的關機中，已變更快取中的資料可能會遺失。

如果您使用叢集將檔案讀取優化，則不會遺失任何變更。 如果您也使用叢集來快取檔案變更 (寫入) ，請考慮是否要調整核心檔案系統的快取 [原則](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)<!-- link to legacy doc --> 自訂將資料寫入長期儲存的頻率。

一般而言，您的復原方案應該專注于備份後端儲存體系統，以保存更多資料，而且通常更重要的是在失敗後重新建立工作流程。

## <a name="protect-data-in-nas-core-filers"></a>保護 NAS 核心檔集中的資料

使用接受的方法來保護儲存在內部部署 NAS 硬體核心檔案管理工具中的資料，包括快照集和完整備份（由 NAS 提供者所建議）。 這些核心檔的嚴重損壞修復已超出本文的範圍。

## <a name="protect-data-in-azure-blob-storage"></a>保護 Azure Blob 儲存體中的資料

Avere vFXT for Azure 使用 Azure Blob 核心檔的本機冗余儲存體 (LRS) 。 這表示您 Blob 容器中的資料會自動複製，以防止資料中心內的暫時性硬體故障。

本節提供有關如何進一步保護 Blob 儲存體中的資料，避免罕見的全區域中斷或意外刪除的秘訣。

保護 Azure Blob 儲存體中資料的最佳作法包括：

* 將重要資料複製到另一個區域中的另一個儲存體帳戶，通常是由您的嚴重損壞修復計畫) 所決定的 (。
* 控制對所有目標系統上資料的存取，以防止意外刪除或損毀。 請考慮在資料儲存體上使用 [資源鎖定](../azure-resource-manager/management/lock-resources.md) 。
* 為您的 Blob 核心檔案系統啟用 Avere vFXT for Azure [雲端快照](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) 集功能。

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>將 Avere vFXT 核心檔案管理工具資料複製到備份帳戶

請遵循下列步驟，在其他帳戶中建立資料備份。

1. 如有需要，請產生新的加密金鑰，並將它安全地儲存在受影響的系統之外。

   如果您的資料是由 Avere vFXT for Azure 叢集加密，您應該先產生新的加密金鑰，然後再將資料複製到另一個儲存體帳戶。 將該金鑰和密碼安全地儲存在安全且不會受到區域性故障影響的設備中。

   將容器新增至叢集時，您必須提供此金鑰，即使您將它重新新增至其原始叢集也一樣。

   讀取 [雲端加密設定](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> 以取得詳細資訊。

   如果您的容器僅使用 Azure 的內建加密，您可以略過此步驟。

1. 從系統移除核心檔案管理工具。 這會強制叢集將所有變更的資料寫入後端儲存體。

   雖然您必須在備份之後重新新增核心檔案管理工具，但是將它移除是保證所有資料都已完全寫入後端的最佳方式。  (「暫停」選項有時可能會在快取中留下變更的資料。 )  <!-- xxx true? or just metadata? -->

   請記下 [控制台] 的 [ **命名空間** ] 頁面中所列的核心檔案管理工具的名稱和連接資訊 () ，以便您在備份之後重新加入容器時可以進行複寫。

   使用叢集控制台來移除核心檔案管理工具。 [開啟叢集控制台](avere-vfxt-cluster-gui.md)，然後選擇 [**核心**檔案管理工具]  >  **管理核心檔案管理**工具。 尋找您想要備份的儲存體系統，然後使用 [ **移除** ] 按鈕將它從叢集中刪除。

1. 在另一個區域的另一個儲存體帳戶中，建立新的空白 Blob 儲存體容器。

1. 使用任何便利的複製工具，將核心檔案管理工具上的資料複製到新的容器。 複本必須在不變更的情況下複寫資料，而不會中斷 Avere vFXT for Azure 所使用的專屬雲端檔案系統格式。 以 Azure 為基礎的工具組括 [AzCopy](../storage/common/storage-use-azcopy-v10.md)、 [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)和 [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)。

1. 將資料複製到備份容器之後，請依照 [設定儲存體](avere-vfxt-add-storage.md)中的說明，將原始容器新增回叢集。

   * 使用相同的核心檔案管理工具名稱和連接資訊，讓用戶端工作流程不需要變更。
   * 將 **Bucket 內容** 值設定為現有的資料選項。
   * 如果容器已由叢集加密，您必須輸入其內容的目前加密金鑰。  (這是您在步驟一中更新的金鑰 ) 

針對第一個備份之後的備份，您不需要建立新的儲存體容器。 不過，請考慮在每次執行備份時產生新的加密金鑰，以確保您將目前的金鑰儲存在您記住的位置。

### <a name="access-a-backup-data-source-during-an-outage"></a>在中斷期間存取備份資料來源

若要從 Avere vFXT for Azure 叢集中存取備份容器，請遵循下列程式：

1. 如有需要，請在不受影響的區域中建立新的 Avere vFXT for Azure 叢集。

   > [!TIP]
   > 當您建立 Avere vFXT for Azure 叢集時，您可以儲存其建立範本和參數的複本。 如果您在建立主要叢集時儲存此資訊，您可以使用它來建立具有相同屬性的替代叢集。 在 [ [摘要](avere-vfxt-deploy.md#validation-and-purchase) ] 頁面上，按一下 [ **下載範本和參數** ] 連結。 在建立叢集之前，請先將資訊儲存至檔案。

1. 新增指向重複 Blob 容器的新雲端核心檔案管理工具。

   請務必指定目標容器已在核心檔案管理工具建立嚮導的 **Bucket 內容** 設定中包含資料。  (系統應該會在您不小心將此設定保留為 **空白**時發出警示。 )   <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. 如有必要，請更新用戶端，使其掛接新叢集或新的核心檔案管理工具，而不是原始的檔案管理工具。  (如果您以與原始容器相同的名稱和連接路徑來新增取代核心檔案管理工具，除非您需要在新的 IP 位址掛接新叢集，否則不需要更新用戶端進程。 ) 

## <a name="next-steps"></a>後續步驟

* 如需自訂 Avere vFXT for Azure 設定的詳細資訊，請參閱叢集 [調整](avere-vfxt-tuning.md)。
* 深入瞭解嚴重損壞修復和在 Azure 中建立具復原能力的應用程式：

  * [Azure 復原技術指導](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [從全區域服務中斷復原](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Azure 應用程式的災害復原和高可用性](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
