---
title: 對 Azure 串流分析輸出進行疑難排解
description: 本文說明對 Azure 串流分析作業中的輸出連線進行疑難排解的技術。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: bee40bc30a0ffbdacf8cc7bf88d1512c4fc43fa4
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147940"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>對 Azure 串流分析輸出進行疑難排解

本文說明 Azure 串流分析輸出連線的常見問題，以及如何針對輸出問題進行疑難排解。 許多疑難排解步驟都需要針對您的串流分析作業啟用資源和其他診斷記錄。 如果您未啟用資源記錄，請參閱[使用資源記錄針對 Azure 串流分析進行疑難排解](stream-analytics-job-diagnostic-logs.md)。

## <a name="the-job-doesnt-produce-output"></a>作業不會產生輸出

1. 對各個輸出使用 [測試連線]**** 按鈕，驗證輸出的連線能力。
1. 查看 [**監視**] 索引標籤上的 [[監視計量](stream-analytics-monitoring.md)]。因為這些值已匯總，所以計量會延遲幾分鐘的時間。

   * 如果**輸入事件**值大於零，則作業可以讀取輸入資料。 如果**輸入事件**值不大於零，則作業的輸入會有問題。 如需詳細資訊，請參閱針對[輸入連接進行疑難排解](stream-analytics-troubleshoot-input.md)。
   * 如果**資料轉換錯誤**值大於零且已上升，請參閱[Azure 串流分析資料錯誤](data-errors.md)，以取得有關資料轉換錯誤的詳細資訊。
   * 如果**執行階段錯誤**值大於零，則您的作業會接收資料，但在處理查詢時產生錯誤。 若要找出錯誤，請移至 [[審核記錄](../azure-resource-manager/management/view-activity-logs.md)]，然後篩選 [**失敗**] 狀態。
   * 如果**輸入事件**值大於零，且**輸出事件**值等於零，下列其中一個語句為 true：
      * 查詢處理產生零個輸出事件。
      * 事件或欄位的格式可能不正確，因此在查詢處理後產生零輸出。
      * 由於連線或驗證問題，作業無法將推送資料至輸出接收端。

   作業記錄訊息會說明其他詳細資料，包括發生的情況，但查詢邏輯篩選掉所有事件的情況除外。 如果多個事件的處理產生錯誤，則每隔10分鐘會匯總錯誤。

## <a name="the-first-output-is-delayed"></a>第一個輸出延遲

當串流分析作業啟動時，會讀取輸入事件。 但在某些情況下，輸出中可能會有延遲。

時態性查詢元素中的較長時間值可能會造成輸出延遲。 為了在大型時間範圍內產生正確的輸出，串流作業會從最新的時間讀取資料，以填滿時間間隔。 資料最多可超過七天。 在讀取未處理的輸入事件之前，不會產生任何輸出。 當系統升級串流作業時，就會出現這個問題。 進行升級時，作業會重新開機。 這類升級通常每隔幾個月會發生一次。

設計串流分析查詢時，請謹慎使用。 如果您在作業的查詢語法中針對時態性元素使用較長的時間範圍，當作業開始或重新開機時，可能會導致第一個輸出的延遲。 超過數小時，最多七天，視為較長的時間範圍。

這種第一次輸出延遲的一個緩和方式是使用查詢平行處理技術，例如分割資料。 或者，您可以新增更多串流處理單位來改善輸送量，直到作業趕上為止。  如需詳細資訊，請參閱[建立串流分析作業時的考慮](stream-analytics-concepts-checkpoint-replay.md)。

這些因素會影響第一個輸出的時效性：

* 使用視窗型匯總，例如輪轉、跳動和滑動視窗的 GROUP BY 子句：

  * 針對輪轉或跳動視窗匯總，結果會在視窗時間範圍結束時產生。
  * 若為滑動視窗，當事件進入或離開滑動視窗時，就會產生結果。
  * 如果您打算使用較大的視窗大小，例如超過一小時，最好選擇跳動或滑動視窗。 這些視窗類型可讓您更頻繁地查看輸出。

* 使用時態性聯結，例如與 DATEDIFF 聯結：
  * 一旦相符事件的兩邊到達，就會比對產生。
  * 缺少相符專案的資料（例如左方外部聯結）是在 DATEDIFF 視窗的結尾產生，針對左邊的每個事件。

* 使用時態性分析函式，例如 ISFIRST、LAST 和 LAG，限制持續時間：
  * 針對分析函數，會針對每個事件產生輸出。 沒有延遲。

## <a name="the-output-falls-behind"></a>輸出落在之後

在正常作業中，輸出可能會有較長時間和較長的延遲時間。 如果輸出落後于這樣，您可以藉由檢查下列因素來找出根本原因：

* 下游接收是否已節流
* 上游來源是否已節流
* 查詢中的處理邏輯是否需要大量計算

若要查看輸出詳細資料，請在 [Azure 入口網站中選取串流作業，然後選取 [**作業圖表**]。 針對每個輸入，每個資料分割都有一個待處理專案（backlog）事件度量。 如果計量持續增加，這就是系統資源受到限制的指標。 增加的原因可能是輸出接收節流，或 CPU 使用率過高。 如需詳細資訊，請參閱[使用作業圖表進行資料驅動的偵錯工具](stream-analytics-job-diagram-with-metrics.md)。

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL Database 輸出的索引鍵違規警告

當您將 Azure SQL database 設定為串流分析作業的輸出時，它會將記錄大量插入目的地資料表。 一般而言，Azure 串流分析保證至少[一次傳遞](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)至輸出接收。 當 SQL 資料表已定義 unique 條件約束時，您仍然可以[完成一次性傳遞]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)至 sql 輸出。

當您在 SQL 資料表上設定唯一索引鍵條件約束時，Azure 串流分析會移除重複的記錄。 它會將資料分割成批次，並以遞迴方式插入批次，直到找到單一重複記錄為止。 分割和插入進程會一次忽略一個重複的專案。 對於具有多個重復資料列的串流作業，此程式沒有效率且耗時。 如果您在過去一小時的活動記錄中看到多個金鑰違規警告訊息，您的 SQL 輸出可能會使整個作業變慢。

若要解決此問題，請啟用 [IGNORE_DUP_KEY] 選項，以設定導致金鑰違規的[索引]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)。 此選項可讓 SQL 在大量插入期間忽略重複的值。 Azure SQL Database 只會產生警告訊息，而不是錯誤。 因此，Azure 串流分析不再產生主鍵違規錯誤。

針對數種類型的索引設定 IGNORE_DUP_KEY 時，請注意下列觀察：

* 您無法在使用 ALTER INDEX 的 primary KEY 或 unique 條件約束上設定 IGNORE_DUP_KEY。 您必須卸載並重新建立索引。  
* 您可以使用唯一索引的 ALTER INDEX 來設定 IGNORE_DUP_KEY。 這個實例不同于 PRIMARY KEY/UNIQUE 條件約束，而且是使用 CREATE INDEX 或 INDEX 定義來建立的。  
* [IGNORE_DUP_KEY] 選項不會套用到資料行存放區索引，因為您無法對其強制執行唯一性。  

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Azure 串流分析中的資料行名稱是小寫（1.0）

使用原始相容性層級（1.0）時，Azure 串流分析會將資料行名稱變更為小寫。 此行為已在較新的相容性層級中修正。 若要保留大小寫，請移至相容性層級1.1 或更新版本。 如需詳細資訊，請參閱[串流分析作業的相容性層級](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)。

## <a name="get-help"></a>取得說明

如需進一步的協助，請嘗試我們的[Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure 串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
