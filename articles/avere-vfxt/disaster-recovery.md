---
title: Avere vFXT for Azure 的嚴重損壞修復指引
description: 如何保護 Avere vFXT for Azure 中的資料免于意外刪除或中斷
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75966656"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Avere vFXT for Azure 的嚴重損壞修復指引

本文概述保護您的 Avere vFXT for Azure 工作流程的策略，並提供備份資料的指引，讓您可以從意外或中斷中復原。

Avere vFXT for Azure 會暫時將資料儲存在其快取中。 資料會長期儲存在後端儲存體系統中-內部部署硬體系統、Azure Blob 儲存體容器或兩者。

若要防止中斷和可能的資料遺失，請考慮下列四個領域：

* 保護 Avere vFXT for Azure 系統變得無法使用時的停機時間
* 保護叢集快取中的資料
* 保護後端 NAS 硬體存放區中的資料
* 保護後端 Azure Blob 雲端儲存體中的資料

每個 Avere vFXT for Azure 客戶都必須建立自己的完整嚴重損壞修復計畫，其中包含這些專案的方案。 您也可以在與 vFXT 叢集搭配使用的應用程式中建立復原功能。 如需協助，請參閱[後續步驟](#next-steps)中的連結。

## <a name="protect-against-downtime"></a>防止停機時間

Avere vFXT for Azure 產品內建了冗余：

* 叢集具有高度可用性，而且個別叢集節點可以在最少中斷的情況下故障切換。
* 在快取中變更的資料會定期寫入後端核心檔存放區（硬體 NAS 或 Azure Blob）進行長期儲存。

每個 Avere vFXT for Azure 叢集都必須位於單一可用性區域中，但您可以使用位於不同區域或不同區域的冗余叢集，以在發生區域性中斷時快速提供存取權。

如果您擔心遺失資料的存取權，您也可以將儲存體容器放在多個區域中。 不過，請記住，區域之間的交易會有較高的延遲和較高的成本，而不是保留在某個區域內的交易。

## <a name="protect-data-in-the-cluster-cache"></a>保護叢集快取中的資料

在正常關機之前，快取的資料一律會寫入至核心檔案系統，但在不受控制的關機中，已變更快取中的資料可能會遺失。

如果您使用叢集來優化檔案讀取，則不會有任何變更會遺失。 如果您也使用叢集來快取檔案變更（寫入），請考慮是否要調整核心檔案檔案的快取[原則](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)<!-- link to legacy doc --> 自訂將資料寫入長期儲存體的頻率。

一般來說，您的復原計畫應該專注于備份後端儲存體系統，其中保存較多的資料，而且通常在失敗後重新建立工作流程時更為重要。

## <a name="protect-data-in-nas-core-filers"></a>保護 NAS 核心檔集中的資料

使用已接受的方法來保護儲存在內部部署 NAS 硬體核心檔案管理工具中的資料，包括 NAS 提供者建議的快照和完整備份。 這些核心檔的嚴重損壞修復已超出本文的範圍。

## <a name="protect-data-in-azure-blob-storage"></a>保護 Azure Blob 儲存體中的資料

Avere vFXT for Azure 使用 Azure Blob 核心檔存放區的本機多餘儲存體（LRS）。 這表示會自動複製 Blob 容器中的資料，以防止資料中心內發生暫時性硬體失敗。

本節提供如何進一步保護 Blob 儲存體中的資料，避免罕見的全區域中斷或意外刪除的秘訣。

保護 Azure Blob 儲存體中資料的最佳作法包括：

* 經常將重要資料複製到另一個區域中的另一個儲存體帳戶（通常是由您的嚴重損壞修復計畫所決定）。
* 控制對所有目標系統上資料的存取，以防止意外刪除或損毀。 請考慮在資料存放區上使用[資源鎖定](../azure-resource-manager/management/lock-resources.md)。
* 為您的 Blob 核心檔案系統啟用 [Avere vFXT for Azure[雲端快照](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>)集] 功能。

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>將 Avere vFXT 核心檔案管理工具資料複製到備份帳戶

請遵循下列步驟，在另一個帳戶中建立資料備份。

1. 如有需要，請產生新的加密金鑰，並將它安全地儲存在受影響的系統之外。

   如果您的資料由 Avere vFXT for Azure 叢集加密，您應該先產生新的加密金鑰，然後再將資料複製到另一個儲存體帳戶。 將金鑰和密碼安全地儲存在安全且不會受到區域失敗影響的設施中。

   將容器新增至叢集時，您必須提供此金鑰，即使您將它重新新增到其原始叢集也一樣。

   讀取[雲端加密設定](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> 以取得詳細資訊。

   如果您的容器僅使用 Azure 的內建加密，您可以略過此步驟。

1. 從系統中移除核心檔案管理工具。 這會強制叢集將所有已變更的資料寫入後端儲存體。

   雖然您必須在備份之後重新新增核心檔案管理工具，但移除它是保證所有資料都已完整寫入後端的最佳方式。 （[暫停] 選項有時可能會將變更的資料留在快取中）。 <!-- xxx true? or just metadata? -->

   記下核心檔案管理工具的名稱和連接資訊（列在 [控制台] 的 [**命名空間**] 頁面中），以便您在備份之後重新新增容器時進行複寫。

   使用 [叢集控制台] 移除核心檔案管理工具。 [開啟 [叢集控制台](avere-vfxt-cluster-gui.md)]，然後選擇 [核心檔案管理工具] [**管理核心**檔案**伺服器** > ]。 尋找您要備份的儲存系統，並使用 [**移除**] 按鈕將它從叢集刪除。

1. 在另一個區域的另一個儲存體帳戶中，建立新的空白 Blob 儲存體容器。

1. 使用任何便利的複製工具，將核心檔案管理器上的資料複製到新的容器。 複製必須複寫資料而不進行變更，而不會中斷 Avere vFXT for Azure 所使用的專利雲端檔案系統格式。 以 Azure 為基礎的工具組括[AzCopy](../storage/common/storage-use-azcopy-v10.md)、 [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)和[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)。

1. 將資料複製到備份容器之後，請依照[設定儲存體](avere-vfxt-add-storage.md)中的說明，將原始容器新增回叢集。

   * 使用相同的核心檔案管理工具名稱和連接資訊，讓用戶端工作流程不需要變更。
   * 將 [ **Bucket 內容**] 值設為 [現有資料] 選項。
   * 如果容器是由叢集加密，您必須輸入目前的加密金鑰做為其內容。 （這是您在步驟一中更新的金鑰）。

針對第一個備份之後，您不需要建立新的儲存體容器。 不過，請考慮在每次執行備份時產生新的加密金鑰，以確定您已將目前的金鑰儲存在您所記住的位置。

### <a name="access-a-backup-data-source-during-an-outage"></a>在中斷期間存取備份資料來源

若要從 Avere vFXT for Azure 叢集存取備份容器，請遵循此程式：

1. 如有需要，請在不受影響的區域中建立新的 Avere vFXT for Azure 叢集。

   > [!TIP]
   > 當您建立 Avere vFXT for Azure 叢集時，您可以儲存其建立範本和參數的複本。 如果您在建立主要叢集時儲存此資訊，您可以使用它來建立具有相同屬性的替代叢集。 在 [[摘要](avere-vfxt-deploy.md#validation-and-purchase)] 頁面上，按一下 [**下載範本和參數**] 連結。 建立叢集之前，請先將資訊儲存至檔案。

1. 新增指向重複 Blob 容器的新雲端核心檔案管理工具。

   請務必指定目標容器已包含 [核心檔案管理工具建立嚮導] 的 [值區**內容**] 設定中的資料。 （如果您不小心將此設為**空白**，系統應該會向您發出警示）。  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. 如有必要，請更新用戶端，使其掛接新叢集或新的核心檔案管理工具，而不是原始的。 （如果您使用與原始容器相同的名稱和連接路徑來新增取代核心檔案管理工具，就不需要更新用戶端進程，除非您需要在新的 IP 位址掛接新叢集）。

## <a name="next-steps"></a>後續步驟

* 如需自訂 Avere vFXT for Azure 設定的詳細資訊，請參閱叢集[調整](avere-vfxt-tuning.md)。
* 深入瞭解嚴重損壞修復和在 Azure 中建立具復原功能的應用程式：

  * [Azure 復原技術指導](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [從全區域服務中斷復原](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Azure 應用程式的災害復原和高可用性](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
