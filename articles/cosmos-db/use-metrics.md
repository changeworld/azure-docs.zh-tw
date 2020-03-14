---
title: 使用 Azure Cosmos DB 中的計量進行監視及偵錯
description: 使用 Azure Cosmos DB 中的計量偵錯常見問題及監控資料庫。
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 06/18/2019
ms.reviewer: sngun
ms.openlocfilehash: ef457fe8c21bc7e62f910a78913069df32bea1a3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246457"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的計量進行監視及偵錯

Azure Cosmos DB 為輸送量、儲存體、一致性、可用性和延遲提供計量。 Azure 入口網站會提供這些計量的彙總檢視。 您也可以從 Azure 監視器 API 檢視 Azure Cosmos DB 計量。 若要瞭解如何從 Azure 監視器中查看計量，請參閱[從 Azure 監視器取得計量](cosmos-db-azure-monitor-metrics.md)一文。 

本文將逐步解說常見的使用案例，以及如何使用 Azure Cosmos DB 計量來分析和偵錯這些問題。 系統每隔五分鐘就會收集計量一次並保留七天。

## <a name="view-metrics-from-azure-portal"></a>從 Azure 入口網站查看計量

1. 登入 [Azure 入口網站](https://portal.azure.com/)

1. 開啟 [**計量**] 窗格。 根據預設，[計量] 窗格會顯示 Azure Cosmos 帳戶中所有資料庫的儲存體、索引、要求單位計量。 您可以針對每個資料庫、容器或區域篩選這些計量。 您也可以在特定時間細微性篩選計量。 如需輸送量、儲存體、可用性、延遲和一致性計量的詳細資訊，請在個別的索引標籤上提供。 

   ![Azure 入口網站中的 Cosmos DB 效能計量](./media/use-metrics/performance-metrics.png)

下列計量可從 [**計量**] 窗格取得： 

* **輸送量計量**-此度量會顯示已取用或失敗的要求數目（429回應碼），因為已超過為容器布建的輸送量或儲存體容量。

* **儲存體計量**-此度量會顯示資料大小和索引使用方式。

* **可用性計量**-此度量會顯示每小時要求總數的成功要求百分比。 成功率是由 Azure Cosmos DB Sla 所定義。

* **延遲計量**-此度量會顯示您的帳戶運作所在區域中 Azure Cosmos DB 所觀察到的讀取和寫入延遲。 您可以針對異地複寫帳戶，以視覺化方式跨區域的延遲。 此度量不代表端對端要求延遲。

* **一致性計量**-此度量會顯示您所選擇的一致性模型之間的一致性。 針對多重區域帳戶，此計量也會顯示您所選取區域之間的複寫延遲。

* **系統計量**-此度量會顯示主要分割區提供多少中繼資料要求。 它也有助於識別已節流的要求。

下列各節說明您可以使用 Azure Cosmos DB 計量的常見案例。 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>了解有多少要求成功或導致錯誤

若要開始，請前往 [Azure 入口網站](https://portal.azure.com)，並瀏覽至 [計量] 刀鋒視窗。 在分頁中，尋找 [每1分鐘的要求數目超過容量] 圖表。 此圖表依狀態碼分段顯示每分鐘的要求總數。 如需 HTTP 狀態碼的詳細資訊，請參閱 [Azure Cosmos DB 的 HTTP 狀態碼](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) \(英文\)。

最常見的錯誤狀態碼是 429 (速率限制/節流)。 此錯誤表示對 Azure Cosmos DB 的要求數目多於佈建的輸送量。 此問題最常見的解決方案是為指定的集合[擴大 RU](./set-throughput.md)。

![每分鐘的要求數目](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>判斷所有磁碟分割中的輸送量分佈

良好的磁碟分割索引鍵基數對於任何種類的可調整應用程式都很重要。 若要判斷任何分割容器的輸送量分佈且能細分至分割區，請瀏覽至 [Azure 入口網站](https://portal.azure.com)中的 [計量] 刀鋒視窗。 在 [輸送量] 索引標籤上，儲存體細目顯示在**每個實體磁碟分割每秒使用的最多 RU** 圖表中。 下圖說明資料分佈不佳的範例，如最左側有誤差的磁碟分割所示。

![在下午 3:05 使用量飆升的單一磁碟分割](media/use-metrics/metrics-17.png)

輸送量分佈不平均可能會造成磁碟分割過度使用，這可能會導致節流要求，而且可能需要重新分割磁碟。 如需有關在 Azure Cosmos DB 中進行磁碟分割的詳細資訊，請參閱[在 Azure Cosmos DB 中進行磁碟分割和調整](./partition-data.md)。

## <a name="determine-the-storage-distribution-across-partitions"></a>判斷所有磁碟分割中的儲存體分佈

良好的磁碟分割基數對於任何種類的可調整應用程式都很重要。 若要判斷任何分割容器的儲存體分佈且能細分至分割區，請前往 [Azure 入口網站](https://portal.azure.com)中的 [計量] 刀鋒視窗。 在 [儲存體] 索引標籤中，儲存體分佈顯示在前幾大分割區索引鍵圖表使用的「資料 + 索引」儲存體。 下圖說明資料儲存體分佈不佳的情況，如最左側有誤差的磁碟分割所示。

![資料分佈不佳的範例](media/use-metrics/metrics-07.png)

按一下圖表上的磁碟分割，可以找出導致資料分佈扭曲的磁碟分割索引鍵。

![磁碟分割索引鍵會讓資料分佈扭曲](media/use-metrics/metrics-05.png)

找出導致資料分佈扭曲的分割區索引鍵之後，便可能必須用更分散的分割區索引鍵來重新分割容器。 如需有關在 Azure Cosmos DB 中進行磁碟分割的詳細資訊，請參閱[在 Azure Cosmos DB 中進行磁碟分割和調整](./partition-data.md)。

## <a name="compare-data-size-against-index-size"></a>比較資料大小與索引大小

在 Azure Cosmos DB 中，已使用的總儲存體空間為資料大小和索引大小的加總。 一般來說，索引大小只佔資料大小的一小部分。 在 [Azure 入口網站](https://portal.azure.com)的 [計量] 刀鋒視窗中，[儲存體] 索引標籤顯示資料和索引使用之儲存體空間的細目。

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

如果要節省索引空間，可以調整[編製索引原則](index-policy.md)。

## <a name="debug-why-queries-are-running-slow"></a>偵錯查詢執行速度很慢的原因

在 SQL API SDK 中，Azure Cosmos DB 會提供查詢執行的統計資料。

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* 提供查詢中每個元件的執行時間長度詳細資料。 長時間執行查詢的最常見根本原因是掃描，這表示查詢無法利用索引。 使用較好的篩選條件，就可以解決這個問題。

## <a name="next-steps"></a>後續步驟

您現在已了解如何使用 Azure 入口網站中提供的計量來監視和偵錯問題。 若要深入了解如何提升資料庫效能，請閱讀下列文章：

* 若要瞭解如何從 Azure 監視器中查看計量，請參閱[從 Azure 監視器取得計量](cosmos-db-azure-monitor-metrics.md)一文。 
* [Azure Cosmos DB 的效能和規模測試](performance-testing.md)
* [Azure Cosmos DB 的效能秘訣](performance-tips.md)
