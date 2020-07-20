---
title: 使用 Visual Studio Code 中的工作圖表在本機上進行 Azure 串流分析查詢的調試
description: 本文說明如何使用適用于 Visual Studio Code 的 Azure 串流分析延伸模組中的工作圖表，在本機對查詢進行偵錯工具。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: c31f3c998df918466e707c95f041592051e8251c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045309"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>使用 Visual Studio Code 中的工作圖表在本機上進行 Azure 串流分析查詢的調試

輸出沒有結果或非預期結果的串流作業通常需要進行疑難排解。 適用于 Azure 的 Visual Studio Code 延伸模組串流分析整合了作業圖表、計量、診斷記錄和中繼結果，協助您快速找出問題的來源。 您可以使用作業圖表，同時在本機測試查詢，以檢查每個步驟的中繼結果集和計量。

## <a name="debug-a-query-using-job-diagram"></a>使用作業圖表來進行查詢的調試

Azure 串流分析腳本是用來將輸入資料轉換成輸出資料。 作業圖表會顯示資料如何透過多個查詢步驟，從輸入來源（例如事件中樞或 IoT 中樞）流動到輸出接收。 每個查詢步驟都會對應至使用語句在腳本中定義的暫存結果集 `WITH` 。 您可以在每個中繼結果集中查看資料以及每個查詢步驟的度量，以找出問題的來源。

> [!NOTE]
> 此作業圖表只會在單一節點中顯示本機測試的資料和計量。 它不應該用來進行效能調整和疑難排解。

### <a name="start-local-testing"></a>開始本機測試

使用本[快速入門](quick-create-vs-code.md)來瞭解如何使用 Visual Studio Code 建立串流分析作業，或將[現有的作業匯出至本機專案](visual-studio-code-explore-jobs.md)。 系統會針對匯出的作業自動填入輸入和輸出的認證。

如果您想要使用本機輸入資料來測試查詢，請遵循這些[指示](visual-studio-code-local-run.md)。 如果您想要使用即時輸入進行測試，請[設定您的輸入](stream-analytics-add-inputs.md)，移至下一個步驟。 

開啟* \. script.asaql*腳本檔案，然後選取 [在**本機執行**]。 然後，選取 [**使用本機輸入**] 或 [**使用即時輸入**]。 作業圖表會出現在視窗的右側。

### <a name="view-the-output-and-intermediate-result-set"></a>查看輸出和中繼結果集  

1. 所有作業輸出都會顯示在 [結果] 視窗的 [Visual Studio Code] 視窗右下方。

   > [!div class="mx-imgBorder"]
   > ![作業輸出結果](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. 選取查詢步驟以流覽至腳本。 系統會自動將您導向至左側編輯器中的對應腳本。 中繼結果會出現在 [結果] 視窗的 [Visual Studio Code] 視窗右下方。

   > [!div class="mx-imgBorder"]
   > ![作業圖表預覽結果](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>檢視計量

在本節中，您會探索圖表的每個部分可用的計量。

1. 選取 Visual Studio Code 視窗右下方的 [**結果**] 索引標籤旁邊的 [**計量**] 索引標籤。

2. 從下拉式清單中選取 [**作業**]。 您可以選取圖形節點中的任何空白空間，以流覽至作業層級計量。 此視圖包含所有的計量，這些度量會在作業執行時每10秒更新一次。 您可以選取或取消選取右側的計量，以在圖表中進行查看。

   > [!div class="mx-imgBorder"]
   > ![作業圖表計量](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. 從下拉式清單中選取輸入資料來源的名稱，以查看輸入計量。 下列螢幕擷取畫面中的輸入來源稱為「*引號*」。 如需輸入計量的詳細資訊，請參閱[瞭解串流分析作業監視和如何監視查詢](stream-analytics-monitoring.md)。

   > [!div class="mx-imgBorder"]
   > ![作業圖表計量](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. 從作業圖表中選取查詢步驟，或從下拉式清單中選取 [步驟名稱]，以查看步驟層級計量。 浮水印延遲是唯一可用的步驟度量。

   > [!div class="mx-imgBorder"]
   > ![步驟計量](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. 選取圖表中的輸出，或從下拉式清單中，以查看輸出相關的計量。 如需輸出計量的詳細資訊，請參閱[瞭解串流分析作業監視和如何監視查詢](stream-analytics-monitoring.md)。 不支援即時輸出接收。

   > [!div class="mx-imgBorder"]
   > ![輸出計量](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>檢視診斷記錄

作業層級診斷記錄包含輸入資料來源和輸出接收的診斷資訊。 當您選取 [輸入] 節點或 [輸出] 節點時，只會顯示對應的記錄。 如果您選取查詢步驟，則不會顯示任何記錄。 您可以在作業層級找到所有記錄，也可以依嚴重性和時間篩選記錄。

   > [!div class="mx-imgBorder"]
   > ![診斷記錄](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   選取記錄專案以查看整個訊息。

   > [!div class="mx-imgBorder"]
   > ![診斷記錄訊息](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>其他作業圖表功能

您可以視需要選取工具列中的 [**停止**] 或 [**暫停**]。 作業暫停之後，您就可以從最後一個輸出中繼續執行。

> [!div class="mx-imgBorder"]
> ![停止或暫停作業](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

選取作業圖表右上方的 [**作業摘要**]，以查看本機作業的屬性和設定。

> [!div class="mx-imgBorder"]
> ![本機作業摘要](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>限制

* 本機執行中不支援即時輸出接收。

* 只有在 Windows 作業系統上才支援使用 JavaScript 函數在本機執行作業。

* 不支援 c # 自訂程式碼和 Azure Machine Learning 函數。 

* 只有雲端輸入選項具有[時間原則](stream-analytics-out-of-order-and-late-events.md)支援，而本機輸入選項則否。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Visual Studio Code 建立串流分析作業](quick-create-vs-code.md)
* [使用 Visual Studio Code 探索 Azure 串流分析](visual-studio-code-explore-jobs.md)
* [使用 Visual Studio Code 和範例資料在本機測試串流分析查詢](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 在本機測試 Azure 串流分析作業和即時輸入](visual-studio-code-local-run-live-input.md)
