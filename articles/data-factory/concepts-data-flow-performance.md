---
title: 映射資料流程性能和調優指南
description: 瞭解影響 Azure 資料工廠中映射資料流程性能的關鍵因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: 95a60abef283984d66736358d2d02048f08d700d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246988"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>映射資料流程性能和調優指南

在 Azure 資料工廠中映射資料流程提供了一個無代碼介面，用於大規模設計、部署和協調資料轉換。 如果您不熟悉映射資料流程，請參閱[映射資料流程概述](concepts-data-flow-overview.md)。

在設計和測試來自 ADF UX 的資料流程時，請確保打開偵錯模式以即時執行資料流程，而無需等待群集預熱。 有關詳細資訊，請參閱[偵錯模式](concepts-data-flow-debug-mode.md)。

此視頻顯示了一些使用資料流程轉換資料的示例計時：
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>監控資料流程性能

在設計映射資料流程時，可以通過按一下配置面板中的資料預覽選項卡來對每個轉換進行單元測試。 驗證邏輯後，將資料流程作為管道中的工作端到端測試。 添加執行資料流程活動並使用調試按鈕測試資料流程的性能。 要打開資料流程的執行計畫和性能設定檔，請按一下管道輸出選項卡中的"操作"下的眼鏡圖示。

![資料流程監視器](media/data-flow/mon002.png "資料流程監視器 2")

 您可以使用此資訊來估計資料流程針對不同大小的資料來源的性能。 有關詳細資訊，請參閱[監視映射資料流程](concepts-data-flow-monitoring.md)。

![資料流程監視](media/data-flow/mon003.png "資料流程監視器 3")

 對於管道調試運行，暖群集需要大約一分鐘的群集設置時間。 如果要初始化預設 Azure 集成運行時，則啟動時間可能需要大約 5 分鐘。

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>在 Azure 集成運行時增加計算大小

具有更多內核的集成運行時會增加 Spark 計算環境中的節點數，並提供更多的處理能力來讀取、寫入和轉換資料。
* 如果希望處理速率高於輸入速率，請嘗試**計算優化**群集。
* 如果要在記憶體中緩存更多資料，請嘗試**記憶體優化**群集。 與計算優化相比，經過優化的記憶體每個核心的價格點更高，但可能會導致更快的轉換速度。

![新紅外](media/data-flow/ir-new.png "新紅外")

有關如何創建集成運行時的詳細資訊，請參閱[Azure 資料工廠中的集成運行時](concepts-integration-runtime.md)。

### <a name="increase-the-size-of-your-debug-cluster"></a>增加調試群集的大小

預設情況下，打開調試將使用為每個資料工廠自動創建的預設 Azure 集成運行時。 此預設 Azure IR 設置為八個內核，4 個為驅動程式節點，4 個設置為輔助節點，使用常規計算屬性。 使用較大資料進行測試時，可以通過創建具有較大配置的 Azure IR 來增加調試群集的大小，並在打開調試時選擇此新的 Azure IR。 這將指示 ADF 使用此 Azure IR 進行資料預覽和管道調試，並處理資料流程。

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>優化 Azure SQL 資料庫和 Azure SQL 資料倉儲

### <a name="partitioning-on-source"></a>在源上分區

1. 轉到 **"優化**"選項卡並選擇 **"設置分區"**
1. 選擇**源**。
1. 在 **"分區數**"下，設置到 Azure SQL DB 的最大連接數。 您可以嘗試較高的設置來獲得與資料庫的並行連接。 但是，在某些情況下，由於連接數量有限，可能會導致性能更快。
1. 選擇是按特定表列還是按查詢進行分區。
1. 如果選擇 **"列"，** 則選取分區列。
1. 如果選擇 **"查詢"，** 請輸入與資料庫表的分區方案匹配的查詢。 此查詢允許源資料庫引擎利用分區消除。 不需要對源資料庫表進行分區。 如果源尚未分區，ADF 仍將根據在"源"轉換中選擇的鍵在 Spark 轉換環境中使用資料分區。

![源部件](media/data-flow/sourcepart3.png "源部件")

> [!NOTE]
> 説明您為源選擇分區數的良好指南基於為 Azure 集成運行時設置的內核數，並將該數位乘以 5。 例如，如果要轉換 ADLS 資料夾中的一系列檔，並且要使用 32 核 Azure IR，則目標分區數為 32 x 5 = 160 個分區。

### <a name="source-batch-size-input-and-isolation-level"></a>源批次處理大小、輸入和隔離等級

在源轉換中的 **"源選項**"下，以下設置可能會影響性能：

* 批次處理大小指示 ADF 將資料存儲在記憶體中的集中，而不是逐行存儲。 批次處理大小是一個可選設置，如果計算節點的大小不正確，則可能會耗盡資源。
* 設置查詢可以允許您在源的行到達資料流程進行處理之前對其進行篩選。 這可以加快初始資料獲取速度。 如果使用查詢，則可以為 Azure SQL DB 添加可選的查詢提示，例如"讀取未提交"。
* 未提交讀取將在源轉換上提供更快的查詢結果

![來源](media/data-flow/source4.png "來源")

### <a name="sink-batch-size"></a>沉分批大小

為避免逐行處理資料流程，請在 Azure SQL DB 和 Azure SQL DW 接收器的"設置"選項卡中設置**批次處理大小**。 如果設置了批次處理大小，ADF 會根據提供的大小處理分批寫入資料庫。

![接收](media/data-flow/sink4.png "接收")

### <a name="partitioning-on-sink"></a>在水槽上分區

即使您的目標表中沒有分區資料，也建議在接收器轉換中對資料進行分區。 分區資料通常會導致強制所有連接使用單個節點/分區的載入速度更快。 轉到接收器的"優化"選項卡，然後選擇 *"迴圈分區*"以選擇要寫入接收器的理想分區數。

### <a name="disable-indexes-on-write"></a>禁用寫入時索引

在管道中，在資料流程活動之前添加[預存程序活動](transform-data-using-stored-procedure.md)，該活動禁用從接收器寫入的目標表上的索引。 在資料流程活動之後，添加另一個啟用這些索引的預存程序活動。 或者利用資料庫接收器中的預處理和後處理腳本。

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>增加 Azure SQL DB 和 DW 的大小

在管道運行之前安排對源和接收器 Azure SQL DB 和 DW 的大小進行調整，以增加輸送量並在達到 DTU 限制後最小化 Azure 限制。 管道執行完成後，將資料庫調整到其正常運行速率。

* SQL DB 源表具有 887k 行和 74 列到 SQL DB 表，具有單個派生列轉換，使用經過 80 核調試 Azure IR 的記憶體，端到端大約需要 3 分鐘。

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[僅 Azure 同步 SQL DW]使用暫存通過聚基批量載入資料

若要避免逐行插入 DW，請在接收器設置中選中**啟用暫存，** 以便 ADF 可以使用[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。 PolyBase 允許 ADF 批量載入資料。
* 當您從管道執行資料流程活動時，您需要選擇 Blob 或 ADLS Gen2 存儲位置，以在批量載入期間暫存資料。

* 421Mb 檔的檔源包含 74 列到 Synapse 表，單個派生列轉換使用經過優化的記憶體 80 核調試 Azure IR 端到端大約需要 4 分鐘。

## <a name="optimizing-for-files"></a>優化檔

在每個轉換中，您可以設置您希望資料工廠在"優化"選項卡中使用的分區方案。最好首先測試基於檔的接收器，保留預設分區和優化。

* 對於較小的檔，您可能會發現選擇較少的分區有時比要求 Spark 對您的小檔進行分區更好、更快。
* 如果沒有足夠的來源資料資訊，請選擇 *"迴圈*分區"並設置分區數。
* 如果資料具有可是好的雜湊鍵的列，請選擇*雜湊分割*。

* 包含包含 74 列的 421Mb 檔的檔接收器的檔源和單個派生列轉換使用經過優化的記憶體 80 核調試 Azure IR 端到端大約需要 2 分鐘。

在資料預覽和管道調試中調試時，基於檔的源資料集的限制和採樣大小僅適用于返回的行數，而不是讀取的行數。 這可能會影響調試執行的性能，並可能導致流失敗。
* 預設情況下，調試群集是小型單節點群集，我們建議使用示例小檔進行調試。 轉到調試設置，並使用暫存檔案指向資料的一小部分。

    ![調試設置](media/data-flow/debugsettings3.png "偵錯設定")

### <a name="file-naming-options"></a>檔命名選項

在映射寫入 Blob 或 ADLS 檔存儲的資料流程中寫入轉換資料的最常見方法。 在接收器中，必須選擇指向容器或資料夾的資料集，而不是命名檔。 當映射資料流程使用 Spark 執行時，您的輸出會根據您的分區方案拆分到多個檔上。

常見的分區方案是選擇 _"輸出到單個檔_"，該檔將所有輸出 PART 檔合併到接收器中的單個檔中。 此操作要求將輸出減少到單個叢集節點上的單個分區。 如果要將許多大型原始檔案合併到單個輸出檔案中，則叢集節點資源可能會耗盡。

為了避免耗盡計算節點資源，請將預設優化的方案保留在資料流程中，並在管道中添加複製活動，將輸出檔案夾中的所有 PART 檔合併到新的單個檔。 此技術將轉換操作與檔合併分開，並實現與將 _"輸出"設置為單個檔_相同的結果。

### <a name="looping-through-file-lists"></a>逐一查看檔案清單

當源轉換在多個檔上迴圈，而不是通過 For each 活動迴圈時，映射資料流程將更好地執行。 我們建議在源轉換中使用萬用字元或檔案清單。 資料流程過程將更快地執行，允許迴圈在 Spark 群集內進行。 有關詳細資訊，請參閱[源轉換 中的萬用字元](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

例如，如果您有一個 2019 年 7 月的資料檔案清單，您希望在 Blob 存儲中的資料夾中處理，下面是可在源轉換中使用萬用字元。

```DateFiles/*_201907*.txt```

通過使用萬用字元，管道將僅包含一個資料流程活動。 這將比對 Blob 存儲的查找效果更好，然後使用 ForEach 和內部執行資料流程活動遍遍所有匹配的檔。

### <a name="optimizing-for-cosmosdb"></a>為宇宙發展優化

在 CosmosDB 接收器上設置輸送量和批次處理屬性僅在從管道資料流程活動執行該資料流程期間生效。 在資料流程執行後，CosmosDB 將遵守原始收集設置。

* 批次處理大小：計算資料的粗排大小，並確保行大小 = 批次處理大小小於 200 萬。 如果是，則增加批次處理大小以獲得更好的輸送量
* 輸送量：在此處設置更高的輸送量設置，以允許文檔更快地寫入 CosmosDB。 請記住，基於高輸送量設置的 RU 成本較高。
*   寫入輸送量預算：使用小於每分鐘總 R 機的值。 如果資料流程具有大量 Spark 分區，則設置預算輸送量將允許在這些分區之間實現更多平衡。

## <a name="join-performance"></a>加入性能

管理資料流程中聯接的性能是一項很常見的操作，您將在整個資料轉換的整個生命週期中執行。 在 ADF 中，資料流程不需要在聯接之前對資料進行排序，因為這些操作在 Spark 中作為雜湊聯接執行。 但是，您可以通過"廣播"聯接優化提高性能。 這將通過將聯接關係兩側的內容向下推送到 Spark 節點來避免隨機播放。 這適用于用於參考查找的較小表。 可能不適合節點記憶體的較大表不適合廣播優化。

另一個聯接優化是構建聯接，這樣可以避免 Spark 實現交叉聯接的傾向。 例如，當您在聯結條件中包含文本值時，Spark 可能會將其視為首先執行完整點菜產品，然後篩選出聯接值的要求。 但是，如果您確保聯結條件兩側都有列值，則可以避免此 Spark 誘導的點菜產品並提高聯接和資料流程的性能。

## <a name="next-steps"></a>後續步驟

請參閱與性能相關的其他資料流程文章：

- [資料流程優化選項卡](concepts-data-flow-overview.md#optimize)
- [資料流程活動](control-flow-execute-data-flow-activity.md)
- [監控資料流程性能](concepts-data-flow-monitoring.md)
