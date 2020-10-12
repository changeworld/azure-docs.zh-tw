---
title: 使用 Azure Cosmos DB 中的變更摘要支援
description: 使用 Azure Cosmos DB 的變更摘要支援來追蹤文件中的變更、以事件為基礎的處理 (例如觸發程序)，以及讓快取和分析系統保持最新狀態
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 4cd0ad1553f04a781349a4664fbb408108015632
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510278"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Azure Cosmos DB 中變更摘要

Azure Cosmos DB 中的變更摘要支援是靠接聽 Azure Cosmos 容器的任何變更而運作。 然後變更摘要會輸出已排序的文件清單，這些文件已依其修改的順序變更過。 變更會保存，可進行非同步累加處理，而輸出可配送給一或多個取用者進行平行處理。

深入了解[變更摘要設計模式](change-feed-design-patterns.md)。

## <a name="supported-apis-and-client-sdks"></a>支援的 API 和用戶端 SDK

此功能目前受到下列 Cosmos DB API 和用戶端 SDK 支援。

| **用戶端驅動程式** | **SQL API** | **適用於 Cassandra 的 Azure Cosmos DB API** | **適用於 MongoDB 的 Azure Cosmos DB API** | **Gremlin API**|**資料表 API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | 是 | 是 | 是 | 是 | 否 |
|Java|是|是|是|是|否|
|Python|是|是|是|是|否|
|節點/JS|是|是|是|是|否|

## <a name="change-feed-and-different-operations"></a>變更摘要和不同的作業

現在，您會在變更摘要中看到所有插入和更新。 您無法篩選特定作業類型的變更摘要。 其中一個可能的替代方案是，在變更摘要中處理項目時，在項目上新增「軟標記」以用於更新，並據以篩選。

目前變更摘要不會記錄刪除項目。 與上一個範例類似，您可以在要刪除的項目上新增軟標記。 例如，您可以在項目中新增稱為 "deleted" 的屬性，並將其設定為 "true"，然後在項目上設定 TTL，讓其可以被自動刪除。 您可以讀取歷程記錄項目的變更摘要 (項目的相對應最新變更，不包含中繼變更)，例如，五年前新增的項目。 您最遠可回到容器的原始位置讀取變更摘要，但如果某個項目已刪除，就會從變更摘要中加以移除。

### <a name="sort-order-of-items-in-change-feed"></a>變更摘要中項目的排序順序

變更摘要項目會按修改時間順序排列。 系統會依據邏輯分割區索引鍵保障此排序順序。

### <a name="consistency-level"></a>一致性層級

使用最終一致性層級中的變更摘要時，在後續的變更摘要讀取作業之間可能會有重複的事件 (一個讀取作業的最後一個事件會顯示為下一個的第一個)。

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>多重區域 Azure Cosmos 帳戶中的變更摘要

在多重區域 Azure Cosmos 帳戶，中，如果寫入區域進行容錯移轉，變更摘要會跨手動容錯移轉作業運作，而且會連續運作。

### <a name="change-feed-and-time-to-live-ttl"></a>變更摘要和存留時間 (TTL)

如果項目上的 TTL (存留時間) 屬性設為 -1，變更摘要會永久保存。 如果未刪除資料，即會一直保留在變更摘要中。  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>變更摘要和 _etag、_lsn 或 _ts

_etag 格式是作為內部之用，因為該格式可以隨時變更，請別依賴它。 _ts 是修改或建立時間戳記。 您可以使用 _ts，依時間順序進行比較。 _lsn 是僅為變更摘要新增的批次識別碼，其代表交易識別碼。 許多項目可能有相同的 _lsn。 FeedResponse 上的 ETag 與您在項目上看到的 _etag 不同。 _etag 是內部識別碼，而且用於並行控制。 _Etag 屬性會告訴您專案的版本，而 ETag 屬性則是用來排序饋送。

## <a name="working-with-change-feed"></a>使用變更摘要

您可以搭配變更摘要使用下列選項：

* [搭配使用變更摘要與 Azure Functions](change-feed-functions.md)
* [搭配變更摘要處理器使用變更摘要](change-feed-processor.md) 

容器內每個邏輯分割區索引鍵都可使用變更摘要，因此可以配送給一或多個取用者以進行平行處理，如下圖所示。

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Azure Cosmos DB 變更摘要的分散式處理" border="false":::

## <a name="features-of-change-feed"></a>變更摘要的功能

* 根據預設，所有 Azure Cosmos 帳戶都會啟用變更摘要。

* 和任何其他 Azure Cosmos DB 作業一樣，您可以在與您的 Azure Cosmos 資料庫關聯的任何區域中，使用[佈建輸送量](request-units.md)從變更摘要讀取。

* 變更摘要包含對容器內項目進行的插入與更新作業。 您可以擷取刪除項目，方法是在您的項目 (例如文件) 內設定「虛刪除」旗標來取代刪除動作。 或者，您可以使用 [TTL 功能](time-to-live.md)為項目設定有限的逾期期限。 例如 24 小時，並使用該屬性的值擷取刪除項目。 使用此解決方案，您必須在比 TTL 逾期期限更短的時間間隔內處理變更。

* 對項目所做的每項變更皆會在變更摘要中出現一次，且用戶端必須管理檢查點邏輯。 如果想要避免複雜的檢查點管理作業，變更摘要處理器提供了自動檢查點和「至少一次」語意。 請參閱[搭配變更摘要處理器使用變更摘要](change-feed-processor.md)。

* 變更記錄檔中只會包含指定項目的最新變更。 中繼變更可能無法使用。

* 變更摘要會依照各個邏輯分割區索引鍵值內的修改順序排序。 跨分割區索引鍵值順序不會是固定的。

* 變更可以從任何時間點同步處理，也就是說，可用變更沒有固定的資料保留期限。

* Azure Cosmos 容器所有邏輯分割區索引鍵的變更都是以平行方式提供使用。 這項功能可讓大型容器的變更由多個取用者平行處理。

* 應用程式可以在同一個容器上同時要求多個變更摘要。 ChangeFeedOptions.StartTime 可用來提供初始的開始點。 例如，尋找與指定時鐘時間相對應的接續權杖。 ContinuationToken (如果指定) 會優先於 StartTime 和 StartFromBeginning 值。 ChangeFeedOptions.StartTime 的精確度為 5 秒內。

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Cassandra API 和 MongoDB API 中的變更摘要

變更摘要功能會呈現為 MongoDB API 中的變更資料流，並在 Cassandra API 中使用述詞進行查詢。 若要深入了解 MongoDB API 的實作詳細資料，請參閱[適用於 MongoDB 的 Azure Cosmos DB API 中的變更資料流](mongodb-change-streams.md)。

原生 Apache Cassandra 提供異動資料擷取 (CDC)，這是一種標幟特定資料表以便封存的機制，而且會在達到 CDC 記錄的可設定磁碟大小之後，拒絕那些資料表的寫入。 適用於 Cassandra 的 Azure Cosmos DB API 中的變更摘要功能可增強透過 CQL 使用述詞查詢變更的能力。 若要深入了解實作詳細資料，請參閱[適用於 Cassandra 的 Azure Cosmos DB API 中的變更摘要](cassandra-change-feed.md)。

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要：

* [讀取變更摘要的選項](read-change-feed.md)
* [搭配使用變更摘要與 Azure Functions](change-feed-functions.md)
* [使用變更摘要處理器](change-feed-processor.md)
