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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364548"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure 串流分析輸出至 Azure Cosmos DB  
Azure 串流分析可以將 JSON 輸出的目標設為[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) ，針對非結構化 JSON 資料啟用資料封存和低延遲查詢。 本文件涵蓋實作這種組態的一些最佳作法。

如果您不熟悉 Azure Cosmos DB，請參閱[Azure Cosmos DB 檔](https://docs.microsoft.com/azure/cosmos-db/)以開始使用。 

> [!Note]
> 此時，串流分析只支援透過*SQL API*連接到 Azure Cosmos DB。
> 尚不支援其他 Azure Cosmos DB API。 如果您將串流分析指向 Azure Cosmos DB 使用其他 Api 建立的帳戶，則可能無法正確儲存資料。 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Azure Cosmos DB 做為輸出目標的基本概念
串流分析中的 Azure Cosmos DB 輸出可讓您將串流處理結果當做 JSON 輸出寫入至您的 Azure Cosmos DB 容器。 

串流分析不會在您的資料庫中建立容器。 相反地，它需要您事先建立它們。 接著，您可以控制 Azure Cosmos DB 容器的計費成本。 您也可以使用[Azure Cosmos DB api](https://msdn.microsoft.com/library/azure/dn781481.aspx)，直接調整容器的效能、一致性及容量。

> [!Note]
> 您必須從 Azure Cosmos DB 防火牆將 0.0.0.0 新增至允許的 IP 清單。

下列各節將詳細說明 Azure Cosmos DB 的一些容器選項。

## <a name="tuning-consistency-availability-and-latency"></a>微調一致性、可用性和延遲
為符合您的應用程式需求，Azure Cosmos DB 可讓您微調資料庫和容器，並在一致性、可用性、延遲和輸送量之間進行取捨。 

您可以視案例針對讀取與寫入延遲所需的讀取一致性層級，來選擇資料庫帳戶上的一致性層級。 您可以藉由相應增加容器上的要求單位（ru）來改善輸送量。 

此外，Azure Cosmos DB 預設會啟用對您容器的每個 CRUD 作業進行同步索引。 這是在 Azure Cosmos DB 中控制寫入/讀取效能的另一個實用選項。 

如需詳細資訊，請參閱[變更您的資料庫和查詢一致性層級](../cosmos-db/consistency-levels.md)一文。

## <a name="upserts-from-stream-analytics"></a>來自串流分析的 Upsert
串流分析與 Azure Cosmos DB 整合，可讓您根據指定的**檔識別碼**資料行，在容器中插入或更新記錄。 這也稱為*upsert*。

串流分析使用開放式 upsert 方法。 只有當插入因檔識別碼衝突而失敗時，才會進行更新。 

藉由相容性層級1.0，串流分析會執行此更新做為修補作業，因此它會啟用檔的部分更新。 串流分析加入新的屬性，或以累加方式取代現有的屬性。 不過，在 JSON 檔中，陣列屬性值的變更會導致覆寫整個陣列。 也就是說，陣列不會合並。 

使用1.2，會修改 upsert 行為以插入或取代檔。 有關相容性層級1.2 的後續章節會進一步說明此行為。

如果內送 JSON 檔具有現有的識別碼欄位，則會自動使用該欄位做為 Azure Cosmos DB 中的**檔識別碼**資料行。 任何後續的寫入都會以這種方式處理，因而導致下列其中一種情況：

- 唯一識別碼會導致插入。
- 將重複的識別碼和**檔識別碼**設定為**ID**會導致 upsert。
- 在第一份檔之後，重複的識別碼和**檔識別碼**未設定導致錯誤。

如果您想要儲存*所有*檔（包括具有重複識別碼的檔），請重新命名查詢中的 id 欄位（使用**AS**關鍵字）。 讓 Azure Cosmos DB 建立 ID 欄位，或以另一個資料行的值取代識別碼（使用**AS**關鍵字或使用**檔識別碼**設定）。

## <a name="data-partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割
Azure Cosmos DB 會根據您的工作負載自動調整資料分割。 因此，我們建議您不要[限制](../cosmos-db/partition-data.md)容器，做為資料分割的方法。 當串流分析寫入無限制的容器時，它會使用與先前查詢步驟或輸入資料分割配置一樣多的平行寫入器。

> [!NOTE]
> Azure 串流分析僅支援最上層具有分割區索引鍵的無限制容器。 例如，支援 `/region`。 不支援嵌套的分割區索引鍵（例如 `/region/name`）。 

視您選擇的資料分割索引鍵而定，您可能會收到此_警告_：

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

請務必選擇具有數個相異值的分割區索引鍵屬性，並可讓您將工作負載平均分散到這些值。 做為資料分割的自然成品，涉及相同資料分割索引鍵的要求會受到單一分割區的最大輸送量限制。 

屬於相同資料分割索引鍵之檔的儲存體大小限制為 10 GB。 理想的分割區索引鍵是在查詢中經常顯示為篩選準則，而且具有足夠的基數，以確保您的解決方案可調整。

分割區索引鍵也是預存程式中的交易和 Azure Cosmos DB 的觸發程式中的界限。 您應該選擇資料分割索引鍵，讓交易中一起出現的檔共用相同的資料分割索引鍵值。 [Azure Cosmos DB 中的分割](../cosmos-db/partitioning-overview.md)一文提供有關選擇分割區索引鍵的詳細資訊。

針對固定 Azure Cosmos DB 容器，串流分析不允許在填滿之後相應增加或放大。 其輸送量上限為 10 GB 和 10000 RU/秒。 若要將資料從固定容器遷移到無限制的容器（例如，至少一個具有 1000 RU/秒和分割區索引鍵），請使用[資料移轉工具](../cosmos-db/import-data.md)或[變更摘要庫](../cosmos-db/change-feed.md)。

寫入多個固定容器的功能已被取代。 我們不建議您將串流分析作業相應放大。

## <a name="improved-throughput-with-compatibility-level-12"></a>相容性層級1.2 的改良輸送量
透過相容性層級1.2，串流分析支援原生整合，以 Azure Cosmos DB 進行大量寫入。 這種整合可有效率地寫入 Azure Cosmos DB，同時最大化輸送量和有效率地處理節流要求。 

改善的寫入機制可在新的相容性層級下使用，因為 upsert 行為的差異。 在1.2 之前的層級中，upsert 行為是插入或合併檔。 使用1.2，會修改 upsert 行為以插入或取代檔。

在1.2 之前的層級中，串流分析會使用自訂預存程式，將每個分割區索引鍵的檔大量 upsert 到 Azure Cosmos DB。 其中的批次會寫入為交易。 即使單一記錄遇到暫時性錯誤（節流），也必須重試整個批次。 這會讓具有合理節流的案例相對緩慢。

下列範例顯示從相同的 Azure 事件中樞輸入讀取兩個相同的串流分析作業。 這兩個串流分析作業都會使用傳遞查詢[完全分割](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)，並寫入至相同的 Azure Cosmos DB 容器。 左側的計量來自于設定相容性層級1.0 的作業。 右側的計量是以1.2 設定。 Azure Cosmos DB 容器的分割區索引鍵是來自輸入事件的唯一 GUID。

![串流分析計量的比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

事件中樞中的傳入事件速率是設定為接受 Azure Cosmos DB 容器（20000 ru）的兩倍，因此必須在 Azure Cosmos DB 中進行節流。 不過，1.2 的作業會以較高的輸送量（每分鐘的輸出事件）和較低的平均 SU% 使用率一致地寫入。 在您的環境中，這項差異將取決於一些因素。 這些因素包括事件格式的選擇、輸入事件/訊息大小、資料分割索引鍵和查詢。

![Azure Cosmos DB 計量比較](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

使用1.2，串流分析在 Azure Cosmos DB 中使用100% 的可用輸送量更為聰明，只有節流或速率限制的 resubmissions。 這可為其他工作負載（例如在容器上執行的查詢）提供更好的體驗。 如果您想要查看串流分析如何使用 Azure Cosmos DB 作為每秒1000到10000個訊息的接收器來相應放大，請嘗試[此 Azure 範例專案](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)。

Azure Cosmos DB 輸出的輸送量與1.0 和1.1 相同。 我們*強烈建議*您在 Azure Cosmos DB 的串流分析中使用相容性層級1.2。

## <a name="azure-cosmos-db-settings-for-json-output"></a>JSON 輸出的 Azure Cosmos DB 設定

使用 Azure Cosmos DB 做為串流分析中的輸出，會產生下列資訊提示。

![Azure Cosmos DB 輸出資料流程的資訊欄位](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|欄位           | 描述|
|-------------   | -------------|
|輸出別名    | 在您的串流分析查詢中參照此輸出的別名。|
|訂用帳戶    | Azure 訂用帳戶。|
|帳戶識別碼      | Azure Cosmos DB 帳戶的名稱或端點 URI。|
|帳戶金鑰     | Azure Cosmos DB 帳戶的共用存取金鑰。|
|資料庫        | Azure Cosmos DB 資料庫名稱。|
|容器名稱 | 容器名稱，例如 `MyContainer`。 一個名為 `MyContainer` 的容器必須存在。  |
|文件識別碼     | 選擇性。 輸出事件中的資料行名稱，輸出事件是作為唯一索引鍵，插入或更新作業必須依據該索引鍵。 如果您將它保留空白，則會插入所有事件，但不含 update 選項。|

設定 Azure Cosmos DB 輸出之後，您可以在查詢中使用它做為[INTO 語句](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)的目標。 當您以這種方式使用 Azure Cosmos DB 輸出時，必須[明確設定分割](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks)區索引鍵。 

輸出記錄必須包含區分大小寫的資料行，其名稱是在 Azure Cosmos DB 中的資料分割索引鍵之後。 若要達到更高的平行處理，語句可能需要使用相同資料行的[PARTITION by 子句](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)。

以下是查詢範例：

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>錯誤處理和重試

如果暫時性失敗、服務無法使用或在串流分析正在傳送事件至 Azure Cosmos DB 時發生節流，串流分析會無限期地重試，以順利完成作業。 但是，它不會嘗試在下列失敗時重試：

- 未經授權（HTTP 錯誤碼401）
- NotFound （HTTP 錯誤碼404）
- 禁止（HTTP 錯誤碼403）
- BadRequest （HTTP 錯誤碼400）
