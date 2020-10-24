---
title: 使用 Azure Cosmos DB 中的計量進行監視及偵錯
description: 使用 Azure Cosmos DB 中的計量偵錯常見問題及監控資料庫。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a7645950fd7a239376f07d6c6f4689c1a3f3da5
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476309"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的計量進行監視及偵錯

Azure Cosmos DB 為輸送量、儲存體、一致性、可用性和延遲提供計量。 Azure 入口網站會提供這些計量的彙總檢視。 您也可以從 Azure 監視器 API 檢視 Azure Cosmos DB 計量。 容器名稱等度量的維度值會區分大小寫。 因此，對這些維度值執行字串比較時，您必須使用不區分大小寫的比較。 若要瞭解如何從 Azure 監視器查看計量，請參閱「 [從 Azure 監視器取得度量](./monitor-cosmos-db.md) 」一文。

本文將逐步解說常見的使用案例，以及如何使用 Azure Cosmos DB 計量來分析和偵錯這些問題。 系統每隔五分鐘就會收集計量一次並保留七天。

## <a name="view-metrics-from-azure-portal"></a>從 Azure 入口網站查看計量

1. 登入 [Azure 入口網站](https://portal.azure.com/)

1. 開啟 [ **計量** ] 窗格。 根據預設，[計量] 窗格會顯示 Azure Cosmos 帳戶中所有資料庫的儲存體、索引、要求單位度量。 您可以針對每個資料庫、容器或區域篩選這些計量。 您也可以在特定的時間細微性篩選計量。 輸送量、儲存體、可用性、延遲和一致性計量的詳細資料會在不同的索引標籤上提供。 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Azure 入口網站中的 Cosmos DB 效能度量":::

[ **計量** ] 窗格提供下列計量： 

* **輸送量計量** -此計量會顯示因容器所布建的輸送量或儲存體容量超過的 (429 回應碼) 所耗用或失敗的要求數目。

* **儲存體計量** -此計量會顯示資料和索引使用方式的大小。

* **可用性計量** -此度量會顯示每小時要求總數的成功要求百分比。 成功率是由 Azure Cosmos DB Sla 所定義。

* **延遲計量** -此計量會顯示您的帳戶運作所在區域 Azure Cosmos DB 所觀察到的讀取和寫入延遲。 您可以針對異地複寫的帳戶，將不同區域的延遲視覺化。 此度量不代表端對端要求延遲。

* **一致性計量** -此計量會顯示您所選擇之一致性模型的最終一致性。 針對多重區域帳戶，此計量也會顯示您所選取區域之間的複寫延遲。

* **系統計量** -此計量會顯示主要磁碟分割所提供的中繼資料要求數目。 它也有助於識別節流的要求。

下列各節說明您可以使用 Azure Cosmos DB 計量的一般案例。 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>了解有多少要求成功或導致錯誤

若要開始，請前往 [Azure 入口網站](https://portal.azure.com)，並瀏覽至 [計量]**** 刀鋒視窗。 在分頁中，找出 [每1分鐘的要求數目超過容量] 圖表。 此圖表依狀態碼分段顯示每分鐘的要求總數。 如需有關 HTTP 狀態碼的詳細資訊，請參閱 [Azure Cosmos DB 的 HTTP 狀態碼](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)。

最常見的錯誤狀態碼是 429 (速率限制/節流)。 此錯誤表示對 Azure Cosmos DB 的要求數目多於佈建的輸送量。 此問題最常見的解決方案是為指定的集合[相應增加 RU](./set-throughput.md)。

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Azure 入口網站中的 Cosmos DB 效能度量":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>判斷所有磁碟分割中的輸送量分佈

良好的磁碟分割索引鍵基數對於任何種類的可調整應用程式都很重要。 若要判斷任何分割容器的輸送量分佈且能細分至分割區，請瀏覽至 [Azure 入口網站](https://portal.azure.com)中的 [計量]**** 刀鋒視窗。 在 [輸送量]**** 索引標籤上，儲存體細目顯示在**每個實體磁碟分割每秒使用的最多 RU** 圖表中。 下圖說明資料分佈不佳的範例，如最左側有誤差的磁碟分割所示。

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Azure 入口網站中的 Cosmos DB 效能度量":::

輸送量分佈不平均可能會造成磁碟分割過度使用**，這可能會導致節流要求，而且可能需要重新分割磁碟。 如需有關在 Azure Cosmos DB 中進行磁碟分割的詳細資訊，請參閱[在 Azure Cosmos DB 中進行磁碟分割和調整](./partitioning-overview.md)。

## <a name="determine-the-storage-distribution-across-partitions"></a>判斷所有磁碟分割中的儲存體分佈

良好的磁碟分割基數對於任何種類的可調整應用程式都很重要。 若要判斷任何分割容器的儲存體分佈且能細分至分割區，請前往 [Azure 入口網站](https://portal.azure.com)中的 [計量] 刀鋒視窗。 在 [儲存體] 索引標籤中，儲存體分佈顯示在前幾大分割區索引鍵圖表使用的「資料 + 索引」儲存體。 下圖說明資料儲存體分佈不佳的情況，如最左側有誤差的磁碟分割所示。

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Azure 入口網站中的 Cosmos DB 效能度量":::

按一下圖表上的磁碟分割，可以找出導致資料分佈扭曲的磁碟分割索引鍵。

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="Azure 入口網站中的 Cosmos DB 效能度量":::

找出導致資料分佈扭曲的分割區索引鍵之後，便可能必須用更分散的分割區索引鍵來重新分割容器。 如需有關在 Azure Cosmos DB 中進行磁碟分割的詳細資訊，請參閱[在 Azure Cosmos DB 中進行磁碟分割和調整](./partitioning-overview.md)。

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

* 若要瞭解如何從 Azure 監視器查看計量，請參閱「 [從 Azure 監視器取得度量](./monitor-cosmos-db.md) 」一文。 
* [Azure Cosmos DB 的效能和規模測試](performance-testing.md)
* [Azure Cosmos DB 的效能秘訣](performance-tips.md)