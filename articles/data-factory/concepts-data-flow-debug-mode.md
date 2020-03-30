---
title: 映射資料流程偵錯模式
description: 在建置資料流程時啟動互動式偵錯工作階段
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 18848695327a374f12fbe5a34d03366b050d8b65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928353"
---
# <a name="mapping-data-flow-debug-mode"></a>映射資料流程偵錯模式

## <a name="overview"></a>總覽

Azure 資料工廠映射資料流程的偵錯模式允許您在生成和調試資料流程時以對話模式監視資料形狀轉換。 調試會話既可用於資料流程設計會話，也可用於資料流程的管道調試執行。 要打開偵錯模式，請使用設計介面頂部的"資料流程調試"按鈕。

![調試滑塊](media/data-flow/debugbutton.png "調試滑塊")

打開滑塊後，系統將提示您選擇要使用的集成運行時配置。 如果選擇了 AutoResolve 集成執行時間，則具有 8 個常規計算核心的群集，其執行時間為 60 分鐘。 有關資料流程集成運行時的詳細資訊，請參閱[資料流程性能](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime)。

![調試紅外選擇](media/data-flow/debugbutton2.png "調試紅外選擇")

當偵錯模式打開時，您將使用活動 Spark 群集以對話模式構建資料流程。 在 Azure Data Factory 中關閉偵錯後，將會關閉此工作階段。 您應該留意在您已開啟偵錯工作階段的期間內，由 Azure Databricks 所產生的每小時費用。

在大多數情況下，最好在偵錯模式下構建資料流程，以便在在 Azure 資料工廠中發佈工作之前驗證業務邏輯並查看資料轉換。 使用管道面板上的"調試"按鈕測試管道中的資料流程。

## <a name="cluster-status"></a>叢集狀態

當群集準備進行調試時，設計介面頂部的群集狀態指示器將變為綠色。 如果您的叢集已經暖機，則綠色指示器幾乎會立即出現。 如果群集在進入偵錯模式時尚未運行，則必須等待 5-7 分鐘，群集才能啟動。 指示燈將旋轉，直到準備就緒。

完成調試後，關閉調試開關，以便 Azure 資料塊群集可以終止，並且不再為調試活動計費。

## <a name="debug-settings"></a>偵錯設定

可以通過按一下"資料流程"畫布工具列上的"調試設置"來編輯調試設置。 您可以在此處選擇要用於每個源轉換的行限制或檔源。 此設置中的行限制僅適用于當前調試會話。 您還可以選擇要用於 SQL DW 源的過渡連結服務。 

![偵錯設定](media/data-flow/debug-settings.png "偵錯設定")

如果資料流程或其任何參考資料集中具有參數，則可以通過選擇 **"參數"** 選項卡來指定在調試期間要使用的值。

![調試設置參數](media/data-flow/debug-settings2.png "調試設置參數")

## <a name="data-preview"></a>資料預覽

若開啟偵錯，[資料預覽] 索引標籤在底部面板上亮起。 如果不打開偵錯模式，資料流程將僅在"檢查"選項卡中顯示進出每個轉換的當前中繼資料。資料預覽將僅查詢在調試設置中設置為限制的行數。 按一下 **"刷新"** 以獲取資料預覽。

![資料預覽](media/data-flow/datapreview.png "資料預覽")

> [!NOTE]
> 檔源僅限制您看到的行，而不是要讀取的行。 對於非常大的資料集，建議您獲取該檔的一小部分並將其用於測試。 您可以在"調試設置"中為檔資料集類型的每個源選擇暫存檔案。

在資料流程的偵錯模式中執行時，您的資料不會寫入至接收轉換。 調試會話旨在用作轉換的測試控管。 偵錯期間不需要接收，而且會在資料流程中忽略。 如果要測試在 Sink 中寫入資料，請從 Azure 資料工廠管道執行資料流程，並使用管道中的調試執行。

### <a name="testing-join-conditions"></a>測試聯結條件

當單元測試聯接、存在或查找轉換時，請確保為測試使用一小組已知資料。 您可以使用上面的"調試設置"選項來設置用於測試的暫存檔案。 這是必需的，因為在限制或採樣大型資料集中的行時，無法預測將哪些行以及哪些鍵將讀取到流中進行測試。 結果是非確定性的，這意味著您的聯結條件可能會失敗。

### <a name="quick-actions"></a>快速動作

看到資料預覽後，可以生成快速轉換以鍵入、刪除或對列進行修改。 按一下列標題，然後從資料預覽工具列中選擇一個選項。

![快速動作](media/data-flow/quick-actions1.png "快速動作")

選擇修改後，資料預覽將立即刷新。 按一下右上角的 **"確認"** 以生成新的轉換。

![快速動作](media/data-flow/quick-actions2.png "快速動作")

**類型轉換**和**修改**將生成派生列轉換，**刪除**將生成 Select 變換。

![快速動作](media/data-flow/quick-actions3.png "快速動作")

> [!NOTE]
> 如果編輯資料流程，則需要在添加快速轉換之前重新提取資料預覽。

### <a name="data-profiling"></a>資料分析

在資料預覽選項卡中選擇一列，然後按一下資料預覽工具列中的 **"統計資訊"，** 將在資料網格的最右側彈出一個圖表，其中包含有關每個欄位的詳細統計資訊。 Azure Data Factory 會根據要顯示圖表類型的資料取樣近進行判斷。 高基數位段將預設為 Null/NOT Null 圖表，而基數較低的分類和數位資料將顯示顯示資料值頻率的橫條圖。 您還將看到字串欄位的最大/len 長度、數值欄位中的最小/最大值、標準開發、百分位數、計數和平均值。

![資料行統計資料](media/data-flow/stats.png "資料行統計資料")

## <a name="next-steps"></a>後續步驟

* 完成資料流程的構建和調試後，[請從管道執行資料流程。](control-flow-execute-data-flow-activity.md)
* 使用資料流程測試管道時，請使用管道[調試運行執行選項。](iterative-development-debugging.md)
