---
title: 對 Azure 串流分析查詢進行疑難排解
description: 本文說明對 Azure 串流分析作業中的查詢進行疑難排解的技術。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 0dc36b817d5b5cdf731edecd64e1879c153d866a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015125"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>對 Azure 串流分析查詢進行疑難排解

本文說明開發串流分析查詢的常見問題，以及對其進行疑難排解的方式。

本文說明開發 Azure 串流分析查詢時的常見問題、如何對查詢問題進行疑難排解，以及如何修正問題。 需要對串流分析作業啟用資源記錄，許多疑難排解步驟才能執行。 如果您未啟用資源記錄，請參閱[使用資源記錄對 Azure 串流分析進行疑難排解](stream-analytics-job-diagnostic-logs.md)。

## <a name="query-is-not-producing-expected-output"></a>查詢未產生預期的輸出

1.  在本機執行測試以檢查錯誤：

    - 在 Azure 入口網站的 [查詢] 索引標籤上，選取 [測試]。 使用下載的範例資料[測試查詢](stream-analytics-test-query.md)。 檢查是否有任何錯誤並嘗試修正。   
    - 您也可以使用適用於 Visual Studio 的 Azure 串流分析工具或 [Visual Studio Code](visual-studio-code-local-run-live-input.md)，[在本機測試查詢](stream-analytics-live-data-local-testing.md)。 

2.  使用 Azure 串流分析工具中的工作圖表，在本機進行 Visual Studio Code 的[偵錯工具逐步](debug-locally-using-job-diagram-vs-code.md)解說。 作業圖表顯示資料如何從輸入來源 (事件中樞、IoT 中樞等)，經過多個查詢步驟，最後流向輸出接收。 每個查詢步驟都使用 WITH 陳述式，對應到指令碼中定義的暫存結果集。 您可以在每個中繼結果集檢視資料和計量，以找出問題的來源。

    ![作業圖表預覽結果](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

3.  如果您使用 [**Timestamp By**](/stream-analytics-query/timestamp-by-azure-stream-analytics)，請確定事件有大於 [作業開始時間](./stream-analytics-time-handling.md)的時間戳記。

4.  排除常見的錯誤，例如︰
    - 查詢中的 [**WHERE**](/stream-analytics-query/where-azure-stream-analytics) 子句篩選出所有事件，造成無法產生任何輸出作業。
    - [**CAST**](/stream-analytics-query/cast-azure-stream-analytics) 函式失敗，導致作業失敗。 若要避免 cast 類型的失敗，請改為使用 [**TRY_CAST**](/stream-analytics-query/try-cast-azure-stream-analytics)。
    - 當您使用視窗函式時，請等候完整的視窗運作時間，以查看查詢的輸出。
    - 事件的時間戳記早於作業開始時間，因此會捨棄事件。
    - [**JOIN**](/stream-analytics-query/join-azure-stream-analytics) 條件不相符。 如果沒有相符項目，則不會有輸出。

5.  確定事件排序原則已如預期設定。 移至 [設定]，然後選取 [事件排序][](./stream-analytics-time-handling.md)。 如果您使用 [測試] 按鈕測試查詢，則不會套用原則。 此結果是在瀏覽器中進行測試與在生產環境中執行作業之間的一個差異。 

6. 使用活動和資源記錄來偵錯：
    - 使用[活動記錄](../azure-resource-manager/management/view-activity-logs.md)，篩選找出錯誤並進行偵錯。
    - 使用[作業資源記錄](stream-analytics-job-diagnostic-logs.md)找出錯誤並進行偵錯。

## <a name="resource-utilization-is-high"></a>資源使用率偏高

請確實利用 Azure 串流分析中的平行處理。 您將了解如何藉由設定輸入分割區並調整分析查詢定義，為串流分析作業[調整查詢平行處理](stream-analytics-parallelization.md)。

如果資源使用率一致超過80%，則會增加水位線的延遲，而待處理的事件數目不斷增加，請考慮增加串流單位。 高使用率表示作業使用接近已配置資源的最大值。

## <a name="debug-queries-progressively"></a>以漸進方式對查詢偵錯

在即時的資料處理中，了解資料在查詢進行中所呈現的樣子將會相當有幫助。 您可以在 Visual Studio 中使用作業圖表來看到資料。 如果您沒有 Visual Studio，您可以採取額外的步驟來輸出中間資料。

由於 Azure 串流分析作業的輸入或步驟可以讀取多次，因此您可以寫入額外的 SELECT INTO 陳述式。 這種方式會將中繼資料輸出到儲存體，讓您可以檢查資料的正確性，就像您對程式進行偵錯時 *監看變數* 的動作一樣。

下列 Azure 串流分析作業中的查詢範例有一個串流輸入、兩個的參考資料輸入和要傳送至 Azure 資料表儲存體的輸出。 查詢會聯結事件中樞的資料和兩個參考 Blob 以取得名稱和類別資訊︰

![範例串流分析 SELECT INTO 查詢](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

請注意，雖然作業正在執行，但不會在輸出中產生任何事件。 在 [監視]圖格上 (如此處所示)，您可以看到輸入正在產生資料，但您無法知道哪一個 **聯結** 步驟會造成所有資料遭到捨棄。

![串流分析監視圖格](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

在此情況下，您可以新增一些額外的 SELECT INTO 陳述式以「記錄」中繼「聯結」結果及從輸入中讀取的資料。

在此範例中，我們新增了兩個新的「暫存輸出」。 這些暫存輸出可以是您喜歡的任何接收體。 在這裡我們使用 Azure 儲存體作為範例︰

![新增額外的 SELECT INTO 陳述式到串流分析查詢](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

然後您可以重新撰寫查詢，如下所示︰

![重寫 SELECT INTO 串流分析查詢](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

現在請再次啟動作業，並讓它執行幾分鐘的時間。 然後使用 Visual Studio Cloud Explorer 查詢 temp1 和 temp2 以產生下列表格︰

**temp1 資料表**
![SELECT INTO temp1 資料表串流分析查詢](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 資料表**
![SELECT INTO temp2 資料表串流分析查詢](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

如您所見，temp1 和 temp2 都有資料，且 temp2 的名稱資料欄已正確地填入。 不過，因為輸出中仍沒有資料，可能有些地方出了問題︰

![SELECT INTO output1 資料表 (沒有資料) 串流分析查詢](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

藉由資料取樣，您幾乎可以確定問題出自第二個「聯結」。 您可以從 Blob 下載參考資料，以及查看下列項目︰

![SELECT INTO ref 資料表串流分析查詢](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

如您所見，此參考資料中的 GUID 格式與 temp2 中的 [from] 資料欄格式不同。 這就是為什麼資料並未如預期般送達 output1。

您可以修正資料格式並上傳至參考 Blob，然後再試一次︰

![SELECT INTO temp 資料表串流分析查詢](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

此時，輸出中的資料已如預期般進行格式化並填入。

![SELECT INTO final 資料表串流分析查詢](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>取得說明

如需進一步的協助，請嘗試 [Azure 串流分析的 Microsoft 問與答頁面](/answers/topics/azure-stream-analytics.html)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](/rest/api/streamanalytics/)
