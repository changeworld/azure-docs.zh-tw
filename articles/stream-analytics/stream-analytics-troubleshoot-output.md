---
title: 對 Azure 串流分析輸出進行疑難排解
description: 本文說明對 Azure 串流分析作業中的輸出連線進行疑難排解的技術。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 10/05/2020
ms.custom: seodec18
ms.openlocfilehash: 6942fd68625fd8eac18ea899330fd99f31f771f7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019832"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>對 Azure 串流分析輸出進行疑難排解

此文章會說明 Azure 串流分析輸出連線的常見問題，以及對輸出問題進行疑難排解的方式。 需要對串流分析作業啟用資源及其他診斷記錄，許多疑難排解步驟才能執行。 如果您未啟用資源記錄，請參閱[使用資源記錄對 Azure 串流分析進行疑難排解](stream-analytics-job-diagnostic-logs.md)。

## <a name="the-job-doesnt-produce-output"></a>作業不會產生輸出

1. 對各個輸出使用 [測試連線] 按鈕，驗證輸出的連線能力。
1. 請查看 [監視] 索引標籤上的 [[監視計量](stream-analytics-monitoring.md)]。因為是彙總值，計量會延遲幾分鐘顯示。

   * 如果 [輸入事件] 值大於零，作業便可以讀取輸入資料。 如果 [輸入事件] 值不大於零，便代表作業的輸入有問題。 請參閱[針對輸入連線進行疑難排解](stream-analytics-troubleshoot-input.md)以取得詳細資訊。 如果您的作業有參考資料輸入，請在查看 **輸入事件** 度量時，依邏輯名稱套用分割。 如果您的參考資料中沒有輸入事件，則可能表示未正確設定此輸入來源以提取正確的參考資料集。
   * 如果 [資料轉換錯誤] 值大於零且持續上升，請參閱 [Azure 串流分析資料錯誤](data-errors.md)以取得資料轉換錯誤的詳細資訊。
   * 如果 [執行階段錯誤] 值大於零，便代表您作業會接收到資料，但在處理查詢時產生錯誤。 若要找出錯誤，請前往[稽核記錄](../azure-resource-manager/management/view-activity-logs.md)，並篩選 [失敗] 狀態。
   * 如果 [輸入事件] 值大於零，且 [輸出事件] 值等於零，則下列其中一個陳述為 true：
      * 查詢處理產生零個輸出事件。
      * 事件或欄位可能格式錯誤，因此在查詢處理後導致零個輸出。
      * 由於連線或驗證問題，作業無法將推送資料至輸出接收端。

   除了查詢邏輯篩選掉所有事件的情況之外，作業記錄訊息會說明其他詳細資料 (包括目前所發生的事)。 如果處理多個事件會產生錯誤，則錯誤每 10 分鐘就會彙總一次。

## <a name="the-first-output-is-delayed"></a>第一個輸出已延遲

當串流分析作業開始時，系統會讀取輸入事件。 但是在某些情況下，輸出中可能會出現延遲。

時態性查詢元素中的較長時間值可能會造成輸出延遲。 若要針對較長時間範圍產生正確的輸出，串流作業會盡可能讀取最近時間的資料，以填滿時間範圍。 該資料最多可能是七天之前。 在系統讀取未處理的輸入事件之前，將不會產生任何輸出。 當系統升級串流作業時，可能會出現此問題。 進行升級時，作業會重新啟動。 這類升級通常每隔幾個月會發生一次。

在設計您的串流分析查詢時請慎重考慮。 如果您在作業的查詢語法中針對時態性元素使用較長的時間範圍，當作業啟動或重新啟動時，可能會在第一個輸出就會延遲。 超過數個小時，最多七天的時間，視為較長的時間範圍。

適用於這種第一個輸出延遲的其中一種緩解方式，便是使用分割資料之類的查詢平行處理技術。 或者，您可以加入更多串流單位來改善輸送量，直到作業趕上進度為止。  如需詳細資訊，請參閱[建立串流分析作業時的考量](stream-analytics-concepts-checkpoint-replay.md)。

這些因素會影響第一個輸出的時效性：

* 使用視窗型彙總，例如輪轉視窗、跳動視窗和滑動視窗的 GROUP BY 子句：

  * 對於輪轉視窗或跳動視窗的彙總，會在時間範圍的結尾產生結果。
  * 對於滑動視窗，當事件進入或離開滑動視窗時就會產生結果。
  * 如果您打算使用較長的範圍大小 (例如超過一個小時)，最好選擇跳動視窗或滑動視窗。 這些視窗類型可讓您更頻繁地查看輸出。

* 使用時態性聯結，例如 JOIN 搭配 DATEDIFF：
  * 當兩邊相符的事件都到達時，就會產生相符項目。
  * 缺乏相符項目的資料 (例如 LEFT OUTER JOIN) 會在 DATEDIFF 時間範圍的結尾，針對左側的每個事件產生。

* 使用時態性分析函數，例如 ISFIRST、LAST 和 LAG 搭配 LIMIT DURATION：
  * 對於分析函數，會針對每個事件產生輸出。 不會有任何延遲。

## <a name="the-output-falls-behind"></a>輸出落後

在作業 (Job) 的一般作業 (Operation) 期間，輸出的延遲時間可能會越來越長。 如果輸出像這樣出現落後，您可以透過檢查下列因素來找出根本原因：

* 下游接收是否已節流
* 上游來源是否已節流
* 查詢中的處理邏輯是否需要大量計算

若要查看輸出詳細資料，請在 Azure 入口網站中選取串流作業，然後選取 [工作圖表]。 針對每個輸入，每個分割區都會有待辦項目事件計量。 如果計量持續增加，則表示系統資源受到限制。 此增加可能是因為輸出接收節流，或是高 CPU 使用率。 如需詳細資訊，請參閱[使用作業圖表進行資料驅動偵錯](stream-analytics-job-diagram-with-metrics.md)。

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL Database 輸出的索引鍵違規警告

當您設定 Azure SQL Database 作為串流分析作業的輸出時，其會將記錄大量插入至目的地資料表。 一般而言，Azure 串流分析會保證對輸出接收進行[至少一次的傳遞](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) \(英文\)。 在 SQL 資料表已定義唯一條件約束的情況下，您仍然可以對 SQL 輸出[達成剛好一次的傳遞]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) \(英文\)。

當您在 SQL 資料表上設定唯一索引鍵條件約束時，Azure 串流分析會移除重複的記錄。 其會將資料分割為幾個批次，並以遞迴方式插入批次，直到找到單一重複的記錄。 分割和插入程序會一次忽略一個重複項目。 針對具有許多重複資料列的串流作業，此程序既沒效率且耗時。 如果您在過去一小時內於活動記錄中看到多個索引鍵違規警告訊息，很有可能是 SQL 輸出使整個作業變慢。

若要解決此問題，請啟用 IGNORE_DUP_KEY 選項來[設定造成索引鍵違規的索引]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)。 此選項可讓 SQL 在大量插入期間忽略重複的值。 Azure SQL Database 只會產生警告訊息，而不會產生錯誤。 因此，Azure 串流分析便不再會產生主索引鍵違規錯誤。

針對數種類型的索引設定 IGNORE_DUP_KEY 時，請注意下列觀察：

* 您無法在主索引鍵或是使用 ALTER INDEX 的唯一條件約束上設定 IGNORE_DUP_KEY。 您必須卸除索引並重新加以建立。  
* 您可以針對唯一索引使用 ALTER INDEX 來設定 IGNORE_DUP_KEY。 此執行個體與 PRIMARY KEY/UNIQUE 條件約束不同，並且是使用 CREATE INDEX 或 INDEX 定義所建立。  
* IGNORE_DUP_KEY 選項不適用於資料行存放區索引，因為您無法在其上方強制執行唯一性。

## <a name="sql-output-retry-logic"></a>SQL 輸出重試邏輯

當具有 SQL 輸出的串流分析作業收到第一個批次的事件時，會發生下列步驟：

1. 作業嘗試連接至 SQL。
2. 作業會提取目的地資料表的架構。
3. 作業會根據目的地資料表架構來驗證資料行名稱和類型。
4. 此作業會從批次中的輸出記錄準備記憶體中的資料表。
5. 作業會使用 BulkCopy [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy.writetoserver)將資料表寫入至 SQL。

在這些步驟中，SQL 輸出可能會遇到下列類型的錯誤：

* 使用指數輪詢重試策略重試的暫時性 [錯誤](../azure-sql/database/troubleshoot-common-errors-issues.md#transient-fault-error-messages-40197-40613-and-others) 。 最小重試間隔取決於個別的錯誤碼，但間隔通常小於60秒。 上限最多可以是五分鐘。 

   [登入失敗](../azure-sql/database/troubleshoot-common-errors-issues.md#unable-to-log-in-to-the-server-errors-18456-40531) 和 [防火牆問題](../azure-sql/database/troubleshoot-common-errors-issues.md#cannot-connect-to-server-due-to-firewall-issues) 在上一次嘗試後至少會重試5分鐘，然後重試直到成功為止。

* 資料錯誤（例如轉換錯誤和架構條件約束違規）會以輸出錯誤原則來處理。 這些錯誤的處理方式是重試二進位分割批次，直到會由 skip 或 retry 處理造成錯誤的個別記錄為止。 [一律會處理](./stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output)主要的唯一索引鍵條件約束違規。

* 當有 SQL 服務問題或內部程式碼缺失時，可能會發生非暫時性錯誤。 例如，當 (代碼1132之類的錯誤) 彈性集區達到其儲存空間限制時，重試不會解決錯誤。 在這些案例中，串流分析作業的效能會 [降低](job-states.md)。
* `BulkCopy` 在步驟5期間可能會發生超時狀況 `BulkCopy` 。 `BulkCopy` 可能會偶爾遇到作業超時。 預設的最小設定 timeout 為五分鐘，當連續叫用時，會加倍。
一旦 timeout 超過15分鐘，最大的批次大小提示 `BulkCopy` 會降至一半，直到每個批次的100事件保留為止。

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Azure 串流分析 (1.0) 中的資料行名稱為小寫

使用原始的相容性層級 (1.0) 時，Azure 串流分析會將資料行名稱變更為小寫。 此行為已在較新的相容性層級中修正。 若要保留大小寫，請移至相容性層級 1.1 或更新版本。 如需詳細資訊，請參閱[串流分析作業的相容性層級](./stream-analytics-compatibility-level.md) \(部分機器翻譯\)。

## <a name="get-help"></a>取得說明

如需進一步的協助，請嘗試 [Azure 串流分析的 Microsoft 問與答頁面](/answers/topics/azure-stream-analytics.html)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 串流分析查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference) \(英文\)
* [Azure 串流分析管理 REST API 參考](/rest/api/streamanalytics/)