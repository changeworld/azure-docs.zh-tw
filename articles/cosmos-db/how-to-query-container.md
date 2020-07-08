---
title: 查詢 Azure Cosmos DB 中的容器
description: 瞭解如何使用分割中和跨分割區查詢來查詢 Azure Cosmos DB 中的容器
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 08ac95fe2a6b3e01d6bbcf96b120426f12f4e21c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261251"
---
# <a name="query-an-azure-cosmos-container"></a>查詢 Azure Cosmos 容器

本文說明如何查詢 Azure Cosmos DB 中的容器 (集合、圖表或資料表) 的輸送量。 特別是，它會涵蓋資料分割和跨分割區查詢在 Azure Cosmos DB 中的使用方式。

## <a name="in-partition-query"></a>分割區內查詢

當您從容器查詢資料時，如果查詢已指定分割區索引鍵篩選，Azure Cosmos DB 會自動將查詢優化。 它會將查詢路由至對應到篩選中所指定之分割區索引鍵值的[實體](partition-data.md#physical-partitions)分割區。

例如，請考慮下列使用等號比較篩選準則的查詢 `DeviceId` 。 如果我們在資料分割所在的容器上執行此查詢 `DeviceId` ，此查詢將會篩選成單一實體分割區。

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

如同先前的範例，此查詢也會篩選到單一資料分割。 在上新增額外的篩選 `Location` 並不會變更它：

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

以下是具有資料分割索引鍵之範圍篩選的查詢，且不會限定為單一實體分割區。 若要成為資料分割內查詢，查詢必須具有包含分割區索引鍵的等號比較篩選準則：

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>跨分割區查詢

下列查詢沒有資料分割索引鍵（）的篩選 `DeviceId` 。 因此，它必須針對每個分割區的索引執行的所有實體分割區展開傳送：

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

每個實體分割區都有自己的索引。 因此，當您在容器上執行跨分割區查詢時，您實際上是*針對每個*實體分割區執行一個查詢。 Azure Cosmos DB 會在不同的實體分割區自動匯總結果。

不同實體分割區中的索引彼此獨立。 Azure Cosmos DB 中沒有全域索引。

## <a name="parallel-cross-partition-query"></a>跨分割區的平行查詢

Azure Cosmos DB SDK 1.9.0 和更新版本支援平行查詢執行選項。 跨分割區的平行查詢可讓您執行低延遲的跨分割區查詢。

若要管理平行執行查詢，您可以調整下列參數︰

- **MaxConcurrency**：設定與容器分割區的同時網路連線數目上限。 如果您將此屬性設定為，SDK 就會管理平行處理原則的 `-1` 程度。 如果  `MaxConcurrency` 設定為 `0` ，則會有一個連至容器分割區的單一網路連線。

- **MaxBufferedItemCount**：權衡取捨查詢延遲性和用戶端記憶體使用量。 如果省略此選項或將其設定為 -1，則 SDK 會管理在平行查詢執行期間緩衝處理的項目數。

由於 Azure Cosmos DB 的平行處理跨分割區查詢的能力，因此當系統新增[實體](partition-data.md#physical-partitions)資料分割時，查詢延遲通常會適當地調整。 不過，當實體分割區總數增加時，RU 費用會大幅增加。

當您執行跨分割區查詢時，基本上就是針對每個個別的實體分割區執行不同的查詢。 雖然跨分割區查詢查詢會使用索引（如果有的話），但它們的效率仍然不如資料分割中的查詢那麼有效率。

## <a name="useful-example"></a>有用的範例

以下是進一步瞭解跨分割區查詢的比喻：

假設您是一個交付驅動程式，必須將套件傳遞至不同的公寓 complexes。 每個單元都有一個內部部署清單，其中包含所有居民的單位編號。 我們可以比較每個單元的複雜和實體分割區，以及每個清單與實體分割區的索引。

我們可以使用此範例來比較資料分割和跨分割區查詢：

### <a name="in-partition-query"></a>分割區內查詢

如果傳遞驅動程式知道正確的單元複雜（實體分割區），則可以立即驅動至正確的建築物。 驅動程式可以檢查公寓的常駐單位編號（索引）清單，並快速傳遞適當的套件。 在這種情況下，驅動程式不會浪費任何時間或工作，以檢查並查看是否有任何套件收件者存在於該單元。

### <a name="cross-partition-query-fan-out"></a>跨分割區查詢（展開傳送）

如果傳遞驅動程式不知道正確的單元複雜（實體分割區），就必須將磁片磁碟機到每個單一公寓大樓，並檢查清單中是否有所有常駐的單位編號（索引）。 一旦他們抵達每個單元，他們仍然可以使用每個居民的地址清單。 不過，他們必須檢查每個公寓複雜的清單，不論是否有任何封裝收件者都存在。 這是跨分割區查詢的工作方式。 雖然它們可以使用索引（不需要在每個單一門上挖一次），但它們必須分別檢查每個實體分割區的索引。

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>跨分割區查詢（範圍僅限於幾個實體磁碟分割）

如果遞送驅動程式知道所有套件收件者都存留在某個特定的公寓 complexes 內，他們就不需要對每一個人進行驅動。 雖然駕駛至少數的公寓 complexes 仍然需要更多工作，而不只是造訪單一大樓，但遞送驅動程式仍然節省大量的時間和投入量。 如果查詢在其篩選中具有關鍵詞的分割區 `IN` 索引鍵，則只會檢查相關實體分割區的資料索引。

## <a name="avoiding-cross-partition-queries"></a>避免跨分割區查詢

對於大部分的容器而言，您會有一些跨分割區查詢是不可避免的。 有一些跨分割區查詢沒問題！ 幾乎所有的查詢作業都支援跨分割區（邏輯分割區索引鍵和實體磁碟分割）。 Azure Cosmos DB 在查詢引擎和用戶端 Sdk 中也有許多優化，可跨實體分割區平行執行查詢。

對於大部分的大量讀取案例，我們建議您只在查詢篩選中選取最常見的屬性。 您也應該確定您的資料分割索引鍵符合其他分割區索引[鍵選取的最佳做法](partitioning-overview.md#choose-partitionkey)。

避免跨分割區查詢通常只對大型容器很重要。 每次您檢查實體分割區的結果索引時，都會向您收取大約 2.5 RU 的費用，即使實體分割區中沒有任何專案符合查詢的篩選準則。 因此，如果您只有一個（或只是幾個）實體分割區，跨分割區查詢將不會耗用比資料分割內查詢更多的 RU。

實體分割區的數目會與已布建的 RU 數量相關聯。 每個實體分割區最多允許10000個布建的 RU，且最多可儲存 50 GB 的資料。 Azure Cosmos DB 會自動為您管理實體分割區。 容器中的實體分割區數目取決於您布建的輸送量和耗用的儲存體。

如果您的工作負載符合下列準則，您應該嘗試避免跨分割區查詢：
- 您計畫已布建超過 30000 RU
- 您計畫儲存超過 100 GB 的資料

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的資料分割：

- [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
- [Azure Cosmos DB 中的綜合分割區索引鍵](synthetic-partition-keys.md)
