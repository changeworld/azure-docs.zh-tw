---
title: Azure Avere vFXT 的災害復原指南
description: 如何保護 Azure Avere vFXT 中的資料免受意外刪除或中斷
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966656"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Azure Avere vFXT 的災害復原指南

本文概述了為 Azure 工作流保護 Avere vFXT 的策略，並提供有關備份資料以便從事故或中斷中恢復的指導。

Azure 的 Avere vFXT 臨時將資料存儲在其緩存中。 資料存儲在後端存儲系統中長期存儲 - 本地硬體系統、Azure Blob 存儲容器或兩者。

為防止中斷和可能的資料丟失，請考慮以下四個方面：

* 如果 Azure 系統的 Avere vFXT 不可用，防止停機
* 保護群集緩存中的資料
* 保護後端 NAS 硬體存儲中的資料
* 保護後端 Azure Blob 雲存儲中的資料

Azure 客戶的每個 Avere vFXT 都必須創建自己的全面災害復原計畫，其中包括這些專案的計畫。 您還可以將彈性構建到與 vFXT 群集一起使用的應用程式中。 閱讀["後續步驟"](#next-steps)中的連結以尋求説明。

## <a name="protect-against-downtime"></a>防止停機

冗余內置到 Azure 產品的 Avere vFXT 中：

* 群集高度可用，單個叢集節點可以以最小的中斷故障進行容錯移轉。
* 緩存中更改的資料定期寫入後端核心檔案伺服器（硬體 NAS 或 Azure Blob），以便進行長期存儲。

Azure 群集的每個 Avere vFXT 都必須位於單個可用性區域中，但您可以使用位於不同區域或不同區域的冗余群集，在區域中斷時快速提供訪問。

如果您擔心無法訪問資料，還可以將存儲容器放置在多個區域中。 但是，請記住，區域之間的事務比留在區域內的事務具有更高的延遲和更高的成本。

## <a name="protect-data-in-the-cluster-cache"></a>保護群集緩存中的資料

緩存的資料在定期關閉之前始終寫入核心檔案伺服器，但在不受控制的關機中，緩存中更改的資料可能會丟失。

如果僅使用群集優化檔讀取，則不會丟失任何更改。 如果還使用群集快取檔案更改（寫入），請考慮是否調整核心檔檔的[緩存策略](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)<!-- link to legacy doc --> 自訂將資料寫入長期存儲的頻率。

通常，您的恢復計畫應側重于備份後端存儲系統，這些存儲系統包含更多資料，並且通常對於故障後重新建立工作流更為重要。

## <a name="protect-data-in-nas-core-filers"></a>保護 NAS 核心檔案伺服器中的資料

使用接受的方法保護存儲在本地 NAS 硬體核心檔程式中的資料，包括 NAS 提供程式建議的快照和完整備份。 這些核心檔案伺服器的災害復原超出了本文的範圍。

## <a name="protect-data-in-azure-blob-storage"></a>保護 Azure Blob 存儲中的資料

Azure 的 Avere vFXT 使用 Azure Blob 核心檔案伺服器的本地冗余存儲 （LRS）。 這意味著自動複製 Blob 容器中的資料，以防止資料中心內的瞬態硬體故障。

本節提供有關如何進一步保護 Blob 存儲中的資料免受罕見區域範圍中斷或意外刪除的提示。

保護 Azure Blob 存儲中資料的最佳做法包括：

* 經常將關鍵資料複製到另一個區域的另一個存儲帳戶（通常由災害復原計畫確定）。
* 控制對所有目標系統上的資料的訪問，以防止意外刪除或損壞。 請考慮在資料存儲上使用[資源鎖](../azure-resource-manager/management/lock-resources.md)。
* 為 Blob 核心檔案伺服器啟用適用于 Azure[雲快照](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>)功能的 Avere vFXT。

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>將 Avere vFXT 核心檔程式資料複製到備份帳戶

按照以下步驟在另一個帳戶中建立資料備份。

1. 如果需要，生成新的加密金鑰並將其安全地存儲在受影響的系統之外。

   如果資料由 Azure 群集的 Avere vFXT 加密，則應在將資料複製到其他存儲帳戶之前生成新的加密金鑰。 將該金鑰和密碼安全地存儲在安全且不會受區域故障影響的設施中。

   將容器添加到群集時必須提供此金鑰 ，即使您正在將其重新添加到其原始群集。

   讀取[雲加密設定](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> 瞭解詳細資訊。

   如果容器僅使用 Azure 的內置加密，則可以跳過此步驟。

1. 從系統中刪除核心檔案伺服器。 這強制群集將所有更改的資料寫入後端存儲。

   儘管您必須在備份後重新添加核心檔程式，但刪除它是確保所有資料完全寫入後端的最佳方式。 （"掛起"選項有時會將更改的資料保留在緩存中。 <!-- xxx true? or just metadata? -->

   記下核心檔程式的名稱和交匯點資訊（列在控制台中的**命名空間**頁上），以便在備份後重新添加容器時複製該資訊。

   使用群集控制台刪除核心檔程式。 [打開群集控制台](avere-vfxt-cluster-gui.md)並選擇**核心檔案伺服器** > **管理核心檔程式**。 查找要備份的存儲系統，並使用 **"刪除**"按鈕將其從群集中刪除。

1. 在另一個區域中的另一個存儲帳戶中創建新的空 Blob 存儲容器。

1. 使用任何方便的複製工具將核心檔案伺服器上的資料複製到新容器。 副本必須在不更改的情況下複製資料，並且不會中斷 Avere vFXT 為 Azure 使用的專有雲檔案系統格式。 基於 Azure 的工具組括[AzCopy、Azure](../storage/common/storage-use-azcopy-v10.md) [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)和[Azure 資料工廠](../data-factory/connector-azure-data-lake-store.md)。

1. 將資料複製到備份容器後，將原始容器添加回群集，如[配置存儲](avere-vfxt-add-storage.md)中所述。

   * 使用相同的核心檔器名稱和交匯點資訊，以便用戶端工作流無需更改。
   * 將**存儲桶內容**值設置為現有資料選項。
   * 如果容器由群集加密，則必須為其內容輸入當前加密金鑰。 （這是您在步驟一中更新的金鑰。

對於第一個備份之後的備份，不需要創建新的存儲容器。 但是，請考慮在每次執行備份時生成新的加密金鑰，以確保將當前金鑰存儲在您記得的位置。

### <a name="access-a-backup-data-source-during-an-outage"></a>在中斷期間訪問備份資料來源

要從 Azure 群集的 Avere vFXT 訪問備份容器，請按照此過程操作：

1. 如果需要，請為未受影響的區域中的 Azure 群集創建新的 Avere vFXT。

   > [!TIP]
   > 為 Azure 群集創建 Avere vFXT 時，可以保存其創建範本和參數的副本。 如果在創建主群集時保存此資訊，則可以使用它創建具有相同屬性的替換群集。 在[摘要](avere-vfxt-deploy.md#validation-and-purchase)頁上，按一下 **"下載"範本和參數**連結。 在創建群集之前，將資訊保存到檔中。

1. 添加新的雲核心檔器，指向重複的 Blob 容器。

   請確保指定目標容器已在核心檔程式創建嚮導的 Bucket**內容**設置中包含資料。 （如果您不小心將此集保留為 **"空**"，系統應提醒您。  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. 如有必要，更新用戶端，以便它們裝載新群集或新核心檔程式，而不是原始檔案。 （如果添加與原始容器相同的名稱和交匯點路徑的替換核心檔程式，則無需更新用戶端進程，除非您需要在新的 IP 位址裝載新群集。

## <a name="next-steps"></a>後續步驟

* 有關為 Azure 自訂 Avere vFXT 設置的詳細資訊，請閱讀[群集調優](avere-vfxt-tuning.md)。
* 瞭解有關 Azure 中災害復原和構建彈性應用程式的更多資訊：

  * [Azure 復原技術指導](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [從全區域服務中斷復原](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Azure 應用程式的災害復原和高可用性](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
