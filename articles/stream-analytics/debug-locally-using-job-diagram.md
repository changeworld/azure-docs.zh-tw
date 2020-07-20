---
title: 使用 Visual Studio 中的工作圖表在本機上進行 Azure 串流分析查詢的調試
description: 本文說明如何使用適用于 Visual Studio 的 Azure 串流分析工具中的作業圖表，在本機對查詢進行偵錯工具。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 86367f1dce3cc8040555827935e9ca2f9f9fb4c5
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045394"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>使用 Visual Studio 中的工作圖表在本機上進行 Azure 串流分析查詢的調試

輸出沒有結果或非預期結果的作業，是串流查詢的常見疑難排解案例。 您可以使用作業圖表，同時在 Visual Studio 中測試查詢，以檢查每個步驟的中繼結果集和計量。 當您針對問題進行疑難排解時，作業圖表可以協助您快速找出問題的來源。

## <a name="debug-a-query-using-job-diagram"></a>使用作業圖表來進行查詢的調試

Azure 串流分析腳本是用來將輸入資料轉換成輸出資料。 作業圖表會顯示從輸入來源（事件中樞、IoT 中樞等）到多個查詢步驟的資料流程，最後是輸出接收的方式。 每個查詢步驟都會對應至使用語句在腳本中定義的暫存結果集 `WITH` 。 您可以在每個中繼結果集中查看資料以及每個查詢步驟的度量，以找出問題的來源。

> [!NOTE]
> 此作業圖表只會在單一節點中顯示本機測試的資料和計量。 它不應該用來進行效能調整和疑難排解。

### <a name="start-local-testing"></a>開始本機測試

使用本[快速入門](stream-analytics-quick-create-vs.md)來瞭解如何使用 Visual Studio 建立串流分析作業，或將[現有的作業匯出至本機專案](stream-analytics-vs-tools.md#export-jobs-to-a-project)。 如果您想要使用本機輸入資料來測試查詢，請遵循這些[指示](stream-analytics-live-data-local-testing.md)。 如果您想要使用即時輸入進行測試，請移至下一個步驟。

> [!NOTE]
> 如果您將作業匯出至本機專案，並想要針對即時輸入資料流程進行測試，則需要重新指定所有輸入的認證。  

從腳本編輯器中選擇輸入和輸出來源，然後選取 [在**本機執行**]。 作業圖表會出現在右側。

### <a name="view-the-intermediate-result-set"></a>查看中繼結果集  

1. 選取查詢步驟以流覽至腳本。 系統會自動將您導向至左側編輯器中的對應腳本。

   ![作業圖表導覽腳本](./media/debug-locally-using-job-diagram/navigate-script.png)

2. 選取查詢步驟，然後在快顯對話方塊中選取 [**預覽**]。 結果集會顯示在下方結果視窗中的索引標籤上。

   ![作業圖表預覽結果](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>查看步驟計量

在本節中，您會探索圖表的每個部分可用的計量。

#### <a name="input-sources-live-stream"></a>輸入來源（即時串流）

![作業圖表即時輸入來源](./media/debug-locally-using-job-diagram/live-input.png)

|計量|描述|
|-|-|
|**TaxiRide**| 輸入的名稱。|
|**事件中樞** | 輸入來源類型。|
|**事件**|讀取的事件數目。|
|**待的事件來源**|事件中樞和 IoT 中樞輸入需要讀取的訊息數目。|
|**以位元組為單位的事件**|讀取的位元組數。|
| **降級的事件**|具有反還原序列化以外之問題的事件計數。|
|**早期事件**| 在高水位線之前具有應用程式時間戳記的事件數目。|
|**晚期事件**| 在高水位線之後具有應用程式時間戳記的事件數目。|
|**事件來源**| 讀取的資料單位數目。 例如，Blob 的數目。|

#### <a name="input-sources-local-input"></a>輸入來源（本機輸入）

![作業圖表本機輸入來源](./media/debug-locally-using-job-diagram/local-input.png)

|計量|描述|
|-|-|
|**TaxiRide**| 輸入的名稱。|
|**資料列計數**| 從步驟產生的資料列數目。|
|**資料大小**| 此步驟所產生的資料大小。|
|**本機輸入**| 使用本機資料做為輸入。|

#### <a name="query-steps"></a>查詢步驟

![作業圖表查詢步驟](./media/debug-locally-using-job-diagram/query-step.png)

|計量|描述|
|-|-|
|**TripData**|暫存結果集的名稱。|
|**資料列計數**| 從步驟產生的資料列數目。|
|**資料大小**| 此步驟所產生的資料大小。|
  
#### <a name="output-sinks-live-output"></a>輸出接收（即時輸出）

![作業圖表本機輸出接收](./media/debug-locally-using-job-diagram/live-output.png)

|計量|描述|
|-|-|
|**regionaggEH**|輸出的名稱。|
|**事件**|輸出至接收的事件數目。|

#### <a name="output-sinks-local-output"></a>輸出接收（本機輸出）

![作業圖表本機輸出接收](./media/debug-locally-using-job-diagram/local-output.png)

|計量|描述|
|-|-|
|**regionaggEH**|輸出的名稱。|
|**本機輸出**| 將結果輸出到本機檔案。|
|**資料列計數**| 輸出至本機檔案的資料列數目。|
|**資料大小**| 輸出至本機檔案的資料大小。|

### <a name="close-job-diagram"></a>關閉作業圖表

如果您不再需要作業圖表，請選取右上角的 [**關閉**]。 關閉圖表視窗之後，您必須再次開始進行本機測試，才能看到它。

### <a name="view-job-level-metrics-and-stop-running"></a>查看作業層級計量並停止執行

其他作業層級計量會顯示在快顯視窗中。 如果您想要停止作業，請在主控台中按**Ctrl + C** 。

![作業圖表停止作業](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>限制

* 因為驗證模型限制，所以不支援 Power BI 和 Azure Data Lake Storage Gen1 輸出接收。

* 只有雲端輸入選項具備[時間原則](stream-analytics-out-of-order-and-late-events.md)支援，而本機輸入選項則沒有。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Visual Studio 建立串流分析工作](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio 檢視 Azure 串流分析工作](stream-analytics-vs-tools.md)
* [使用適用於 Visual Studio 的 Azure 串流分析工具 (預覽) 在本機測試即時資料](stream-analytics-live-data-local-testing.md)
