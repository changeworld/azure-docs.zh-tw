---
title: 查詢 Azure Cosmos DB 中的容器
description: 瞭解如何使用資料分割和跨資料分割查詢，在 Azure Cosmos DB 中查詢容器
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: e948031d3d1d03890bfcfccd65424a15e6e314cd
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276113"
---
# <a name="query-an-azure-cosmos-container"></a>查詢 Azure Cosmos 容器

本文說明如何查詢 Azure Cosmos DB 中的容器 (集合、圖表或資料表) 的輸送量。 尤其是，它涵蓋了分割區和跨資料分割查詢在 Azure Cosmos DB 中的運作方式。

## <a name="in-partition-query"></a>分割區內查詢

當您從容器查詢資料時，如果查詢已指定分割區索引鍵篩選，Azure Cosmos DB 會自動優化查詢。 它會將查詢路由至對應至篩選中指定之分割區索引鍵值的 [實體](partitioning-overview.md#physical-partitions) 分割區。

例如，請考慮下列使用相等篩選的查詢 `DeviceId` 。 如果我們在分割的容器上執行此查詢 `DeviceId` ，此查詢會篩選到單一實體資料分割。

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

如同先前的範例，此查詢也會篩選成單一資料分割。 將其他篩選準則加入時， `Location` 不會變更此變更：

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

以下是在分割區索引鍵上具有範圍篩選的查詢，且不會設定為單一實體分割區的範圍。 若要成為分割區內查詢，查詢必須有包含分割區索引鍵的等號比較篩選準則：

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>跨分割區查詢

下列查詢沒有分割區索引鍵 () 的篩選 `DeviceId` 。 因此，它必須在針對每個資料分割的索引執行的所有實體分割區上展開：

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

每個實體分割區都有自己的索引。 因此，當您在容器上執行跨分割區查詢時， *每個* 實體分割區都能有效執行一個查詢。 Azure Cosmos DB 會自動將結果匯總到不同的實體分割區。

不同實體分割區中的索引是彼此獨立的。 Azure Cosmos DB 中沒有全域索引。

## <a name="parallel-cross-partition-query"></a>跨分割區的平行查詢

Azure Cosmos DB SDK 1.9.0 和更新版本支援平行查詢執行選項。 跨分割區的平行查詢可讓您執行低延遲的跨分割區查詢。

若要管理平行執行查詢，您可以調整下列參數︰

- **>maxconcurrency**：設定容器磁碟分割的同時網路連接數目上限。 如果您將此屬性設定為 `-1` ，則 SDK 會管理平行處理原則的程度。 如果  `MaxConcurrency` 設定為 `0` ，則會有與容器分割區的單一網路連接。

- **MaxBufferedItemCount**：權衡取捨查詢延遲性和用戶端記憶體使用量。 如果省略此選項或將其設定為 -1，則 SDK 會管理在平行查詢執行期間緩衝處理的項目數。

由於 Azure Cosmos DB 平行處理跨資料分割查詢的能力，因此查詢延遲通常會隨著系統新增 [實體](partitioning-overview.md#physical-partitions)分割區而調整。 不過，當實體分割區總數增加時，RU 費用會大幅增加。

當您執行跨分割區查詢時，基本上是針對個別的實體分割區執行個別的查詢。 雖然跨資料分割查詢查詢將會使用索引（如果有的話），但它們仍不如資料分割內查詢有效率。

## <a name="useful-example"></a>實用範例

以下是深入瞭解跨資料分割查詢的類比：

假設您是一個傳遞驅動程式，必須將套件傳遞給不同的單元 complexes。 每個單元都有複雜的內部部署清單，其中包含所有常駐的單位編號。 我們可以將每個單元的複雜部分與實體分割區和每個清單進行比較。

我們可以使用此範例來比較分割區和跨資料分割的查詢：

### <a name="in-partition-query"></a>分割區內查詢

如果傳遞驅動程式知道正確的單元複雜 (實體分割區) ，則它們可以立即驅動至正確的建築物。 驅動程式可以在索引) 中，檢查公寓的 (常駐單元號碼清單，並快速地傳遞適當的封裝。 在這種情況下，驅動程式不會浪費任何時間或精力駕駛到一段複雜時間來檢查，看看是否有任何套件收件者存在。

### <a name="cross-partition-query-fan-out"></a>跨分割區查詢 (展開) 

如果傳遞驅動程式不知道正確的單元複雜 (實體分割區) ，它們必須磁片磁碟機到每個單一的公寓大樓，並檢查清單中的所有常駐單元編號 (索引) 。 一旦每個單元都達到複雜的情況，他們仍然可以使用每個居民的地址清單。 不過，他們將需要檢查每個單元複雜的清單，是否有任何套件收件者在那裡。 這是跨分割區查詢的運作方式。 雖然它們可以使用索引 (不需要在每個大門) 上挖下，但它們必須個別檢查每個實體資料分割的索引。

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>跨分割區查詢 (範圍僅限於少數實體資料分割) 

如果傳遞驅動程式知道所有套件收件者都在特定的公寓 complexes 內，則不需要磁片磁碟機到每一個。 雖然驅動到少數的單元 complexes，仍需要比僅造訪單一建築更多的工作，但交貨驅動程式仍能節省大量的時間和精力。 如果查詢的資料分割索引鍵在其篩選中使用 `IN` 關鍵字，它只會檢查相關實體資料分割的資料索引。

## <a name="avoiding-cross-partition-queries"></a>避免跨分割區查詢

對於大部分的容器而言，您將會有一些跨資料分割的查詢是不可避免的。 有一些跨分割區查詢是正常的！ 幾乎所有的資料分割都支援所有的查詢作業， (邏輯分割區索引鍵和實體分割區) 。 在查詢引擎和用戶端 Sdk 中，Azure Cosmos DB 也有許多優化，可跨實體資料分割平行執行查詢。

在大部分的大量讀取案例中，我們建議您只在查詢篩選器中選取最常見的屬性。 您也應該確定您的資料分割索引鍵符合其他資料 [分割索引鍵選取的最佳作法](partitioning-overview.md#choose-partitionkey)。

避免跨分割區查詢通常只對大型容器相當重要。 當您檢查實體資料分割的索引是否有結果時，即使實體分割區中沒有任何專案符合查詢的篩選準則，您仍須支付大約 2.5 RU 的最小費用。 因此，如果您只有一個 (或只有幾個) 的實體分割區，跨分割區查詢將不會耗用比資料分割內查詢更大的 RU。

實體分割區的數目會系結至布建的 RU 數量。 每個實體分割區最多允許10000個布建的 RU，且最多可儲存 50 GB 的資料。 Azure Cosmos DB 將會自動為您管理實體資料分割。 您容器中的實體分割區數目取決於您布建的輸送量和已使用的儲存體。

如果您的工作負載符合下列準則，您應該嘗試避免跨分割區查詢：
- 您計畫布建超過 30000 RU
- 您計畫儲存超過 100 GB 的資料

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的資料分割：

- [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
- [Azure Cosmos DB 中的綜合分割區索引鍵](synthetic-partition-keys.md)
