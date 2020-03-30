---
title: Azure 流分析中的流單元
description: 本文說明串流單位設定，以及其他會影響 Azure 串流分析效能的因素。
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 397e455c8b6a1097e2a32473036e1acd2bbdf2eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267348"
---
# <a name="understand-and-adjust-streaming-units"></a>了解及調整串流單位

流式處理單元 （SUs） 表示分配給執行流分析作業的計算資源。 SU 的數目愈大，為您的作業配置的 CPU 和記憶體資源就愈多。 這個容量可讓您專注於查詢邏輯，並以及時的方式摘要出管理硬體以執行串流分析作業的需求。

為了達到低延遲的串流處理，Azure 串流分析作業會在記憶體中執行所有處理。 當記憶體用完時，串流工作將會失敗。 因此，對於生產作業來說，請務必監視串流作業的資源使用狀況，並配置足夠的資源讓作業保持全天候運作。

SU % 使用率計量介於 0% 到 100% 的範圍間，可說明工作負載的記憶體耗用量。 就使用量最低的串流作業而言，此計量通常會介於 10% 到 20% 之間。 如果 SU % 使用率偏低，且有輸入事件待處理，您的工作負載就可能需要更多計算資源，而您就必須增加 SU 數目。 建議您最好將 SU 計量保持低於 80%，以因應偶發的尖峰使用量。 Microsoft 建議對 80% 的 SU 使用量計量設定警示，以避免資源耗盡。 如需詳細資訊，請參閱[教學課程：設定 Azure 串流分析作業的警示](stream-analytics-set-up-alerts.md)。

## <a name="configure-stream-analytics-streaming-units-sus"></a>設定串流分析串流單位 (SU)
1. 登錄到[Azure 門戶](https://portal.azure.com/)

2. 在資源清單中，尋找並開啟您想要調整的串流分析作業。 

3. 在作業頁面的 [設定]**** 標題下，選取 [縮放]****。 

    ![Azure 入口網站串流分析作業組態][img.stream.analytics.preview.portal.settings.scale]
    
4. 使用滑桿來設定作業的 SU。 請注意，您只能調整特定的 SU 設定。 
5. 即使作業正在運行，也可以更改分配給作業的 S 編號。 如果作業使用[非分區輸出](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#query-using-non-partitioned-output)或[具有具有不同分區 BY 值的多步驟查詢](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#multi-step-query-with-different-partition-by-values)，則無法執行此操作。 您可能僅限於在作業運行時從一組 SU 值中進行選擇。 

## <a name="monitor-job-performance"></a>監視工作效能
您可以使用 Azure 入口網站來追蹤作業的輸送量：

![Azure 串流分析監視工作][img.stream.analytics.monitor.job]

計算工作負載的預期輸送量。 如果輸送量少於預期，請調整輸入分割區、調整查詢，並將 SU 新增至作業。

## <a name="how-many-sus-are-required-for-a-job"></a>一個作業需要多少 SU？

選擇特定作業所需的 SU 數量取決於輸入的磁碟分割組態以及作業內定義的查詢。 [調整]**** 頁面可讓您設定正確的 SU 數目。 最好作法是配置比所需數目還多的 SU。 串流處理引擎會不惜分派額外的記憶體，針對延遲和輸送量進行最佳化。

一般情況下，最佳的作法是對不是使用 **PARTITION BY** 的查詢使用 6 個 SU 開始。 然後使用反覆嘗試的方法來決定最佳配置，這方法就是您可以在傳送代表的資料總數後修改 SU 數目，並且檢查 SU% 使用率計量。 串流分析作業可使用的串流單位數目上限，取決於為作業定義的查詢中包含的步驟數目，和每個步驟中的分割區數目。 您可以在[這裡](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job)深入了解限制。

如需選擇正確 SU 數目的詳細資訊，請參閱頁面：[調整 Azure 串流分析工作以增加輸送量](stream-analytics-scale-jobs.md)

> [!Note]
> 選擇特定工作所需的 SU 數量，取決於輸入的分割組態和針對作業所定義的查詢。 您為作業選取的 SU 以您的配額為上限。 預設情況下，每個 Azure 訂閱對於特定區域中的所有分析作業最多具有 500 個 S。 若要為您的訂用帳戶增加超出此配額的 SU，請連絡 [Microsoft 支援服務](https://support.microsoft.com)。 每個作業有效的 SU 值為 1、3、6 和更大 (以 6 為增量單位)。

## <a name="factors-that-increase-su-utilization"></a>增加 SU% 使用量的因素 

時態性 (時間導向) 查詢元素是由串流分析提供的一組核心具狀態運算子。 串流分析會在服務升級期間管理記憶體耗用量、復原的檢查點和狀態復原，代替使用者在內部管理這些作業的狀態。 即使串流分析會完全管理狀態，但還是有許多使用者應該考慮的最佳做法建議。

請注意，具有複雜查詢邏輯的作業即使在非連續接收輸入事件時，也可能具有較高的 SU% 使用率。 此情況可能發生在輸入和輸出事件突然激增之後。 如果查詢很複雜，作業可能會繼續維持在記憶體中的狀態。

SU% 使用率可能會突然降到 0，但不久後便回到預期的層級。 這是因為暫時性錯誤或系統啟動的升級造成的。 如果查詢不完全[並行](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)，則增加作業的流式處理單元數可能不會降低 SU% 利用率。

在比較一段時間內的利用率時，請使用[事件速率指標](stream-analytics-monitoring.md)。 輸入事件和輸出事件指標顯示讀取和處理的事件數。 也有指示錯誤事件數的指標，例如反序列化錯誤。 當每個時間單位的事件數增加時，SU% 在大多數情況下會增加。

## <a name="stateful-query-logicin-temporal-elements"></a>時態性元素中的具狀態查詢邏輯
Azure 串流分析作業的其中一個獨特功能是執行具狀態的處理工作，如視窗型彙總、時態性聯結及時態性分析函式。 每個運算子都會保留狀態資訊。這些查詢元素的時間範圍上限是七天。 

時間範圍概念出現在數個「串流分析」查詢元素中：
1. 視窗型彙總：輪轉視窗、跳動視窗和滑動視窗的 GROUP BY

2. 時態性聯結：JOIN 搭配 DATEDIFF 函數

3. 時態性分析函數：ISFIRST、LAST 和 LAG 搭配 LIMIT DURATION

下列因素會影響串流分析作業用的記憶體 (串流單位計量的一部份)：

## <a name="windowed-aggregates"></a>視窗型彙總
視窗型彙總耗用的記憶體 (狀態大小) 不一定與視窗大小成正比。 相反地，耗用的記憶體是與資料的基數成正比，或每個時間視窗中的群組數目成正比。


例如，在下列查詢中，與 `clusterid` 相關的數字是查詢基數。 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

為了緩解由上一個查詢中的高基數引起的任何問題，可以將事件發送到由 分區`clusterid`的事件中心，並通過允許系統使用分區**BY**單獨處理每個輸入分區來擴展查詢，如下例所示：

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

一旦查詢已分割時，便會分散到多個節點。 如此一來，進入到每個節點的 `clusterid` 數目便會降低，因此也降低了運算子群組的基數。 

事件中樞分割區應依據群組索引鍵來分割，以避免需要進行減量步驟。 如需詳細資訊，請參閱[事件中樞概觀](../event-hubs/event-hubs-what-is-event-hubs.md)。 

## <a name="temporal-joins"></a>時態性聯結
時序聯接消耗的記憶體（狀態大小）與聯接的時間擺動室中的事件數成正比，即事件輸入速率乘以擺動室大小。 換句話說，聯結所耗用的記憶體是與 DateDiff 時間範圍乘以平均事件速率成正比。

在聯結中不相符的事件數目，會影響查詢的記憶體使用率。 下列查詢用來尋找能產生點擊率的廣告曝光項目︰

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

在此範例中，可能有大量廣告顯示，但僅有少數人會點選，而且仍然需要在時間範圍中保留所有事件。 視窗大小和事件出現率與記憶體耗用程度成正比。 

要修復此情況，請將事件發送到由聯接鍵（本例中為 ID）分區的事件中心，並通過允許系統使用**分區 BY**單獨處理每個輸入分區來擴展查詢，如下所示：

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

一旦查詢已分割時，便會分散到多個節點。 如此一來，進入到每個節點的事件數目便會降低，因此也降低了聯結時間範圍內保留的狀態大小。 

## <a name="temporal-analytic-functions"></a>時態性分析函數
時態性分析函數耗用的記憶體 (狀態大小)，與事件速率乘以持續時間成正比。分析函數耗用的記憶體不是與視窗大小成正比，而是與每個時間範圍的分割計數成正比。

修復方法與時態性聯結相似。 您可以使用 **PARTITION BY** 向外延展查詢。 

## <a name="out-of-order-buffer"></a>次序錯誤緩衝區 
使用者可以在 [事件順序] 組態窗格中設定次序錯誤緩衝區大小。 緩衝區可用來保留時間範圍持續時間的輸入，並將它們重新排序。 緩衝區的大小，與事件輸入速率乘以次序錯誤時間範圍大小成正比。 預設的時間範圍大小為 0。 

若要補救次序錯誤緩衝區的溢位，請使用 **PARTITION BY** 相應放大查詢。 一旦查詢已分割時，便會分散到多個節點。 如此一來，進入到每個節點的事件數目便會降低，因此也降低了每個重新排列緩衝區中的事件數目。 

## <a name="input-partition-count"></a>輸入分割區計數 
每個輸入分割區的作業輸入都有一個緩衝區。 輸入分割區的數目越大，作業耗用的資源也就越多。 對於每個串流單位，Azure 串流分析可以處理大約 1 MB/s 的輸入。 因此，您可以將串流分析的串流單位數目與事件中樞中的分割區數目比對來最佳化。 

一般而言，設定一個串流單位的作業對於有兩個分割區的事件中樞 (這是事件中樞的最小值) 而言已經足夠。 如果事件中樞有更多分割區，您的串流分析作業便會耗用更多資源，但不一定會使用事件中樞提供的額外輸送量。 

對於有 6 個串流單位的作業來說，您的事件中樞可能需要 4 或 8 個分割區。 不過，請避免使用太多不必要的分割區，因為可能會導致過多的資源使用量。 例如，在有 1 個串流單位的串流分析作業中，有 16 個分割區或更多的事件中樞。 

## <a name="reference-data"></a>參考資料 
ASA 中的參考資料會載入記憶體中，以供快速查閱。 針對目前的實作，每個與參考資料聯結的聯結作業都會在記憶體中保留一份參考資料，即使您多次聯結同樣的參考資料也是如此。 對於使用 **PARTITION BY** 的查詢，每個分割區都有一份參考資料，因此分割區是完全分離的。 在乘數效應之下，如果您多次聯結參考資料與多個分割區，記憶體使用量將會急速攀升。  

### <a name="use-of-udf-functions"></a>使用 UDF 函式
當您新增 UDF 函式時，Azure 串流分析會將 JavaScript 執行階段載入記憶體。 這會影響 SU%。

## <a name="next-steps"></a>後續步驟
* [在 Azure 串流分析中建立可平行查詢](stream-analytics-parallelization.md)
* [擴展 Azure 流分析作業以提高輸送量](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
