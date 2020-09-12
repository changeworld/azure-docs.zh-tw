---
title: 使用 Azure 串流分析中的查詢平行化和調整作業
description: 本文說明如何透過設定輸入資料分割、微調查詢定義，及設定工作串流處理單元來調整串流分析工作。
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: aed0c83bfa61f6afdbdcca3c10dbd5fac3f823d3
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89458173"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>利用 Azure 串流分析中的查詢平行化作業
本文會示範如何利用 Azure 串流分析中的平行化作業。 您可以了解如何透過設定輸入資料分割並調整分析查詢定義來調整串流分析工作。
先決條件是，您必須熟悉[了解及調整串流處理單位](stream-analytics-streaming-unit-consumption.md)中所述的串流處理單位概念。

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>串流分析工作由哪些部分所組成？
串流分析作業定義包含至少一個串流輸入、一個查詢，以及輸出。 輸入是指作業讀取資料流的來源。 查詢是用來轉換資料輸入資料流，而輸出是作業傳送作業結果的目的地。

## <a name="partitions-in-inputs-and-outputs"></a>輸入和輸出中的分割區
資料分割可讓您根據[分割區索引鍵](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#partitions) \(部分機器翻譯\)，將資料分割成子集。 如果您的輸入 (例如事件中樞) 已由索引鍵分割，強烈建議您在將輸入新增到串流分析作業時指定此分割區索引鍵。 調整串流分析作業需要利用輸入和輸出中的分割區。 串流分析作業能夠以平行方式取用及寫入不同的分割區，其能增加輸送量。 

### <a name="inputs"></a>輸入
所有 Azure 串流分析輸入都可以利用資料分割：
-   事件中樞 (如果使用相容性層級 1.1 或更低版本，需要明確使用 PARTITION BY 關鍵字設定分割區索引鍵)
-   IoT 中樞 (如果使用相容性層級 1.1 或更低版本，需要明確使用 PARTITION BY 關鍵字設定分割區索引鍵)
-   Blob 儲存體

### <a name="outputs"></a>輸出

使用串流分析時，您可以利用輸出中的資料分割：
-   Azure Data Lake 儲存體
-   Azure Functions
-   Azure 資料表
-   Blob 儲存體 (可明確地設定資料分割索引鍵)
-   CosmosDB (必須明確地設定分割區索引鍵)
-   事件中樞 (必須明確地設定分割區索引鍵)
-   IoT 中樞 (需要明確地設定資料分割索引鍵)
-   服務匯流排
- 使用選擇性資料分割的 SQL 和 Azure Synapse Analytics：請參閱 [Azure SQL Database 頁面輸出](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf)的詳細資訊。

Power BI 不支援資料分割。 不過，您仍然可以分割輸入，如[本節](#multi-step-query-with-different-partition-by-values)中所述 

如需分割區的詳細資訊，請參閱下列文章：

* [事件中樞功能概觀](../event-hubs/event-hubs-features.md#partitions)
* [資料分割](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>窘迫平行作業
「窘迫平行」作業是 Azure 串流分析中調整性最高的案例。 它將對於查詢執行個體之輸入的某個資料分割，連接到輸出的某個資料分割。 此平行處理原則具有下列需求︰

1. 如果查詢邏輯相依於同一個查詢執行個體所處理的相同索引鍵，則您必須確保事件會傳送至輸入的相同分割區。 對於事件中樞或 IoT 中樞，這表示事件資料必須設定 **PartitionKey** 值。 或者，您可以使用分割的傳送者。 對於 Blob 儲存體，這表示事件會傳送至相同的磁碟分割資料夾。 例如，會針對每個 userID 彙總資料的查詢執行個體，其中輸入事件中樞是使用 userID 作為分割區索引鍵來進行分割。 不過，如果您的查詢邏輯並不需要由同一個查詢執行個體處理相同的索引鍵，您可以忽略這個需求。 簡單的選取-投影-篩選查詢，即為此邏輯的一個例子。  

2. 下一步是確保您的查詢已進行分割。 針對具有相容性層級 1.2 或更高版本 (建議) 的作業，可以在輸入設定中將自訂資料行指定為分割區索引鍵，且該作業將會自動平行化。 針對具有相容性層級 1.0 或 1.1 的作業，您必須在查詢的所有步驟中使用 **PARTITION BY PartitionId**。 您可以使用多個步驟，但全部都必須依相同的索引鍵來分割。 

3. 串流分析所支援的大部分輸出都可以利用資料分割。 如果您使用的輸出類型不支援資料分割，您的作業將不會是「窘迫平行」的。 針對事件中樞輸出，請確定 [分割區索引鍵資料行] 已設定為用於查詢中的相同分割區索引鍵。 如需詳細資訊，請參閱[輸出](#outputs)一節。

4. 輸入分割區的數目必須等於輸出分割區的數目。 Blob 儲存體輸出可支援資料分割，並繼承上游查詢的資料分割配置。 當針對 Blob 儲存體指定資料分割索引鍵時，資料會依每個輸入分割區進行分割，因此結果仍然是完全平行。 以下是允許完全平行作業的分割區值範例：

   * 8 個事件中樞輸入分割區和 8 個事件中樞輸出分割區
   * 8 個事件中樞輸入分割區和 blob 儲存體輸出
   * 8 個事件中樞輸入分割區和 Blob 儲存體輸出，依含有任意基數的自訂欄位分割
   * 8 個 blob 儲存體輸入分割區和 blob 儲存體輸出
   * 8 個 blob 儲存體輸入分割區和 8 個事件中樞輸出分割區

以下幾節討論一些窘迫平行的範例情節。

### <a name="simple-query"></a>簡單查詢

* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 8 個分割區的事件中樞 ([分割區索引鍵資料行] 必須設定為使用 [PartitionId])

查詢：

```SQL
    --Using compatibility level 1.2 or above
    SELECT TollBoothId
    FROM Input1
    WHERE TollBoothId > 100
    
    --Using compatibility level 1.0 or 1.1
    SELECT TollBoothId
    FROM Input1 PARTITION BY PartitionId
    WHERE TollBoothId > 100
```

此查詢是簡單的篩選。 因此，我們不需要擔心將傳送到事件中樞的輸入分割。 請注意，相容性層級低於 1.2 的作業必須包含 **PARTITION BY PartitionId** 子句，使其能滿足稍早所述的需求 #2。 對於輸出，我們必須將作業中的事件中樞輸出設定為讓資料分割索引鍵設為 **PartitionId**。 最後一項檢查是確保輸入分割區數目等於輸出分割區數目。

### <a name="query-with-a-grouping-key"></a>利用群組索引鍵的查詢

* 輸入：具有 8 個分割區的事件中樞
* 輸出：Blob 儲存體

查詢：

```SQL
    --Using compatibility level 1.2 or above
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
    --Using compatibility level 1.0 or 1.1
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

此查詢含有群組索引鍵。 因此，分組在一起的事件必須傳送至相同的事件中樞資料分割。 在此範例中我們是依 TollBoothID 分組，因此，我們應該確保在事件傳送至事件中樞時，TollBoothID 會當作資料分割索引鍵使用。 然後在 ASA 中，我們可以使用 **PARTITION BY PartitionId** 從此資料分割配置繼承，並啟用完整的平行化作業。 因為輸出是 blob 儲存體，所以我們不需要擔心設定分割區索引鍵值，以滿足需求 #4。

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>*非*窘迫平行的情節範例

在上一節，我們示範一些窘迫平行情節。 在本節中，我們要討論的情節不符合成為窘迫平行的所有需求。 

### <a name="mismatched-partition-count"></a>不相符的分割區計數
* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 32 個分割區的事件中樞

如果輸入分割區計數不符合輸出分割區計數，則不論查詢為何，拓撲都不會是窘迫平行。 不過，我們仍然可以取得某種程度的平行處理。

### <a name="query-using-non-partitioned-output"></a>使用非資料分割的輸出查詢
* 輸入：具有 8 個分割區的事件中樞
* 輸出：Power BI

Power BI 輸出目前不支援資料分割。 因此，此情節不是窘迫平行。

### <a name="multi-step-query-with-different-partition-by-values"></a>具有不同 PARTITION BY 值的多重步驟查詢
* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 8 個分割區的事件中樞
* 相容性層級：1.0 或 1.1

查詢：

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

如您所見，第二個步驟把[TollBoothId]  當做資料分割索引鍵來使用。 此步驟和第一個步驟不同，因此需要變換一下。 

### <a name="multi-step-query-with-different-partition-by-values"></a>具有不同 PARTITION BY 值的多重步驟查詢
* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 8 個分割區的事件中樞 ([分割區索引鍵資料行] 必須設定為使用 [TollBoothId])
* 相容性層級 - 1.2 或更高版本

查詢：

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

相容性層級 1.2 或更高版本預設會啟用平行查詢執行。 例如，只要將 [TollBoothId] 資料行設為輸入分割區索引鍵，系統便會分割上一節中的查詢。 不需要 PARTITION BY PartitionId 子句。

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>計算工作的最大串流處理單元
資料流分析工作可使用的串流處理單元總數，取決於為工作定義之查詢中的步驟數目，和每個步驟的資料分割數目。

### <a name="steps-in-a-query"></a>查詢中的步驟
一個查詢可以有一或多個步驟。 每個步驟都是使用 **WITH** 關鍵字定義的子查詢。 在 **WITH** 關鍵字外的查詢 (只有一個查詢) 也視為一個步驟，例如下列查詢中的 **SELECT** 陳述式：

查詢：

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

此查詢有兩個步驟。

> [!NOTE]
> 本文稍後會詳細討論此查詢。
>  

### <a name="partition-a-step"></a>分割步驟
要分割步驟必須符合下列條件：

* 必須分割輸入來源。 
* 查詢的 **SELECT** 陳述式必須讀取某個已分割的輸入來源。
* 步驟內的查詢必須有 **PARTITION BY** 關鍵字。

分割查詢時，將會在個別的分割區群組中處理和彙總輸入事件，然後為每個群組產生輸出事件。 如果需要合併的彙總，您必須建立第二個非分割步驟來彙總。

### <a name="calculate-the-max-streaming-units-for-a-job"></a>計算工作的最大串流處理單元
所有非分割步驟合起來，可將串流分析作業調整為最多 6 個串流單元 (SU)。 此外，您可以在分割的步驟中，為每個資料分割新增 6 個 SU。
您可以在下表中看到部分**範例**。

| 查詢                                               | 作業的最多 SU |
| --------------------------------------------------- | ------------------- |
| <ul><li>查詢包含一個步驟。</li><li>步驟未分割。</li></ul> | 6 |
| <ul><li>輸入資料流會分割為 16 個。</li><li>查詢包含一個步驟。</li><li>步驟進行分割。</li></ul> | 96 (6 * 16 個資料分割) |
| <ul><li>查詢包含兩個步驟。</li><li>兩個步驟都不會分割。</li></ul> | 6 |
| <ul><li>輸入資料流分割時會除以 3。</li><li>查詢包含兩個步驟。 輸入步驟會分割，而第二個步驟則否。</li><li><strong>SELECT</strong> 陳述式會讀取分割的輸入。</li></ul> | 24 (18 個用於分割的步驟 + 6 個用於非分割的步驟) |

### <a name="examples-of-scaling"></a>調整的範例

下列查詢會計算三分鐘內通過收費站 (有三個收費亭) 的車流量。 此查詢可以調整為最多 6 個 SU。

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

若要讓查詢使用更多 SU，輸入資料流和查詢都必須分割。 因為資料流分割區設為 3，以下修改的查詢可以調整為最多 18 個 SU：

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

分割查詢時將會處理輸入事件並將其彙總在個別的資料分割群組中。 也會為每個群組產生輸出事件。 當 **GROUP BY** 欄位不是輸入資料流中的分割區索引鍵時，資料分割可能會導致某些非預期的結果。 例如，上一個查詢中的 **TollBoothId** 欄位不是 **Input1** 的分割區索引鍵。 結果是收費亭 #1 的資料可能分散在多個分割區。

串流分析會個別處理每個 **Input1** 分割區。 因此，在相同的輪轉視窗中，相同收費亭的汽車計數會建立多筆記錄。 如果無法變更輸入分割區索引鍵，可藉由新增非分割步驟彙總資料分割的值來解決此問題，如下列範例所示：

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

此查詢可以調整為 24 個 SU。

> [!NOTE]
> 如果您要聯結兩個資料流，請確定以您用來建立聯結的資料行的分割區索引鍵來分割資料流。 也請確定兩個資料流中的分割區數目相同。
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>大規模達到更高的輸送量

[窘迫平行](#embarrassingly-parallel-jobs)作業雖為必要，但並不足以大規模地維持較高的輸送量。 每個儲存體系統及其對應的串流分析輸出，在達成最佳寫入輸送量的方法上皆有所不同。 如同任何大規模的案例，可以透過使用正確的設定來解決一些挑戰。 此節討論幾個常見輸出的設定，並提供範例來維持每秒 1K、5K 與 10K 個事件的擷取速率。

下列觀察會使用具有無狀態 (通道) 查詢的串流分析作業、寫入事件中樞的基本 JavaScript UDF、Azure SQL DB 或 Cosmos DB。

#### <a name="event-hub"></a>事件中樞

|擷取速率 (每秒事件數) | 串流處理單位 | 輸出資源  |
|--------|---------|---------|
| 1K     |    1    |  2 個 TU   |
| 5K     |    6    |  6 個 TU   |
| 10K    |    12   |  10 個 TU  |

[事件中樞](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) \(英文\) 解決方案會針對串流單位 (SU) 和輸送量進行線性調整，這使其成為從串流分析中分析及串流資料的最有效率且最高效能的方法。 作業可以擴大到 192 個 SU，其大約具有最多 200 MB/s 的處理效能，或每天處理 19 兆個事件。

#### <a name="azure-sql"></a>Azure SQL
|擷取速率 (每秒事件數) | 串流處理單位 | 輸出資源  |
|---------|------|-------|
|    1K   |   3  |  S3   |
|    5K   |   18 |  P4   |
|    10K  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) \(英文\) 支援以平行方式寫入 (稱為繼承資料分割)，但預設並不會啟用。 不過，啟用繼承資料分割加上完全平行查詢，可能並不足以達成更高的輸送量。 SQL 寫入輸送量明顯取決於您的資料庫設定和資料表架構。 [SQL 輸出效能](./stream-analytics-sql-output-perf.md)一文具有可將寫入輸送量最大化之參數的詳細資料。 如 [Azure 串流分析輸出至 Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) 一文中所述，此解決方案在超過 8 個分割區之後，並不會以完全平行管線的形式進行線性調整，且可能需要在 SQL 輸出之前重新分割 (請參閱 [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) \(英文\))。 需要進階 SKU 以維持高 IO 速率，以及由每隔幾分鐘便會發生的記錄備份所帶來的額外負荷。

#### <a name="cosmos-db"></a>Cosmos DB
|擷取速率 (每秒事件數) | 串流處理單位 | 輸出資源  |
|-------|-------|---------|
|  1K   |  3    | 20K 個 RU  |
|  5K   |  24   | 60K 個 RU  |
|  10K  |  48   | 120K 個 RU |

來自串流分析的 [Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) \(英文\) 輸出已經更新為使用[相容性層級 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12) 底下的原生整合。 與相容性層級 1.1 (其為新作業的預設合規性層級) 相比，1.2 能提供明顯更高的輸送量並減少 RU 耗用量。 該解決方案會使用在 /deviceId 上分割的 CosmosDB 容器，而其餘的解決方案也會以相同方式設定。

所有 [大規模的串流 Azure 範例](https://github.com/Azure-Samples/streaming-at-scale) 都會使用事件中樞作為輸入，以透過負載模擬測試用戶端來進行饋送。 每個輸入事件都是 1KB 的 JSON 文件，其可以輕鬆地將所設定的擷取速率轉譯為輸送量速率 (1MB/s、5MB/s 及 10MB/s)。 事件會模擬 IoT 裝置針對最多 1K 個裝置傳送下列 JSON 資料 (以縮短的形式)：

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> 由於用於解決方案中的各種元件，設定可能會有所變更。 如需更精確的評估，請自訂範例以符合您的案例。

### <a name="identifying-bottlenecks"></a>找出瓶頸

使用您 Azure 串流分析作業中的 [計量] 窗格來找出管線中的瓶頸。 檢閱 [輸入/輸出事件] 來了解輸送量，並檢閱[「浮水印延遲」](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) \(英文\) 或 [待處理的事件] 來查看作業是否能與輸入速率保持一致。 針對事件中樞計量，請尋找 [節流的要求] 並據以調整閾值單位。 針對 Cosmos DB 計量，請檢閱 [輸送量] 底下的 [每個分割區索引鍵範圍每秒取用的 RU 上限]，以確保系統會一致地取用您的分割區索引鍵範圍。 針對 Azure SQL DB，請監視 [記錄 IO] 和 [CPU]。

## <a name="get-help"></a>取得說明

如需進一步的協助，請嘗試 [Azure 串流分析的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html) \(英文\)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

