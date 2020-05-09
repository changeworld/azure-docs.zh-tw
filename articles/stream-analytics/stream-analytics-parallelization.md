---
title: 使用 Azure 串流分析中的查詢平行化和調整作業
description: 本文說明如何透過設定輸入資料分割、微調查詢定義，及設定工作串流處理單元來調整串流分析工作。
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 5bae53c04867233138929867c4895e7f6a2f2149
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838768"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>利用 Azure 串流分析中的查詢平行化作業
本文會示範如何利用 Azure 串流分析中的平行化作業。 您可以了解如何透過設定輸入資料分割並調整分析查詢定義來調整串流分析工作。
先決條件是，您必須熟悉[了解及調整串流處理單位](stream-analytics-streaming-unit-consumption.md)中所述的串流處理單位概念。

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>串流分析工作由哪些部分所組成？
串流分析作業定義至少包含一個資料流程輸入、查詢和輸出。 輸入是指作業讀取資料流的來源。 查詢是用來轉換資料輸入資料流，而輸出是作業傳送作業結果的目的地。

## <a name="partitions-in-inputs-and-outputs"></a>輸入和輸出中的資料分割
分割可讓您根據分割區索引[鍵](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#partitions)，將資料分割成子集。 如果您的輸入（例如事件中樞）是依索引鍵進行分割，則強烈建議在將輸入新增至您的串流分析作業時，指定此分割區索引鍵。 調整串流分析作業會利用輸入和輸出中的資料分割。 串流分析作業可以平行取用和寫入不同的分割區，以增加輸送量。 

### <a name="inputs"></a>輸入
所有 Azure 串流分析輸入都可以利用資料分割：
-   EventHub （如果使用相容性層級1.1 或更低，則需要以 PARTITION BY 關鍵字明確設定分割區索引鍵）
-   IoT 中樞（如果使用相容性層級1.1 或更低，則需要使用 PARTITION BY 關鍵字明確設定分割區索引鍵）
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
- 具有選擇性資料分割的 SQL 及 SQL 資料倉儲：如需詳細資訊，請瀏覽[輸出至 Azure SQL Database 頁面](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf)。

Power BI 不支援資料分割。 不過，您仍然可以分割輸入，如[本節](#multi-step-query-with-different-partition-by-values)中所述 

如需分割區的詳細資訊，請參閱下列文章：

* [事件中樞功能概觀](../event-hubs/event-hubs-features.md#partitions)
* [資料分割](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>窘迫平行作業
在 Azure 串流分析中，*窘迫平行*作業是最具擴充性的案例。 它將對於查詢執行個體之輸入的某個資料分割，連接到輸出的某個資料分割。 此平行處理原則具有下列需求︰

1. 如果查詢邏輯相依於同一個查詢執行個體所處理的相同索引鍵，則您必須確保事件會傳送至輸入的相同分割區。 對於事件中樞或 IoT 中樞，這表示事件資料必須設定 **PartitionKey** 值。 或者，您可以使用分割的傳送者。 對於 Blob 儲存體，這表示事件會傳送至相同的磁碟分割資料夾。 例如，使用 userID 做為分割區索引鍵，將輸入事件中樞分割為每個 userID 的查詢實例會匯總資料。 不過，如果您的查詢邏輯不需要相同的索引鍵來處理相同的查詢實例，您可以忽略這項需求。 簡單的選取-投影-篩選查詢，即為此邏輯的一個例子。  

2. 下一個步驟是讓您的查詢進行分割。 對於相容性層級為1.2 或更高（建議）的作業，可以在輸入設定中將自訂資料行指定為分割區索引鍵，並自動 paralellized 作業。 相容性層級為1.0 或1.1 的作業，會要求您在查詢的所有步驟中使用**PARTITION BY PartitionId** 。 您可以使用多個步驟，但全部都必須依相同的索引鍵來分割。 

3. 串流分析中支援的大部分輸出都可以利用資料分割。 如果您使用不支援資料分割的輸出類型，您的作業將無法*平行窘迫*。 對於事件中樞輸出，請確定資料**分割索引鍵資料行**已設定為查詢中所使用的相同資料分割索引鍵。 如需詳細資訊，請參閱[輸出](#outputs)一節。

4. 輸入分割區的數目必須等於輸出分割區的數目。 Blob 儲存體輸出可支援資料分割，並繼承上游查詢的資料分割配置。 當針對 Blob 儲存體指定資料分割索引鍵時，資料會依每個輸入分割區進行分割，因此結果仍然是完全平行。 以下是允許完全平行作業的分割區值範例：

   * 8 個事件中樞輸入分割區和 8 個事件中樞輸出分割區
   * 8 個事件中樞輸入分割區和 blob 儲存體輸出
   * 8 個事件中樞輸入分割區和 Blob 儲存體輸出，依含有任意基數的自訂欄位分割
   * 8 個 blob 儲存體輸入分割區和 blob 儲存體輸出
   * 8 個 blob 儲存體輸入分割區和 8 個事件中樞輸出分割區

以下幾節討論一些窘迫平行的範例情節。

### <a name="simple-query"></a>簡單查詢

* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有8個分割區的事件中樞（「分割區索引鍵資料行」必須設定為使用 "PartitionId"）

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

此查詢是簡單的篩選。 因此，我們不需要擔心將傳送到事件中樞的輸入分割。 請注意，具有1.2 之前相容性層級的作業必須包含**PARTITION BY PartitionId**子句，因此它滿足了先前所 #2 的需求。 對於輸出，我們必須將作業中的事件中樞輸出設定為讓資料分割索引鍵設為 **PartitionId**。 最後一項檢查是確保輸入分割區數目等於輸出分割區數目。

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

如果輸入資料分割計數不符合輸出資料分割計數，則不論查詢為何，拓撲都不會窘迫平行處理。 不過，我們仍然可以取得一些層級或平行處理。

### <a name="query-using-non-partitioned-output"></a>使用非資料分割的輸出查詢
* 輸入：具有 8 個分割區的事件中樞
* 輸出： Power BI

Power BI 輸出目前不支援資料分割。 因此，此情節不是窘迫平行。

### <a name="multi-step-query-with-different-partition-by-values"></a>具有不同 PARTITION BY 值的多重步驟查詢
* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 8 個分割區的事件中樞
* 相容性層級：1.0 或1。1

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

如您所見，第二個步驟把[TollBoothId] **** 當做資料分割索引鍵來使用。 此步驟和第一個步驟不同，因此需要變換一下。 

### <a name="multi-step-query-with-different-partition-by-values"></a>具有不同 PARTITION BY 值的多重步驟查詢
* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有8個分割區的事件中樞（「分割區索引鍵資料行」必須設定為使用 "TollBoothId"）
* 相容性層級-1.2 或更新版本

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

相容性層級1.2 或更新版本預設會啟用平行查詢執行。 例如，只要將 "TollBoothId" 資料行設定為輸入資料分割索引鍵，上一節的查詢就會進行分割。 不需要 PARTITION BY PartitionId 子句。

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
* 步驟內的查詢必須有**PARTITION BY**關鍵字。

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

[窘迫的平行](#embarrassingly-parallel-jobs)作業是必要的，但不足以承受大規模的輸送量更高。 每個儲存系統及其對應的串流分析輸出都具有如何達到最佳寫入輸送量的變化。 如同任何大規模的案例，您可以使用正確的設定來解決一些挑戰。 本節將討論一些通用輸出的設定，並提供範例，以維持每秒1、2和10K 個事件的的內嵌速率。

下列觀察會使用具有無狀態（passthrough）查詢的串流分析作業，這是寫入事件中樞、Azure SQL DB 或 Cosmos DB 的基本 JavaScript UDF。

#### <a name="event-hub"></a>事件中樞

|攝取速率（每秒的事件數） | 串流處理單位 | 輸出資源  |
|--------|---------|---------|
| 1K     |    1    |  2 TU   |
| 5K     |    6    |  6 TU   |
| 10K    |    12   |  10 TU  |

[事件中樞](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs)解決方案會以串流處理單位（SU）和輸送量進行線性調整，讓它成為分析和串流資料的最有效率且高效能串流分析的方式。 作業可以相應增加到 192 SU，這大約會轉譯為最多 200 MB/s 的處理，或每天19000000000000個事件。

#### <a name="azure-sql"></a>Azure SQL
|攝取速率（每秒的事件數） | 串流處理單位 | 輸出資源  |
|---------|------|-------|
|    1K   |   3  |  S3   |
|    5K   |   18 |  P4   |
|    10K  |   36 |  P6   |

[AZURE SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql)支援以平行方式寫入，稱為繼承資料分割，但預設不會啟用。 不過，啟用 [繼承資料分割] 和 [完全平行查詢] 可能不足以達到較高的輸送量。 SQL write 輸送量明顯取決於您的 SQL Azure 資料庫設定和資料表架構。 [SQL 輸出效能](./stream-analytics-sql-output-perf.md)一文中有更多可將您的寫入輸送量最大化之參數的詳細資料。 如[Azure 串流分析輸出](./stream-analytics-sql-output-perf.md#azure-stream-analytics)中所述，在 Azure SQL Database 文章中，此解決方案無法以線性方式調整為超過8個數據分割的完整平行管線，而且可能[需要在 SQL](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)輸出之前重新分割（請參閱）。 需要 Premium Sku 以維持高 IO 速率，以及每隔幾分鐘發生記錄備份的額外負荷。

#### <a name="cosmos-db"></a>Cosmos DB
|攝取速率（每秒的事件數） | 串流處理單位 | 輸出資源  |
|-------|-------|---------|
|  1K   |  3    | 20K RU  |
|  5K   |  24   | 60K RU  |
|  10K  |  48   | 120K RU |

串流分析的[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)輸出已更新為使用[相容性層級 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12)之下的原生整合。 相較于1.1，相容性層級1.2 可提供明顯更高的輸送量並減少 RU 耗用量，這是新作業的預設相容性層級。 解決方案會使用在/deviceId 上分割的 CosmosDB 容器，而其餘的解決方案則設定相同。

所有[大規模的串流處理 azure 範例](https://github.com/Azure-Samples/streaming-at-scale)會使用透過負載模擬測試用戶端作為輸入來送出的事件中樞。 每個輸入事件都是 1KB JSON 檔，可輕鬆地將設定的內嵌速率轉譯為輸送量速率（1MB/s、5MB/s、10 MB/s）。 事件會模擬 IoT 裝置傳送下列 JSON 資料（以縮短形式）給最多1千個裝置：

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
> 由於解決方案中使用的各種元件，設定可能會變更。 如需更精確的評估，請自訂範例以符合您的案例。

### <a name="identifying-bottlenecks"></a>找出瓶頸

使用 Azure 串流分析作業中的 [計量] 窗格，來識別管線中的瓶頸。 檢查輸送量的**輸入/輸出事件**和「[浮水印延遲](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)」或待處理的**事件**，以查看作業是否與輸入速率保持一致。 針對事件中樞計量，尋找已**節流的要求**，並據以調整閾值單位。 如 Cosmos DB 計量，請參閱輸送量之下**每個分割區索引鍵範圍的最大使用 RU/秒**，以確保您的資料分割索引鍵範圍會一致地取用。 若為 Azure SQL DB，請監視**記錄 IO**和**CPU**。

## <a name="get-help"></a>取得說明

如需進一步的協助，請嘗試我們的[Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure 串流分析](stream-analytics-real-time-fraud-detection.md)
* [Azure 串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
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

