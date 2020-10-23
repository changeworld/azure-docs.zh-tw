---
title: Azure 串流分析輸出至 Azure Cosmos DB
description: 本文說明如何使用 Azure 串流分析將輸出儲存至 JSON 輸出的 Azure Cosmos DB，以針對非結構化 JSON 資料進行資料封存和低延遲查詢。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: 5b28d75e6526f27fd0076244ec32848dbf20e91e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424780"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure 串流分析輸出至 Azure Cosmos DB  
Azure 串流分析可以將 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 設定為 JSON 輸出的目標，讓您能夠針對非結構化的 JSON 資料進行資料封存和低延遲查詢。 本文件涵蓋實作這種組態的一些最佳作法。 當您使用 Azure Cosmos DB 作為輸出時，建議您將作業設定為相容性層級1.2。

如果您尚不熟悉 Azure Cosmos DB，請參閱 [Azure Cosmos DB 文件](https://docs.microsoft.com/azure/cosmos-db/)以開始使用。 

> [!Note]
> 目前，串流分析僅支援透過 *SQL API* 來連線至 Azure Cosmos DB。
> 尚不支援其他 Azure Cosmos DB API。 如果您將串流分析指向使用其他 API 建立的 Azure Cosmos DB 帳戶，可能會無法正確儲存資料。 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>將 Azure Cosmos DB 作為輸出目標的基本概念
串流分析中的 Azure Cosmos DB 輸出可將串流處理結果以 JSON 輸出的形式寫入到您的 Azure Cosmos DB 容器。 

串流分析不會在您的資料庫中建立容器。 相反地，其會要求您預先加以建立。 這可讓您控制 Azure Cosmos DB 容器的計費成本。 您也可以使用 [Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) \(部分機器翻譯\) 來直接調整容器的效能、一致性及容量。

> [!Note]
> 您必須從 Azure Cosmos DB 防火牆將 0.0.0.0 新增至允許的 IP 清單。

下列各節會詳述適用於 Azure Cosmos DB 的一些容器選項。

## <a name="tuning-consistency-availability-and-latency"></a>調整一致性、可用性與延遲
為了符合您的應用程式需求，Azure Cosmos DB 允許您微調資料庫與容器，並在一致性、可用性、延遲及輸送量之間進行取捨。 

您可以視案例針對讀取與寫入延遲所需的讀取一致性層級，來選擇資料庫帳戶上的一致性層級。 您可以透過擴大容器上的要求單位 (RU) 來改善輸送量。 

此外，Azure Cosmos DB 預設會在針對您容器的每個 CRUD 作業上，進行同步索引編製。 這是另一個在 Azure Cosmos DB 中控制寫入/讀取效能的實用選項。 

如需詳細資訊，請檢閱[變更資料庫及查詢的一致性層級](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>來自串流分析的 Upsert
串流分析與 Azure Cosmos DB 的整合可讓您根據指定的 [文件識別碼] 資料行，在容器中插入或更新記錄。 這也稱為 *upsert*。

串流分析會使用開放式的 upsert 方法。 更新只會在因文件識別碼衝突而導致插入失敗時發生。 

使用相容性層級 1.0 時，串流分析會以 PATCH 作業的形式執行此更新，因此其會針對文件啟用部分更新。 串流分析會以累加方式加入新的屬性，或是取代現有的屬性。 但是，變更 JSON 文件中陣列屬性的值，會使系統覆寫整個陣列。 也就是不會合併陣列。 

使用 1.2 時，upsert 行為會修改為插入或取代文件。 關於相容性層級 1.2 的後續小節會進一步說明此行為。

如果內送 JSON 文件有現有的 [識別碼] 欄位，系統會在 Azure Cosmos DB 中自動將該欄位作為 [文件識別碼] 資料行。 任何後續的寫入都會以這種方式處理，並導致下列其中一種情況：

- 唯一識別碼會導致插入。
- 設定為 [識別碼] 的重複識別碼和 [文件識別碼] 會導致 upsert。
- 未設定的重複識別碼和 [文件識別碼] 會在第一個文件之後導致錯誤。

如果您想要儲存「所有」文件 (包括具有重複識別碼的文件)，請將查詢中的 [識別碼] 欄位重新命名 (使用 **AS** 關鍵字)。 讓 Azure Cosmos DB 建立 [識別碼] 欄位，或是以另一個資料行的值取代 [識別碼] 欄位 (使用 **AS** 關鍵字或使用 [文件識別碼] 設定)。

## <a name="data-partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割
Azure Cosmos DB 會根據您的工作負載自動調整分割區。 因此我們建議使用[無限制的](../cosmos-db/partition-data.md)容器作為分割資料的方法。 當串流分析寫入無限制的容器時，其會使用與先前查詢步驟或輸入資料分割配置同樣數目的平行寫入器。

> [!NOTE]
> Azure 串流分析僅針對位於頂層的分割區索引鍵支援無限制容器。 例如，支援 `/region`。 不支援巢狀分割區索引鍵 (例如 `/region/name`)。 

視您選擇的分割區索引鍵而定，您可能會收到此「警告」：

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

請務必選擇具有數個相異值的分割區索引鍵屬性，那能讓您將工作負載平均分散到這些值上。 作為資料分割的自然成品，涉及相同分割區索引鍵的要求會受到單一分割區的最大輸送量所限制。 

屬於相同資料分割索引鍵值之檔的儲存體大小限制為 20 GB ([實體分割區大小限制](../cosmos-db/partition-data.md) 為 50 gb) 。 理想的資料分割索引鍵是在查詢中經常出現做為篩選的資料 [分割索引鍵](../cosmos-db/partitioning-overview.md#choose-partitionkey) ，而且具有足夠的基數，以確保您的解決方案可進行調整。

用於串流分析查詢和 Cosmos DB 的分割區索引鍵不需要相同的資料分割索引鍵。 完全平行拓朴建議使用 *輸入分割*區索引鍵， `PartitionId` 作為串流分析查詢的分割區索引鍵，但這可能不是 Cosmos DB 容器分割區索引鍵的建議選擇。

分割區索引鍵也是 Azure Cosmos DB 的預存程序和觸發程序中交易的界限。 您應該選擇分割區索引鍵，使在交易中一起發生的文件會共用相同的分割區索引鍵值。 [Azure Cosmos DB 中的資料分割](../cosmos-db/partitioning-overview.md)能針對選擇分割區索引鍵提供更多詳細資料。

針對固定 Azure Cosmos DB 容器，在其空間已滿時，串流分析並未提供任何擴大或擴增的方法。 其具有 10 GB 和 10,000 RU/秒的輸送量上限。 若要將資料從固定容器移轉到無限制的容器 (例如輸送量至少為 1,000 RU/秒，且具有分割區索引鍵的容器)，請使用[資料移轉工具](../cosmos-db/import-data.md)或[變更摘要程式庫](../cosmos-db/change-feed.md)。

寫入多個固定容器的能力即將淘汰。 我們不建議將其用來擴增您的串流分析作業。

## <a name="improved-throughput-with-compatibility-level-12"></a>相容性層級 1.2 的輸送量改善
使用相容性層級 1.2 時，串流分析支援原生整合以大量寫入 Azure Cosmos DB。 此整合可讓系統有效地寫入 Azure Cosmos DB，同時將輸送量最大化並有效率地處理節流要求。 

此改善的寫入機制之所以會在新的相容性層級下提供，是因為 upsert 行為上的差異。 使用 1.2 之前的層級時，upsert 行為是會插入或合併文件。 使用 1.2 時，upsert 行為會修改為插入或取代文件。

使用 1.2 之前的層級時，串流分析會使用自訂的預存程序，將每個分割區索引鍵的文件針對 Azure Cosmos DB 進行大量 upsert。 在該情況下，批次會寫入為交易。 即使只有單一記錄遇到暫時性錯誤 (節流)，整個批次也都需要淘汰。 這使得即使具有合理節流的案例，也會變得相對緩慢。

下列範例示範會從相同的 Azure 事件中樞輸入進行讀取的兩個相同串流分析作業。 這兩個串流分析作業都是使用傳遞查詢進行[完全分割](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) \(部分機器翻譯\)，並會寫入相同的 Azure Cosmos DB 容器。 左側的計量是來自搭配相容性層級 1.0 設定的作業。 右側的計量則是搭配 1.2 設定。 Azure Cosmos DB 容器的分割區索引鍵是來自輸入事件的唯一 GUID。

![串流分析計量的比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

由於事件中樞中的內送事件速率，比 Azure Cosmos DB 容器 (20,000 個 RU) 所設定可接受的量還要高出兩倍，因此 Azure Cosmos DB 中預期會進行節流。 不過，搭配 1.2 的作業會持續以較高的輸送量 (每分鐘的輸出事件數) 進行寫入，且具有較低的平均 SU% 使用率。 在您的環境中，此差異還會取決於幾個額外的因素。 這些因素包括事件格式的選擇、輸入事件/訊息大小、分割區索引鍵，以及查詢。

![Azure Cosmos DB 計量比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

使用 1.2 時，串流分析能以更加聰明的方式利用 Azure Cosmos DB 中 100% 的可用輸送量，且只有非常少因節流或速率限制所導致的重新提交。 這能為其他工作負載提供更好的體驗，例如同時在容器上執行的查詢。 如果您想要了解串流分析如何以 Azure Cosmos DB 作為接收，從每秒 1,000 個訊息擴增到每秒 10,000 個訊息，請嘗試[這個 Azure 範例專案](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) \(英文\)。

Azure Cosmos DB 輸出的輸送量與 1.0 和 1.1 相同。 我們「強烈建議」您在搭配 Azure Cosmos DB 使用串流分析時，使用相容性層級 1.2。

## <a name="azure-cosmos-db-settings-for-json-output"></a>適用於 JSON 輸出的 Azure Cosmos DB 設定

在串流分析中使用 Azure Cosmos DB 作為輸出會產生下列資訊提示。

![適用於 Azure Cosmos DB 輸出資料流的資訊欄位](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|欄位           | 描述|
|-------------   | -------------|
|輸出別名    | 在您的串流分析查詢中參考此輸出的別名。|
|訂用帳戶    | Azure 訂用帳戶。|
|帳戶識別碼      | Azure Cosmos DB 帳戶的名稱或端點 URI。|
|帳戶金鑰     | Azure Cosmos DB 帳戶的共用存取金鑰。|
|資料庫        | Azure Cosmos DB 資料庫名稱。|
|容器名稱 | 容器名稱，例如 `MyContainer`。 名為 `MyContainer` 的容器必須存在。  |
|文件識別碼     | 選擇性。 輸出事件中的資料行名稱，輸出事件是作為唯一索引鍵，插入或更新作業必須依據該索引鍵。 如果保留空白，所有事件都會插入，且不會有更新選項。|

在您設定 Azure Cosmos DB 輸出之後，您可以在查詢中將其作為 [INTO 陳述式](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics) \(英文\) 的目標使用。 當您以該方式使用 Azure Cosmos DB 輸出時，[必須明確設定分割區索引鍵](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks) \(部分機器翻譯\)。 

輸出記錄必須包含區分大小寫的資料行，其名稱必須以 Azure Cosmos DB 中的分割區索引鍵命名。 若要達成更高的平行處理，陳述式可能會需要使用相同資料行的 [PARTITION BY 子句](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) \(部分機器翻譯\)。

以下是查詢範例：

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>錯誤處理和重試

如果在串流分析將事件傳送到 Azure Cosmos DB 期間發生暫時性失敗、服務無法使用或節流，串流分析會無限期地重試，直到順利完成作業為止。 但其不會針對下列失敗嘗試重試：

- 未經授權 (HTTP 錯誤碼 401)
- 找不到 (HTTP 錯誤碼 404)
- 禁止 (HTTP 錯誤碼 403)
- 不正確的要求 (HTTP 錯誤碼 400)

## <a name="common-issues"></a>常見問題

1. 將唯一索引條件約束新增至集合，而來自串流分析的輸出資料違反此條件約束。 請確定來自串流分析的輸出資料不會違反唯一條件約束或移除條件約束。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的唯一索引鍵條件約束](../cosmos-db/unique-keys.md)。

2. 資料 `PartitionKey` 行不存在。

3. 資料 `Id` 行不存在。

## <a name="next-steps"></a>後續步驟

* [了解來自 Azure 串流分析的輸出](stream-analytics-define-outputs.md) 
* [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Azure 串流分析自訂 Blob 輸出資料分割](stream-analytics-custom-path-patterns-blob-storage-output.md)
