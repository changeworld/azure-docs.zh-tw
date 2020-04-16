---
title: 映射資料流程除錯模式
description: 在建置資料流程時啟動互動式偵錯工作階段
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: d5fa34125f1ec712dda3099e2b3596c2566a536d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415537"
---
# <a name="mapping-data-flow-debug-mode"></a>映射資料流程除錯模式

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概觀

Azure 資料工廠映射資料串流的除錯模式允許您在生成和除錯資料串流時以互動方式監視資料元件轉換。 調試會話既可用於數據流設計會話,也可用於數據流的管道調試執行。 要打開除錯模式,請使用設計圖面頂部的「資料流調試」按鈕。

![除錯滑動](media/data-flow/debugbutton.png "除錯滑動")

開啟滑動後,系統將提示您選擇要使用的整合式執行時設定。 如果選擇了 AutoResolve 整合執行時間,則具有 8 個常規計算核心的群集,其運行時間為 60 分鐘。 關於資料流程整合時的詳細資訊,請參考[資料串流效能](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime)。

![除錯紅外線選擇](media/data-flow/debugbutton2.png "除錯紅外線選擇")

當除錯模式打開時,您將使用活動 Spark 群集以互動方式建構資料串流。 在 Azure Data Factory 中關閉偵錯後，將會關閉此工作階段。 您應該留意在您已開啟偵錯工作階段的期間內，由 Azure Databricks 所產生的每小時費用。

在大多數情況下,最好在調試模式下構建數據流,以便在在 Azure 數據工廠中發佈工作之前驗證業務邏輯並查看數據轉換。 使用管道面板上的「調試」按鈕測試管道中的數據流。

## <a name="cluster-status"></a>叢集狀態

當群集準備進行調試時,設計圖面頂部的群集狀態指示器將變為綠色。 如果您的叢集已經暖機，則綠色指示器幾乎會立即出現。 如果群集在進入調試模式時尚未運行,則必須等待 5-7 分鐘,群集才能啟動。 指示燈將旋轉,直到準備就緒。

完成調試後,關閉調試開關,以便 Azure 數據塊群集可以終止,並且不再為調試活動計費。

## <a name="debug-settings"></a>偵錯設定

可以通過按一下「資料流」畫布工具列上的「調試設置」來編輯調試設置。 您可以在此處選擇要用於每個源轉換的行限制或檔案源。 此設定中的行限制僅適用於當前調試會話。 您還可以選擇要用於 SQL DW 源的過渡連結服務。 

![偵錯設定](media/data-flow/debug-settings.png "偵錯設定")

如果資料流或其任何引用資料集中具有參數,則可以通過選擇 **「參數」** 選項卡來指定在調試期間要使用的值。

![除錯設定參數](media/data-flow/debug-settings2.png "除錯設定參數")

## <a name="data-preview"></a>資料預覽

若開啟偵錯，[資料預覽] 索引標籤在底部面板上亮起。 如果不打開除錯模式,資料流將僅在"檢查"選項卡中顯示進出每個轉換的當前元資料。數據預覽將僅查詢在調試設置中設置為限制的行數。 按下 **「刷新」** 以獲取數據預覽。

![資料預覽](media/data-flow/datapreview.png "資料預覽")

> [!NOTE]
> 檔源僅限制您看到的行,而不是要讀取的行。 對於非常大的數據集,建議您獲取該檔的一小部分並將其用於測試。 您可以在「除錯設定」中為檔案資料集類型的每個源選擇暫存檔。

在資料流程的偵錯模式中執行時，您的資料不會寫入至接收轉換。 調試會話旨在用作轉換的測試工具。 偵錯期間不需要接收，而且會在資料流程中忽略。 如果要測試在 Sink 中寫入數據,請從 Azure 數據工廠管道執行數據流,並使用管道中的調試執行。

### <a name="testing-join-conditions"></a>測試聯結條件

當單元測試聯接、存在或查找轉換時,請確保為測試使用一小組已知數據。 您可以使用上面的「除錯設定」選項來設定用於測試的暫存檔。 這是必需的,因為在限制或採樣大型數據集中的行時,無法預測將哪些行以及哪些鍵將讀取到流中進行測試。 結果是非確定性的,這意味著您的聯接條件可能會失敗。

### <a name="quick-actions"></a>快速動作

看到數據預覽後,可以生成快速轉換以鍵入、刪除或對列進行修改。 按下列標題,然後從資料預覽工具列中選擇一個選項。

![快速操作](media/data-flow/quick-actions1.png "快速動作")

選擇修改後,數據預覽將立即刷新。 按下右上角的 **「確認」** 以生成新的轉換。

![快速操作](media/data-flow/quick-actions2.png "快速動作")

**類型轉換**和**修改**將產生派生列轉換,**刪除**將生成 Select 變換。

![快速操作](media/data-flow/quick-actions3.png "快速動作")

> [!NOTE]
> 如果編輯資料流,則需要在添加快速轉換之前重新提取數據預覽。

### <a name="data-profiling"></a>資料分析

在資料預覽選項卡中選擇一列,然後單擊數據預覽工具列中的 **「統計資訊」,** 將在資料網格的最右側彈出一個圖表,其中包含有關每個欄位的詳細統計資訊。 Azure Data Factory 會根據要顯示圖表類型的資料取樣近進行判斷。 高基數位段將預設為 NULL/NOT NULL 圖表,而基數較低的分類和數位數據將顯示顯示數據值頻率的條形圖。 您還將看到字串欄位的最大/len 長度、數值欄位中的最小/最大值、標準開發、百分位數、計數和平均值。

![資料行統計資料](media/data-flow/stats.png "資料行統計資料")

## <a name="next-steps"></a>後續步驟

* 完成資料串流的建構和除錯後,[請從管道執行資料流。](control-flow-execute-data-flow-activity.md)
* 使用數據流測試管道時,請使用管道[調試運行執行選項。](iterative-development-debugging.md)
