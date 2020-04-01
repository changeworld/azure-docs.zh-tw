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
ms.openlocfilehash: 305632a0faa1eb7e217e86d36c5159e557df7aaf
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409259"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>對 Azure 串流分析輸出進行疑難排解

本文介紹了 Azure 流分析輸出連接的常見問題、如何排除輸出問題以及如何更正問題。 許多故障排除步驟都需要為流分析作業啟用診斷日誌。 如果未啟用診斷紀錄,請參閱[使用診斷紀錄對 Azure 串流分析進行故障排除](stream-analytics-job-diagnostic-logs.md)。

## <a name="output-not-produced-by-job"></a>作業未產生輸出

1.  對各個輸出使用 [測試連線]**** 按鈕，驗證輸出的連線能力。

2.  檢視 **「監視器」** 選項卡上的[**監視指標**](stream-analytics-monitoring.md)。由於這些值是聚合的,因此指標將延遲幾分鐘。
   * 如果輸入事件大於 0,則作業能夠讀取輸入數據。 如果輸入事件不大於 0,則作業的輸入出現問題。 請參閱[排除輸入連接故障](stream-analytics-troubleshoot-input.md),瞭解如何排除輸入連接問題。
   * 如果資料轉換錯誤大於 0 並爬升,請參閱[Azure 流分析數據錯誤](data-errors.md),瞭解有關資料轉換錯誤的詳細資訊。
   * 如果運行時錯誤大於 0,則作業可以接收數據,但在處理查詢時生成錯誤。 若要找出錯誤，請前往[稽核記錄](../azure-resource-manager/management/view-activity-logs.md)，並篩選*失敗*狀態。
   * 如果輸入事件大於 0,並且輸出事件等於 0,則以下之一為 true:
      * 查詢處理產生了零個輸出事件。
      * 事件或欄位可能格式錯誤,導致查詢處理後輸出為零。
      * 由於連線或驗證問題，作業無法將推送資料至輸出接收端。

   在所有前述錯誤案例中，作業記錄訊息說明了其他詳細資料 (包括目前所發生的事)，僅有查詢邏輯篩選掉所有事件的案例除外。 如果處理多個事件生成錯誤,則每 10 分鐘聚合一次錯誤。

## <a name="job-output-is-delayed"></a>作業輸出延遲

### <a name="first-output-is-delayed"></a>第一個輸出就延遲

串流分析作業啟動時，會讀取輸入事件，但在某些情況下會發生輸出延遲。

時態性查詢元素中的較長時間值可能會造成輸出延遲。 若要針對較長時間範圍產生正確的輸出，串流作業要先讀取最近時間 (最多七天前) 的資料，以填滿時間範圍。 在這段時間，在未處理輸入事件的讀取作業趕上之前，都不會產生任何輸出。 當系統升級串流作業，因此而重新啟動作業時，就會出現此問題。 這類升級通常每隔幾個月會發生一次。

因此，在設計您的串流分析查詢時請慎重考慮。 如果您在作業的查詢語法中針對時態性元素使用較長的時間範圍 (超過幾小時，最多七天)，當作業啟動或重新啟動時，可能會在第一個輸出就延遲。  

針對第一個輸出延遲這類型的一個緩和策略，是使用查詢平行化技術 (分割資料)，或新增更多串流單位來改善輸送量，直到作業趕上為止。  如需詳細資訊，請參閱[建立串流分析作業時的考量](stream-analytics-concepts-checkpoint-replay.md)

這些因素會影響所產生的第一個輸出的時效性：

1. 使用視窗型彙總 (輪轉視窗、跳動視窗和滑動視窗的 GROUP BY)
   - 對於輪轉視窗或跳動視窗的彙總，會在視窗時間範圍的結尾產生結果。
   - 對於滑動視窗，當事件進入或離開滑動視窗時就會產生結果。
   - 如果您打算使用較大的視窗大小 (> 1 小時)，最好選擇跳動視窗或滑動視窗，讓您能夠更頻繁地查看輸出。

2. 使用時態性聯結 (JOIN 搭配 DATEDIFF)
   - 只要相符事件的兩邊都達成時，就會產生相符項目。
   - 相對於左邊的每個事件，缺少相符項目 (LEFT OUTER JOIN) 的資料，會在 DATEDIFF 視窗結尾時產生。

3. 使用時態性分析函數 (ISFIRST、LAST 和 LAG 搭配 LIMIT DURATION)
   - 對於分析函數，每個事件都會產生輸出，不會有任何延遲。

### <a name="output-falls-behind"></a>輸出落後

在作業的一般作業期間，如果您發現作業的輸出落後 (延遲越來越長)，便可以檢查下列因素找出根本原因：
- 下游接收是否已節流
- 上游來源是否已節流
- 查詢中的處理邏輯是否需要大量計算

若要查看這些詳細資料，請在 Azure 入口網站中，選取串流作業，然後選取 [工作圖表]****。 每個輸入都有一個按照分割區區分的待辦項目事件計量。 如果待辦項目事件計量持續增加，則表示系統資源受到限制。 可能是因為有輸出接收節流，或 CPU 很高。 如需使用作業圖表的詳細資訊，請參閱[使用作業圖表進行資料導向偵錯](stream-analytics-job-diagram-with-metrics.md)。

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL Database 輸出的索引鍵違規警告

當您設定 Azure SQL 資料庫作為串流分析作業的輸出時，它會將記錄大量插入至目的地資料表。 一般情況下，Azure 串流分析保證[至少一次傳遞](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)至輸出接收，當 SQL 資料表已定義唯一限制式時，仍然可以[達到準確一次傳遞]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)至 SQL 輸出。

一旦在 SQL 資料表上設定好唯一的索引鍵限制式，如果有重複的記錄插入至 SQL 資料表，Azure 串流分析就會移除重複的記錄。 它會將資料分割為幾個批次，並以遞迴方式插入批次，直到找到單一重複的記錄。 如果串流作業有相當多的重複資料列，這種分割和插入的程序必須一個一個忽略重複的項目，這樣比較沒有效率而且耗時。 如果您在過去一小時內的活動記錄中看到多個索引鍵違規警告訊息，有可能是 SQL 輸出減緩整個作業。

若要解決此問題，您應藉由啟用 IGNORE_DUP_KEY 選項來[設定索引]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) (造成索引鍵違規的索引)。 啟用此選項可讓 SQL 在大量插入期間略過重複值，且 SQL Azure 只會產生警告訊息而非錯誤。 Azure 串流分析不會再產生主索引鍵違規錯誤。

針對數種類型的索引設定 IGNORE_DUP_KEY 時，請注意下列觀察：

* 您無法在主索引鍵或者使用 ALTER INDEX 的唯一限制式上設定 IGNORE_DUP_KEY，您必須卸除而後重新建立索引。  
* 您可以使用 ALTER INDEX 對唯一索引設定 IGNORE_DUP_KEY 選項，這與使用 CREATE INDEX 或 INDEX 定義建立的主索引鍵/唯一限制式不同。  

* 因為您無法在這類索引上強制執行唯一性，所以 IGNORE_DUP_KEY 不適用於資料行存放區索引。  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>列名稱由 Azure 串流分析小寫
使用原始相容性級別 (1.0) 時,Azure 流分析用於將列名稱更改為小寫。 此行為在以後的相容性級別中已修復。 為了保留這種情況,我們建議客戶遷移到相容性級別 1.1 及更高版本。 您可以找到有關 Azure[流分析作業的相容性級別](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)的詳細資訊。

## <a name="get-help"></a>取得說明

有關進一步説明,請嘗試我們的[Azure 流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [使用 Azure 串流分析開始](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
