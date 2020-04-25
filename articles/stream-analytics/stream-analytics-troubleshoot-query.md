---
title: 對 Azure 串流分析查詢進行疑難排解
description: 本文說明對 Azure 串流分析作業中的查詢進行疑難排解的技術。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: a55515be478781a2f2448924c209a3348ae462c5
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133318"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>對 Azure 串流分析查詢進行疑難排解

本文說明開發串流分析查詢的常見問題，以及對其進行疑難排解的方式。

本文說明開發 Azure 串流分析查詢、如何針對查詢問題進行疑難排解，以及如何修正問題的常見問題。 許多疑難排解步驟都需要針對您的串流分析作業啟用資源記錄。 如果您沒有啟用資源記錄，請參閱[使用資源記錄針對 Azure 串流分析進行疑難排解](stream-analytics-job-diagnostic-logs.md)。

## <a name="query-is-not-producing-expected-output"></a>查詢未產生預期的輸出

1.  在本機執行測試以檢查錯誤：

    - 在 Azure 入口網站的 [**查詢**] 索引標籤上，選取 [**測試**]。 使用下載的範例資料[測試查詢](stream-analytics-test-query.md)。 檢查是否有任何錯誤並嘗試修正。   
    - 您也可以使用適用于 Visual Studio 或[Visual Studio Code](visual-studio-code-local-run-live-input.md)的 Azure 串流分析工具，在[本機測試您的查詢](stream-analytics-live-data-local-testing.md)。 

2.  在適用于 Visual Studio 的 Azure 串流分析工具中，[使用工作圖表在本機逐步執行 Debug 查詢](debug-locally-using-job-diagram.md)。 作業圖表會顯示資料如何透過多個查詢步驟和最後到輸出接收，從輸入來源（事件中樞、IoT 中樞等）流動。 每個查詢步驟都會使用 WITH 語句對應到腳本中定義的暫存結果集。 您可以在每個中繼結果集中查看資料和計量，以尋找問題的來源。

    ![作業圖表預覽結果](./media/debug-locally-using-job-diagram/preview-result.png)

3.  如果您使用 [**Timestamp By**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)，請確定事件有大於[作業開始時間](stream-analytics-out-of-order-and-late-events.md)的時間戳記。

4.  排除常見的錯誤，例如︰
    - 查詢中的 [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) 子句篩選出所有事件，造成無法產生任何輸出作業。
    - [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) 函式失敗，導致作業失敗。 若要避免 cast 類型的失敗，請改為使用 [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics)。
    - 當您使用視窗函式時，請等候完整的視窗運作時間，以查看查詢的輸出。
    - 事件的時間戳記會在工作開始時間之前，並捨棄事件。
    - [**聯結**](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics)條件不相符。 如果沒有相符專案，則會有零個輸出。

5.  確定事件排序原則已如預期設定。 移至 [**設定**]，然後選取 [[**事件排序**](stream-analytics-out-of-order-and-late-events.md)]。 如果您使用 [測試]**** 按鈕測試查詢，則不會** 套用原則。 此結果是在瀏覽器中進行測試與在生產環境中執行作業之間的一個差異。 

6. 使用活動和資源記錄進行偵錯工具：
    - 使用[活動記錄](../azure-resource-manager/resource-group-audit.md)，並進行篩選以識別錯誤和進行偵錯工具。
    - 使用[作業資源記錄](stream-analytics-job-diagnostic-logs.md)來識別和偵測錯誤。

## <a name="resource-utilization-is-high"></a>資源使用率偏高

請確實利用 Azure 串流分析中的平行處理。 您將了解如何藉由設定輸入分割區並調整分析查詢定義，為串流分析作業[調整查詢平行處理](stream-analytics-parallelization.md)。

## <a name="debug-queries-progressively"></a>以漸進方式對查詢偵錯

在即時的資料處理中，了解資料在查詢進行中所呈現的樣子將會相當有幫助。 您可以使用 Visual Studio 中的作業圖表來查看這項功能。 如果您沒有 Visual Studio，您可以採取額外的步驟來輸出中繼資料。

由於 Azure 串流分析作業的輸入或步驟可以讀取多次，因此您可以寫入額外的 SELECT INTO 陳述式。 這種方式會將中繼資料輸出到儲存體，讓您可以檢查資料的正確性，就像您對程式進行偵錯時*監看變數*的動作一樣。

下列 Azure 串流分析作業中的查詢範例有一個串流輸入、兩個的參考資料輸入和要傳送至 Azure 資料表儲存體的輸出。 查詢會聯結事件中樞的資料和兩個參考 Blob 以取得名稱和類別資訊︰

![範例串流分析 SELECT INTO 查詢](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

請注意，雖然作業正在執行，但不會在輸出中產生任何事件。 在 [監視]**** 圖格上 (如此處所示)，您可以看到輸入正在產生資料，但您無法知道哪一個**聯結**步驟會造成所有資料遭到捨棄。

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

如需進一步的協助，請嘗試我們的[Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure 串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
