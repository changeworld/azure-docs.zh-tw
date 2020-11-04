---
title: Azure Data Lake Storage 的進階層 |Microsoft Docs
description: 搭配 Azure Data Lake Storage Gen2 使用 premium 效能層級
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/30/2020
ms.author: normesta
ms.openlocfilehash: be440407fb6f4d9715ba80c584af023a5f662394
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324218"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage 的進階層

Azure Data Lake Storage Gen2 現在支援 [premium 效能層級](storage-blob-performance-tiers.md#premium-performance)。 Premium 效能層級適用于需要低一致性延遲且擁有大量交易的大型資料分析應用程式和工作負載。

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>可受益于 premium 效能層的工作負載

範例工作負載包括互動式工作負載、IoT、串流分析、人工智慧和機器學習。 

**互動式工作負載** 

這些工作負載需要立即更新和使用者意見反應，例如電子商務和地圖應用程式、互動式影片應用程式等。例如，在電子商務應用程式中，可能不會快取較少的經常流覽專案。 不過，它們必須立即隨選向客戶顯示。 另一個範例是，資料科學家、分析師和開發人員可以藉由對使用 premium 效能層級的帳戶中所儲存的資料執行查詢，以更快的速度衍生時間緊迫的見解。 

**IoT/串流分析** 

在 IoT 案例中，有許多較小的寫入作業可能會每秒推送至雲端。 大量資料可能會內嵌、匯總以供分析之用，然後幾乎立即刪除。 Premium 效能層級的高內嵌功能讓這種類型的工作負載更有效率。 

**人工智慧/機器學習 (AI/ML)** 

AI/ML 會處理不同資料類型的耗用量和處理，例如視覺效果、語音和文字。 這種高效能運算類型的工作負載處理大量資料，需要快速回應和有效率的資料分析內嵌時間。 

## <a name="cost-effectiveness"></a>符合成本效益

Premium 效能層級的儲存體成本較高，但相較于標準效能層級的交易成本較低。 如果您的應用程式和工作負載執行大量的交易，premium 效能層級可能會符合成本效益。

下表示范 Azure Data Lake Storage 進階層的成本效益。 每個資料行都代表一個月的交易數目。  每個資料列都代表讀取交易的交易百分比。 資料表中的每個資料格都會顯示與讀取交易百分比和執行的交易數目相關聯的成本降低百分比。 

例如，假設您的帳戶位於美國東部2區域，則您帳戶的交易數目超過90M，而這些交易的70% 是讀取交易，高階效能層級更符合成本效益。

> [!div class="mx-imgBorder"]
> ![影像在此](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> 如果您想要根據每一 TB 資料的每秒交易數來評估成本效益，可以使用出現在資料表底部的資料行標題。

如需定價的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 定價](https://azure.microsoft.com/pricing/details/storage/data-lake/) 頁面。

## <a name="feature-availability"></a>功能可用性 

某些 Blob 儲存體功能可能無法使用，或可能只對 premium 效能層有部分支援。 如需完整清單，請參閱 [Azure Data Lake Storage Gen2 中提供的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)。 然後，請查看 [已知問題](data-lake-storage-known-issues.md) 清單，以評估任何功能中的差距。

## <a name="enabling-the-premium-performance-tier"></a>啟用 premium 效能層級 

您可以藉由建立 **已啟用****階層命名空間** 設定的 BlockBlobStorage 帳戶，將進階層用於 Azure Data Lake Storage。 如需完整的指引，請參閱 [建立 BlockBlobStorage 帳戶](storage-blob-create-account-block-blob.md) 帳戶。

當您建立帳戶時，請務必選擇 **Premium** performance 選項和 **BlockBlobStorage** 帳戶種類。

> [!div class="mx-imgBorder"]
> ![建立 blockblobstorageacount](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

在 [ **建立儲存體帳戶** **] 頁面的 [** 設定] 索引標籤中，啟用階層式 **命名空間** 設定。 當您建立帳戶時，必須啟用此設定。 您之後無法啟用它。

下圖顯示 [ **建立儲存體帳戶** ] 頁面中的這項設定。

> [!div class="mx-imgBorder"]
> ![階層命名空間設定](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>區域可用性

以下區域提供適用于 Azure Data Lake Storage 的進階層。

|區域|備援|
|--|--|
|美國東部|LRS、ZRS|
|美國東部 2|LRS、ZRS|
|美國中部|LRS|
|美國西部|LRS|
|美國西部 2|LRS、ZRS|
|美國中西部|LRS|
|美國中南部|LRS|
|加拿大中部|LRS|
|加拿大東部|LRS|
|歐洲北部|LRS、ZRS|
|西歐|LRS、ZRS|
|英國南部|LRS|
|英國西部|LRS|
|法國中部|LRS|
|東亞|LRS|
|南韓中部|LRS|
|南韓南部|LRS|
|印度中部|LRS|
|印度西部|LRS|
|阿拉伯聯合大公國北部|LRS|
|日本東部|LRS|
|日本西部|LRS|
|東南亞|LRS、ZRS|
|澳大利亞東部|LRS、ZRS|
|澳大利亞東南部|LRS|
|巴西南部|LRS|

## <a name="next-steps"></a>後續步驟

使用進階層進行 Azure Data Lake Storage 與您最愛的分析服務，例如 Azure Databricks、Azure HDInsight 和 Azure Synapse Analytics。 

- [教學課程：Azure Data Lake Storage Gen2、Azure Databricks 和 Spark](data-lake-storage-use-databricks-spark.md) 
- [使用 Azure Data Lake Storage Gen2 搭配 Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) 叢集HDInsight 目前支援使用 premium 效能層級的帳戶，以及已啟用加速寫入的 HBase 叢集。
- [快速入門：建立 Synapse 工作區](../../synapse-analytics/quickstart-create-workspace.md)

