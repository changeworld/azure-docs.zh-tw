---
title: 使用 Visual Studio 中的工作圖表在本機進行 Azure 串流分析查詢的調試
description: 本文說明如何使用 Azure 串流分析工具中的工作圖表，在本機上針對 Visual Studio 進行查詢的偵錯工具。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: d0e94fda1fb21be1a01516f4cecf657426ae867e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019443"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>使用 Visual Studio 中的工作圖表在本機進行 Azure 串流分析查詢的調試

輸出沒有結果或非預期結果的作業，是串流查詢的常見疑難排解案例。 您可以在 Visual Studio 的本機測試查詢時使用作業圖表，以檢查每個步驟的中繼結果集和計量。 當您針對問題進行疑難排解時，作業圖表可協助您快速找出問題的根源。

## <a name="debug-a-query-using-job-diagram"></a>使用作業圖表來進行查詢的調試

Azure 串流分析腳本可用來將輸入資料轉換成輸出資料。 作業圖表會顯示資料如何從輸入來源 (事件中樞、IoT 中樞等，) 通過多個查詢步驟，最後到輸出接收。 每個查詢步驟都會對應到腳本中使用語句所定義的暫存結果集 `WITH` 。 您可以查看每個中繼結果集中每個查詢步驟的資料，以及每個查詢步驟的度量，以找出問題的來源。

> [!NOTE]
> 此工作圖表只會顯示在單一節點中進行本機測試的資料和度量。 它不應該用於效能調整和疑難排解。

### <a name="start-local-testing"></a>開始本機測試

您可以使用本 [快速入門](stream-analytics-quick-create-vs.md) 來瞭解如何使用 Visual Studio 建立串流分析作業，或將 [現有的作業匯出至本機專案](stream-analytics-vs-tools.md#export-jobs-to-a-project)。 如果您想要使用本機輸入資料來測試查詢，請遵循下列 [指示](stream-analytics-live-data-local-testing.md)。 如果您想要使用即時輸入進行測試，請移至下一個步驟。

> [!NOTE]
> 如果您將作業匯出至本機專案，而且想要針對即時輸入資料流程進行測試，您必須再次指定所有輸入的認證。  

從腳本編輯器中選擇輸入和輸出來源，然後選取 [在 **本機執行**]。 工作圖表會出現在右側。

### <a name="view-the-intermediate-result-set"></a>查看中繼結果集  

1. 選取查詢步驟以流覽至腳本。 系統會自動將您導向至左側編輯器中的對應腳本。

   ![作業圖表流覽腳本](./media/debug-locally-using-job-diagram/navigate-script.png)

2. 選取查詢步驟，然後在快顯對話方塊中選取 [ **預覽** ]。 結果集會顯示在下方 [結果] 視窗中的索引標籤中。

   ![作業圖表預覽結果](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>查看步驟計量

在本節中，您會探索圖表每個部分的可用計量。

#### <a name="input-sources-live-stream"></a>輸入來源 (即時串流) 

![作業圖表即時輸入來源](./media/debug-locally-using-job-diagram/live-input.png)

|計量|描述|
|-|-|
|**TaxiRide**| 輸入的名稱。|
|**事件中樞** | 輸入來源類型。|
|**事件**|讀取的事件數目。|
|**囤積的事件來源**|事件中樞和 IoT 中樞輸入需要讀取的訊息數目。|
|**事件（以位元組為單位）**|讀取的位元組數。|
| **降級的事件**|具有還原序列化以外之問題的事件計數。|
|**早期事件**| 在高水位線之前，具有應用程式時間戳記的事件數目。|
|**晚期事件**| 在高水位線之後，具有應用程式時間戳記的事件數目。|
|**事件來源**| 讀取的資料單位數目。 例如，Blob 的數目。|

#### <a name="input-sources-local-input"></a>輸入來源 (本機輸入) 

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
  
#### <a name="output-sinks-live-output"></a>輸出接收 (即時輸出) 

![顯示本機輸出接收的作業圖表。](./media/debug-locally-using-job-diagram/live-output.png)

|計量|描述|
|-|-|
|**regionaggEH**|輸出的名稱。|
|**事件**|接收的事件輸出數目。|

#### <a name="output-sinks-local-output"></a>輸出接收 (本機輸出) 

![作業圖表區域輸出接收](./media/debug-locally-using-job-diagram/local-output.png)

|計量|描述|
|-|-|
|**regionaggEH**|輸出的名稱。|
|**本機輸出**| 將結果輸出到本機檔案。|
|**資料列計數**| 輸出至本機檔案的資料列數目。|
|**資料大小**| 本機檔案的資料輸出大小。|

### <a name="close-job-diagram"></a>關閉作業圖表

如果您不再需要作業圖表，請選取右上角的 [ **關閉** ]。 關閉圖表視窗之後，您需要再次開始本機測試以查看它。

### <a name="view-job-level-metrics-and-stop-running"></a>查看作業層級度量並停止執行

其他工作層級度量會顯示在快顯主控台中。 如果您想要停止作業，請在主控台中按下 **Ctrl + C** 。

![作業圖表停止作業](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>限制

* 因為驗證模型的限制，所以不支援 Power BI 和 Azure Data Lake Storage Gen1 輸出接收。

* 只有雲端輸入選項具備[時間原則](./stream-analytics-time-handling.md)支援，而本機輸入選項則沒有。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Visual Studio 建立串流分析工作](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio 檢視 Azure 串流分析工作](stream-analytics-vs-tools.md)
* [使用適用於 Visual Studio 的 Azure 串流分析工具 (預覽) 在本機測試即時資料](stream-analytics-live-data-local-testing.md)