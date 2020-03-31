---
title: 使用 Azure 串流分析中的查詢平行化和調整作業
description: 本文說明如何透過設定輸入資料分割、微調查詢定義，及設定工作串流處理單元來調整串流分析工作。
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 31ac43ec796d305b8a8f4b62ea09481e262b6b3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256975"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>利用 Azure 串流分析中的查詢平行化作業
本文會示範如何利用 Azure 串流分析中的平行化作業。 您可以了解如何透過設定輸入資料分割並調整分析查詢定義來調整串流分析工作。
先決條件是，您必須熟悉[了解及調整串流處理單位](stream-analytics-streaming-unit-consumption.md)中所述的串流處理單位概念。

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>串流分析工作由哪些部分所組成？
串流分析工作的定義包含輸入、查詢及輸出。 輸入是指作業讀取資料流的來源。 查詢是用來轉換資料輸入資料流，而輸出是作業傳送作業結果的目的地。

一個工作至少需要一個輸入來源來進行資料串流。 資料流輸入來源可以儲存在 Azure 事件中樞或 Azure Blob 儲存體中。 如需詳細資訊，請參閱 [Azure 串流分析簡介](stream-analytics-introduction.md)和[開始使用 Azure 串流分析](stream-analytics-real-time-fraud-detection.md)。

## <a name="partitions-in-sources-and-sinks"></a>來源與接收的資料分割
調整串流分析作業需要利用輸入或輸出中的分割區。 資料分割可讓您根據分割索引鍵，將資料分成子集。 取用資料的流程 (例如串流分析作業) 可以平行取用和寫入不同的分割區，這樣可以增加輸送量。 

### <a name="inputs"></a>輸入
所有 Azure 串流分析輸入都可以利用資料分割：
-   事件中樞 (需要明確地使用 PARTITION BY 關鍵字設定分割區索引鍵)
-   IoT 中樞 (需要明確地使用 PARTITION BY 關鍵字設定分割區索引鍵)
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
「窘迫平行」** 作業是我們在 Azure 串流分析中調整性最高的情節。 它將對於查詢執行個體之輸入的某個資料分割，連接到輸出的某個資料分割。 此平行處理原則具有下列需求︰

1. 如果查詢邏輯相依於同一個查詢執行個體所處理的相同索引鍵，則您必須確保事件會傳送至輸入的相同分割區。 對於事件中樞或 IoT 中樞，這表示事件資料必須設定 **PartitionKey** 值。 或者，您可以使用分割的傳送者。 對於 Blob 儲存體，這表示事件會傳送至相同的磁碟分割資料夾。 如果查詢邏輯並不需要同一個查詢執行個體所處理的相同索引鍵，您可以忽略這項需求。 簡單的選取-投影-篩選查詢，即為此邏輯的一個例子。  

2. 當資料放在輸入端時，您必須確保查詢已分割。 這要求您在所有步驟中使用**分區 BY。** 您可以使用多個步驟，但全部都必須依相同的索引鍵來分割。 在相容性級別 1.0 和 1.1 下，分區金鑰必須設置為**分區 Id，** 以使作業完全並行。 對於相容性級別為 1.2 和更高的作業，可以在輸入設置中指定自訂列作為分區鍵，即使沒有分區 BY 子句，該作業也將自動被拋對。 對於事件中心輸出，必須將屬性"分區鍵列"設置為使用"分區 Id"。

3. 我們大部分的輸出都可以利用資料分割，不過，如果您使用不支援資料分割的輸出類型，您的作業將無法進行平行處理。 對於事件中心輸出，請確保**分區鍵列**設置為與查詢分區鍵相同。 如需詳細資訊，請參閱[輸出](#outputs)一節。

4. 輸入分割區的數目必須等於輸出分割區的數目。 Blob 儲存體輸出可支援資料分割，並繼承上游查詢的資料分割配置。 當針對 Blob 儲存體指定資料分割索引鍵時，資料會依每個輸入分割區進行分割，因此結果仍然是完全平行。 以下是允許完全平行作業的分割區值範例：

   * 8 個事件中樞輸入分割區和 8 個事件中樞輸出分割區
   * 8 個事件中樞輸入分割區和 blob 儲存體輸出
   * 8 個事件中樞輸入分割區和 Blob 儲存體輸出，依含有任意基數的自訂欄位分割
   * 8 個 blob 儲存體輸入分割區和 blob 儲存體輸出
   * 8 個 blob 儲存體輸入分割區和 8 個事件中樞輸出分割區

以下幾節討論一些窘迫平行的範例情節。

### <a name="simple-query"></a>簡單查詢

* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 8 個分區的事件中心（必須將"分區鍵列"設置為使用"分區 Id"）

查詢：

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

此查詢是簡單的篩選。 因此，我們不需要擔心將傳送到事件中樞的輸入分割。 請注意，在 1.2 之前具有相容性級別的作業必須包含**分區 BY 分區 Id**子句，因此它滿足之前#2的要求。 對於輸出，我們必須將作業中的事件中樞輸出設定為讓資料分割索引鍵設為 **PartitionId**。 最後一項檢查是確保輸入分割區數目等於輸出分割區數目。

### <a name="query-with-a-grouping-key"></a>利用群組索引鍵的查詢

* 輸入：具有 8 個分割區的事件中樞
* 輸出：Blob 儲存體

查詢：

```SQL
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

在此案例中，查詢並不重要。 如果輸入資料分割計數不符合輸出資料分割計數，則拓撲不是窘迫平行。不過，我們仍然可以獲得一定程度的平行化。

### <a name="query-using-non-partitioned-output"></a>使用非資料分割的輸出查詢
* 輸入：具有 8 個分割區的事件中樞
* 輸出：功率 BI

Power BI 輸出目前不支援資料分割。 因此，此情節不是窘迫平行。

### <a name="multi-step-query-with-different-partition-by-values"></a>具有不同 PARTITION BY 值的多重步驟查詢
* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 8 個分割區的事件中樞

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

上述範例示範一些符合 (或不符合) 窘迫平行拓撲的串流分析作業。 如果符合，則可能有最大調整幅度。 對於不符合其中一個設定檔的作業，則提供未來更新時的調整指引。 現在，請在下列各節中使用一般指引。

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>相容性級別 1.2 - 具有不同分區 BY 值的多步驟查詢 
* 輸入：具有 8 個分割區的事件中樞
* 輸出：具有 8 個分區的事件中心（必須將"分區鍵列"設置為使用"TollBoothId"）

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

預設情況下，相容性級別 1.2 支援並行查詢執行。 例如，只要將"TollBoothId"列設置為輸入分區鍵，上一節中的查詢就會分區。 分區 BY 分區 Id 子句不是必需的。

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
* 步驟中的查詢必須具有**分區 BY**關鍵字。

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

## <a name="achieving-higher-throughputs-at-scale"></a>大規模實現更高的輸送量

[令人尷尬的並行](#embarrassingly-parallel-jobs)作業是必要的，但不足以維持更高的輸送量。 每個存儲系統及其相應的流分析輸出在如何實現最佳寫入輸送量方面都有變化。 與任何規模方案一樣，使用正確的配置可以解決一些挑戰。 本節討論一些常見輸出的配置，並提供用於保持每秒 1K、5K 和 10K 事件的攝取速率的示例。

以下觀察結果使用具有無狀態（直通）查詢的流分析作業，這是一個基本的 JavaScript UDF，它寫入事件中心、Azure SQL DB 或 Cosmos DB。

#### <a name="event-hub"></a>事件中樞

|攝取速率（每秒事件） | 串流處理單位 | 輸出資源  |
|--------|---------|---------|
| 1K     |    1    |  2 TU   |
| 5K     |    6    |  6 TU   |
| 10K    |    12   |  10 TU  |

[事件中心](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs)解決方案以流式處理單元 （SU） 和輸送量線性擴展，使其成為分析和流分析中分析資料的最有效和最有效的方法。 作業可以擴展到 192 SU，這大致相當於處理高達 200 MB/s，或每天 19 萬億次事件。

#### <a name="azure-sql"></a>Azure SQL
|攝取速率（每秒事件） | 串流處理單位 | 輸出資源  |
|---------|------|-------|
|    1K   |   3  |  S3   |
|    5K   |   18 |  P4   |
|    10K  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql)支援並行寫入，稱為繼承分區，但預設情況下未啟用。 但是，啟用繼承分區以及完全並行查詢可能不足以實現更高的輸送量。 SQL 寫入輸送量在很大程度上取決於 SQL Azure 資料庫配置和表架構。 [SQL 輸出性能](./stream-analytics-sql-output-perf.md)文章詳細介紹了可最大化寫入輸送量的參數。 如 Azure[流分析輸出到 Azure SQL 資料庫](./stream-analytics-sql-output-perf.md#azure-stream-analytics)一文中所述，此解決方案不會線性擴展為超過 8 個分區的完全並行管道，並且可能需要在 SQL 輸出之前重新分區（請參閱[INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)）。 需要高級 SKU 來維持高 IO 速率，以及每隔幾分鐘進行一次記錄備份的開銷。

#### <a name="cosmos-db"></a>Cosmos DB
|攝取速率（每秒事件） | 串流處理單位 | 輸出資源  |
|-------|-------|---------|
|  1K   |  3    | 20K RU  |
|  5K   |  24   | 60K RU  |
|  10K  |  48   | 120K RU |

來自流分析的[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)輸出已更新為在[相容性級別 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12)下使用本機集成。 與 1.1 相比，相容性級別 1.2 可實現更高的輸送量並減少 RU 消耗，而 1.1 是新作業的預設相容性級別。 該解決方案使用在 /deviceId 上分區的 CosmosDB 容器，其餘解決方案配置相同。

[縮放 Azure 採樣下的所有流式處理](https://github.com/Azure-Samples/streaming-at-scale)都使用通過負載模擬測試用戶端作為輸入而饋送的事件中心。 每個輸入事件都是一個 1KB 的 JSON 文檔，它可輕鬆將配置的引入速率轉換為吞吐率（1MB/s、5MB/s 和 10MB/s）。 事件類比 IoT 設備為多達 1K 設備發送以下 JSON 資料（以縮短的形式）：

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
> 由於解決方案中使用的各種元件，配置可能會發生變化。 要獲得更準確的估計，請自訂示例以適合您的方案。

### <a name="identifying-bottlenecks"></a>找出瓶頸

使用 Azure 流分析作業中的"指標"窗格來識別管道中的瓶頸。 查看**輸入/輸出事件**中的輸送量和["浮水印延遲"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)或 **"積壓事件**"，以查看作業是否跟上輸入速率。 對於事件中心指標，請查找**限制請求**並相應地調整閾值單位。 對於 Cosmos DB 指標，請查看"輸送量"下**每個分區鍵範圍的最大值消耗的 RU/s，** 以確保分區金鑰範圍被統一使用。 對於 Azure SQL DB，請監視**日誌 IO**和**CPU**。

## <a name="get-help"></a>取得說明

有關進一步説明，請嘗試我們的[Azure 流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [使用 Azure 流分析開始](stream-analytics-real-time-fraud-detection.md)
* [Azure 流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
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

