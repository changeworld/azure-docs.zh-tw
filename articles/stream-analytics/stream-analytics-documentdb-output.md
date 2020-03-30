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
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254439"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure 串流分析輸出至 Azure Cosmos DB  
Azure 流分析可以針對[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)進行 JSON 輸出，從而啟用對非結構化 JSON 資料的資料存檔和低延遲查詢。 本文件涵蓋實作這種組態的一些最佳作法。

如果您不熟悉 Azure 宇宙資料庫，請參閱[Azure Cosmos DB 文檔](https://docs.microsoft.com/azure/cosmos-db/)以開始。 

> [!Note]
> 此時，流分析僅支援通過*SQL API*連接到 Azure 宇宙 DB。
> 尚不支援其他 Azure Cosmos DB API。 如果將流分析指向使用其他 API 創建的 Azure Cosmos DB 帳戶，則資料可能無法正確存儲。 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Azure 宇宙 DB 作為輸出目標的基礎知識
流分析中的 Azure Cosmos DB 輸出允許將流處理結果作為 JSON 輸出寫入 Azure Cosmos 資料庫容器。 

流分析不會在資料庫中創建容器。 相反，它要求您預先創建它們。 然後，您可以控制 Azure Cosmos DB 容器的計費成本。 還可以通過使用[Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx)直接調整容器的性能、一致性和容量。

> [!Note]
> 您必須從 Azure Cosmos DB 防火牆將 0.0.0.0 新增至允許的 IP 清單。

以下各節詳細介紹了 Azure Cosmos DB 的一些容器選項。

## <a name="tuning-consistency-availability-and-latency"></a>調整一致性、可用性和延遲
為了符合應用程式要求，Azure Cosmos DB 允許您微調資料庫和容器，並在一致性、可用性、延遲和輸送量之間進行權衡。 

您可以視案例針對讀取與寫入延遲所需的讀取一致性層級，來選擇資料庫帳戶上的一致性層級。 您可以通過擴展容器上的請求單位 （R） 來提高輸送量。 

此外，預設情況下，Azure Cosmos DB 支援將每個 CRUD 操作的同步索引到容器。 這是控制 Azure Cosmos DB 中寫入/讀取性能的另一個有用選項。 

有關詳細資訊，請查看["更改資料庫和查詢一致性級別"](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>來自串流分析的 Upsert
流分析與 Azure Cosmos DB 集成允許您根據給定**的文件識別碼**列在容器中插入或更新記錄。 這也被稱為*上升*。

流分析使用樂觀向上的方法。 僅當插入失敗時，才會發生文件識別碼 衝突。" 

使用相容性級別 1.0，流分析將此更新作為 PATCH 操作執行，因此它支援對文檔的部分更新。 流分析以增量方式添加新屬性或替換現有屬性。 但是，JSON 文檔中陣列屬性值的更改會導致覆蓋整個陣列。 也就是說，陣列不會合並。 

使用 1.2，修改 upsert 行為以插入或替換文檔。 有關相容性級別 1.2 的後一節進一步描述了此行為。

如果傳入的 JSON 文檔具有現有 ID 欄位，則該欄位將自動用作 Azure Cosmos DB 中**的文件識別碼**列。 任何後續寫入都這樣處理，從而導致以下情況之一：

- 唯一 ID 會導致插入。
- 重複 ID 和**文件識別碼**設置為**ID**會導致向上。
- 重複 ID 和**文件識別碼**未在第一個文檔之後設置導致錯誤。

如果要保存*所有*文檔（包括具有重複 ID的文檔），請重命名查詢中的 ID 欄位（通過使用**AS**關鍵字）。 讓 Azure Cosmos DB 創建 ID 欄位或將 ID 替換為另一列的值（通過使用**AS**關鍵字或使用**文件識別碼**設置）。

## <a name="data-partitioning-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的資料分區
Azure Cosmos DB 可根據您的工作負荷自動縮放分區。 因此，我們建議[使用無限](../cosmos-db/partition-data.md)容器作為對資料進行分區的方法。 當 Stream Analytics 寫入無限容器時，它使用與前一個查詢步驟或輸入分區方案一樣多的並行編寫器。

> [!NOTE]
> Azure 流分析僅支援在頂層具有分區鍵的無限容器。 例如，支援 `/region`。 不支援嵌套分區鍵（例如），`/region/name`不支援 。 

根據您選擇的分區鍵，您可能會收到此_警告_：

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

選擇具有許多不同值的分區鍵屬性非常重要，該屬性允許您在這些值之間均勻分佈工作負載。 作為分區的自然偽影，涉及同一分區金鑰的請求受單個分區的最大輸送量的限制。 

屬於同一分區金鑰的文檔的存儲大小限制為 10 GB。 理想的分區金鑰是查詢中頻繁顯示為篩選器的分區鍵，具有足夠的基數以確保解決方案具有可擴充性。

分區鍵也是 Azure Cosmos DB 的預存程序和觸發器中的事務的邊界。 應選擇分區鍵，以便事務中一起發生的文檔共用相同的分區鍵值。 在[Azure Cosmos DB 中分區](../cosmos-db/partitioning-overview.md)的文章提供了有關選擇分區鍵的更多詳細資訊。

對於固定的 Azure Cosmos DB 容器，流分析不允許在已滿後向上或橫向擴展。 它們具有 10 GB 和 10，000 RU/s 輸送量的上限。 要將資料從固定容器遷移到無限制的容器（例如，具有至少 1，000 RU/s 和分區鍵的容器），請使用[資料移轉工具](../cosmos-db/import-data.md)或[更改源庫](../cosmos-db/change-feed.md)。

正在棄用寫入多個固定容器的能力。 我們不建議它擴展您的流分析作業。

## <a name="improved-throughput-with-compatibility-level-12"></a>通過相容性級別 1.2 提高了輸送量
使用相容性級別 1.2，流分析支援本機集成以批量寫入 Azure Cosmos DB。 這種集成支援有效地寫入 Azure Cosmos DB，同時最大限度地提高輸送量並高效地處理限制請求。 

由於 upsert 行為的差異，改進的書寫機制可在新的相容性級別下使用。 在 1.2 之前的級別時，upsert 行為是插入或合併文檔。 使用 1.2，修改 upsert 行為以插入或替換文檔。

在 1.2 之前，流分析使用自訂預存程序將每個分區金鑰的分區金鑰大量擴充到 Azure Cosmos DB 中。 在那裡，批次處理被寫入為事務。 即使單個記錄達到暫時性錯誤（限制），也必須重試整個批次處理。 這使得即使具有合理限制的情況也相對較慢。

下面的示例顯示兩個相同的流分析作業從同一 Azure 事件中心輸入讀取。 兩個流分析作業都使用直通查詢[完全分區](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)，並寫入相同的 Azure Cosmos DB 容器。 左側的指標來自配置了相容性級別 1.0 的作業。 右側的指標配置了 1.2。 Azure Cosmos DB 容器的分區鍵是來自輸入事件的唯一 GUID。

![流分析指標的比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

事件中心中的傳入事件速率比配置為接收的 Azure Cosmos DB 容器（20，000 個 R）高兩倍，因此在 Azure Cosmos DB 中應限制。 但是，具有 1.2 的作業始終以更高的輸送量（每分鐘輸出事件）寫入，並且平均 SU% 利用率較低。 在您的環境中，這種差異將取決於幾個其他因素。 這些因素包括事件格式的選擇、輸入事件/消息大小、分區鍵和查詢。

![Azure 宇宙 DB 指標比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

使用 1.2，流分析在利用 Azure Cosmos DB 中 100% 的可用輸送量時更加智慧，而從限制或速率限制中重新提交的數量很少。 這為其他工作負荷（如同時在容器上運行的查詢）提供了更好的體驗。 如果要查看流分析如何擴展 Azure Cosmos DB 作為每秒 1，000 到 10，000 條消息的接收器，請嘗試[此 Azure 示例專案](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)。

Azure Cosmos DB 輸出的輸送量與 1.0 和 1.1 相同。 *我們強烈建議*在流分析中使用與 Azure Cosmos DB 的相容性級別 1.2。

## <a name="azure-cosmos-db-settings-for-json-output"></a>用於 JSON 輸出的 Azure 宇宙資料庫設置

在流分析中使用 Azure Cosmos DB 作為輸出將生成以下資訊提示。

![Azure 宇宙資料庫輸出流的資訊欄位](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|欄位           | 描述|
|-------------   | -------------|
|輸出別名    | 要在流分析查詢中引用此輸出的別名。|
|訂用帳戶    | Azure 訂用帳戶。|
|帳戶識別碼      | Azure Cosmos DB 帳戶的名稱或端點 URI。|
|帳戶金鑰     | Azure Cosmos DB 帳戶的共用存取金鑰。|
|資料庫        | Azure Cosmos DB 資料庫名稱。|
|容器名稱 | 容器名稱，如`MyContainer`。 必須存在一`MyContainer`個命名的容器。  |
|文件識別碼     | 選擇性。 輸出事件中的資料行名稱，輸出事件是作為唯一索引鍵，插入或更新作業必須依據該索引鍵。 如果將其留空，將插入所有事件，沒有更新選項。|

配置 Azure Cosmos DB 輸出後，可以在查詢中將其用作[INTO 語句](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)的目標。 以這種方式使用 Azure Cosmos DB 輸出時，[需要顯式設置分區金鑰](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks)。 

輸出記錄必須包含以 Azure Cosmos DB 中的分區鍵命名的區分大小寫列。 為了實現更大的並行化，語句可能需要使用同一列的[分區 BY 子句](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)。

以下是查詢範例：

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>錯誤處理和重試

如果在流分析向 Azure Cosmos DB 發送事件時發生暫時性故障、服務不可用或限制，則流分析將無限期地重試以成功完成操作。 但它不會嘗試重試以下故障：

- 未經授權 （HTTP 錯誤代碼 401）
- 未找到 （HTTP 錯誤代碼 404）
- 禁止 （HTTP 錯誤代碼 403）
- 錯誤請求（HTTP 錯誤代碼 400）
