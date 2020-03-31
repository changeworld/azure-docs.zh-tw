---
title: 查詢 Azure Cosmos DB 中的容器
description: 瞭解如何使用分區內和跨分區查詢查詢 Azure Cosmos DB 中的容器
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131403"
---
# <a name="query-an-azure-cosmos-container"></a>查詢 Azure Cosmos 容器

本文說明如何查詢 Azure Cosmos DB 中的容器 (集合、圖表或資料表) 的輸送量。 特別是，它涵蓋了分區內查詢和跨分區查詢在 Azure Cosmos DB 中的工作方式。

## <a name="in-partition-query"></a>分割區內查詢

從容器查詢資料時，如果查詢指定了分區金鑰篩選器，Azure Cosmos DB 將自動優化查詢。 它將查詢路由到與篩選器中指定的分區鍵值對應[的物理分區](partition-data.md#physical-partitions)。

例如，請考慮以下查詢，在 上`DeviceId`使用相等性篩選器。 如果我們在分區`DeviceId`的容器上運行此查詢，則此查詢將篩選為單個物理分區。

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

與前面的示例一樣，此查詢還將篩選到單個分區。 添加附加篩選器`Location`不會更改此功能：

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

下面是一個查詢，該查詢在分區鍵上具有範圍篩選器，並且不會限定為單個物理分區。 為了成為分區內查詢，查詢必須具有包含分區鍵的相等篩選器：

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>跨分割區查詢

以下查詢在分區鍵 （）`DeviceId`上沒有篩選器。 因此，它必須扇出到針對每個分區的索引運行的所有物理分區：

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

每個物理分區都有自己的索引。 因此，當您在容器上運行跨分區查詢時，*您實際上正在每個*物理分區運行一個查詢。 Azure Cosmos DB 將自動聚合不同物理分區的結果。

不同物理分區中的索引彼此獨立。 Azure 宇宙 DB 中沒有全域索引。

## <a name="parallel-cross-partition-query"></a>跨分割區的平行查詢

Azure Cosmos DB SDK 1.9.0 和更新版本支援平行查詢執行選項。 跨分割區的平行查詢可讓您執行低延遲的跨分割區查詢。

若要管理平行執行查詢，您可以調整下列參數︰

- **MaxConcurrency：** 設置與容器分區同時連接的最大數量。 如果將此屬性設置為`-1`，SDK 將管理並行性的程度。 如果 `MaxConcurrency`設置為`0`，則與容器的分區有一個網路連接。

- **MaxBufferedItemCount**：權衡取捨查詢延遲性和用戶端記憶體使用量。 如果省略此選項或將其設定為 -1，則 SDK 會管理在平行查詢執行期間緩衝處理的項目數。

由於 Azure Cosmos DB 能夠並行化跨分區查詢，因此查詢延遲通常會隨著系統添加[物理分區](partition-data.md#physical-partitions)而擴展。 但是，RU 電荷將顯著增加，因為物理分區的總數會增加。

運行跨分區查詢時，實質上是針對單個物理分區執行單獨的查詢。 雖然跨分區查詢將使用索引（如果可用），但它們仍然不如分區內查詢有效。

## <a name="useful-example"></a>有用的示例

下面是一個類比，以更好地瞭解跨分區查詢：

假設您是一個遞送司機，必須將包裹運送到不同的公寓大樓。 每個公寓大樓都有一份包含居民單位號碼的房舍清單。 我們可以將每個公寓複合體與物理分區進行比較，並將每個清單與物理分區的索引進行比較。

我們可以使用此示例比較分區內查詢和跨分區查詢：

### <a name="in-partition-query"></a>分割區內查詢

如果送貨司機知道正確的公寓大樓（物理分區），那麼他們就可以立即開車到正確的建築物。 駕駛員可以檢查公寓大樓的居民單位編號（索引）清單，並快速交付相應的包裹。 在這種情況下，司機不會浪費任何時間和精力開車到公寓大樓檢查，看看是否有任何包裹收件者住在那裡。

### <a name="cross-partition-query-fan-out"></a>跨分區查詢（扇出）

如果送貨司機不知道正確的公寓大樓（物理分區），他們需要開車到每一棟公寓樓，並檢查清單與居民的所有單位編號（索引）。 一旦他們到達每個公寓大樓，他們仍然可以使用每個居民的地址清單。 但是，他們需要檢查每個公寓大樓的清單，是否有任何包裹收件者住在那裡。 這是跨分區查詢的工作原理。 雖然他們可以使用索引（不需要敲每扇門），但他們必須單獨檢查每個物理分區的索引。

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>跨分區查詢（範圍僅限於幾個物理分區）

如果傳遞驅動程式知道所有包裹收件者都住在某個公寓樓內，他們不需要開車到每個公寓。 雖然開車到幾個公寓大樓仍然需要更多的工作，而不是只參觀一棟建築，交付驅動程式仍然節省了大量的時間和精力。 如果查詢的篩選器中具有`IN`分區鍵和關鍵字，則僅檢查相關物理分區的索引以檢查資料。

## <a name="avoiding-cross-partition-queries"></a>避免跨分區查詢

對於大多數容器，您不可避免地會有一些跨分區查詢。 有一些跨分區查詢是可以的！ 幾乎所有查詢操作都支援跨分區（邏輯分區鍵和物理分區）。 Azure Cosmos DB 在查詢引擎和用戶端 SDK 中也有許多優化，用於跨物理分區並行化查詢執行。

對於大多數讀取重方案，我們建議在查詢篩選器中選擇最常見的屬性。 您還應確保分區金鑰符合其他[分區金鑰選擇最佳做法](partitioning-overview.md#choose-partitionkey)。

避免跨分區查詢通常只對大型容器很重要。 每次檢查物理分區的索引以檢查結果時，即使物理分區中的任何項與查詢的篩選器匹配，您也會至少向您收取約 2.5 RU 的費用。 因此，如果您只有一個（或只有幾個）物理分區，則跨分區查詢不會消耗比分區內查詢多得多的 RU。

物理分區的數量與預配的 RU 的數量相關聯。 每個物理分區允許最多 10，000 個預配的 RU，並且可以存儲多達 50 GB 的資料。 Azure 宇宙資料庫將自動為您管理物理分區。 容器中的物理分區數取決於預配輸送量和消耗的存儲。

如果工作負荷滿足以下條件，應儘量避免跨分區查詢：
- 您計畫擁有超過 30，000 RU 的預配
- 您計畫存儲超過 100 GB 的資料

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的資料分割：

- [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
- [Azure Cosmos DB 中的綜合分割區索引鍵](synthetic-partition-keys.md)
