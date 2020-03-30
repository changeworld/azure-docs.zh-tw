---
title: 使用視覺化工作室中的作業圖在本地調試 Azure 流分析查詢
description: 本文介紹如何使用視覺化工作室的 Azure 流分析工具中的作業圖在本地調試查詢。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76847195"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>使用視覺化工作室中的作業圖在本地調試 Azure 流分析查詢

輸出沒有結果或意外結果的作業是流式查詢的常見故障排除方案。 您可以在 Visual Studio 中本地測試查詢時使用作業圖來檢查每個步驟的中間結果集和指標。 作業圖可以説明您在疑難排解時快速隔離問題的根源。

## <a name="debug-a-query-using-job-diagram"></a>使用作業圖調試查詢

Azure 流分析腳本用於將輸入資料轉換為輸出資料。 作業圖顯示資料如何從輸入源（事件中心、IoT 中心等）通過多個查詢步驟流，最後流到輸出接收器。 每個查詢步驟都映射到使用`WITH`語句在腳本中定義的臨時結果集。 您可以查看每個中間結果集中每個查詢步驟的資料和指標，以查找問題的根源。

> [!NOTE]
> 此作業圖僅顯示單個節點中本地測試的資料和指標。 它不應用於性能調優和故障排除。

### <a name="start-local-testing"></a>開始本地測試

使用此[快速入門](stream-analytics-quick-create-vs.md)瞭解如何使用 Visual Studio 創建流分析作業或[將現有作業匯出到本地專案](stream-analytics-vs-tools.md#export-jobs-to-a-project)。 如果要使用本地輸入資料測試查詢，請按照這些[說明操作](stream-analytics-live-data-local-testing.md)。 如果要使用即時輸入進行測試，則轉到下一步。

> [!NOTE]
> 如果將作業匯出到本地專案，並且希望針對即時輸入流進行測試，則需要再次指定所有輸入的憑據。  

從腳本編輯器中選擇輸入和輸出源，然後選擇 **"本地運行**"。 作業圖顯示在右側。

### <a name="view-the-intermediate-result-set"></a>查看中間結果集  

1. 選擇要導航到腳本的查詢步驟。 您會自動定向到左側編輯器中的相應腳本。

   ![作業圖導航腳本](./media/debug-locally-using-job-diagram/navigate-script.png)

2. 選取查詢步驟，並在彈出對話方塊中選擇 **"預覽**"。 結果集顯示在底部結果視窗中的選項卡中。

   ![作業圖預覽結果](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>查看步進指標

在本節中，您將探討圖表每個部分可用的指標。

#### <a name="input-sources-live-stream"></a>輸入源（即時流）

![作業圖即時輸入源](./media/debug-locally-using-job-diagram/live-input.png)

|計量|描述|
|-|-|
|**計程車**| 輸入的名稱。|
|**事件中心** | 輸入源類型。|
|**事件**|讀取的事件數。|
|**積壓事件源**|事件中心和 IoT 中心輸入需要讀取多少條消息。|
|**位元組中的事件**|讀取的位元組數。|
| **降級事件**|除反序列化之外，具有問題的事件計數。|
|**早期活動**| 高水位線之前具有應用程式時間戳記的事件數。|
|**後置事件**| 高水位線後具有應用程式時間戳記的事件數。|
|**事件來源**| 讀取的資料單位數。 例如，Blob 的數目。|

#### <a name="input-sources-local-input"></a>輸入源（本地輸入）

![作業圖本地輸入源](./media/debug-locally-using-job-diagram/local-input.png)

|計量|描述|
|-|-|
|**計程車**| 輸入的名稱。|
|**資料列計數**| 從步驟生成的行數。|
|**資料大小**| 從此步驟生成的資料的大小。|
|**本地輸入**| 使用本地資料作為輸入。|

#### <a name="query-steps"></a>查詢步驟

![作業圖查詢步驟](./media/debug-locally-using-job-diagram/query-step.png)

|計量|描述|
|-|-|
|**行程資料**|臨時結果集的名稱。|
|**資料列計數**| 從步驟生成的行數。|
|**資料大小**| 從此步驟生成的資料的大小。|
  
#### <a name="output-sinks-live-output"></a>輸出接收器（即時輸出）

![作業圖本地輸出接收器](./media/debug-locally-using-job-diagram/live-output.png)

|計量|描述|
|-|-|
|**地區拉格赫**|輸出的名稱。|
|**事件**|輸出到接收器的事件數。|

#### <a name="output-sinks-local-output"></a>輸出接收器（本地輸出）

![作業圖本地輸出接收器](./media/debug-locally-using-job-diagram/local-output.png)

|計量|描述|
|-|-|
|**地區拉格赫**|輸出的名稱。|
|**本地輸出**| 結果輸出到本地檔。|
|**資料列計數**| 輸出到本地檔的行數。|
|**資料大小**| 資料輸出到本地檔的大小。|

### <a name="close-job-diagram"></a>關閉作業圖

如果不再需要作業圖，請在右上角選擇 **"關閉**"。 關閉關係圖視窗後，需要再次啟動本地測試才能看到它。

### <a name="view-job-level-metrics-and-stop-running"></a>查看作業級別指標並停止運行

其他作業級別指標顯示在彈出主控台中。 如果要停止作業，請按主控台中的**Ctrl+C。**

![作業圖停止作業](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>限制

* 由於身份驗證模型限制，不支援 Power BI 和 Azure 資料存儲庫第 1 代輸出接收器。

* 只有雲端輸入選項具備[時間原則](stream-analytics-out-of-order-and-late-events.md)支援，而本機輸入選項則沒有。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Visual Studio 建立串流分析工作](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio 檢視 Azure 串流分析工作](stream-analytics-vs-tools.md)
* [使用適用於 Visual Studio 的 Azure 串流分析工具 (預覽) 在本機測試即時資料](stream-analytics-live-data-local-testing.md)
