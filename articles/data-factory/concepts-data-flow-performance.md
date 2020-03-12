---
title: 對應資料流程效能和微調指南
description: 瞭解會影響 Azure Data Factory 中對應資料流程效能的關鍵因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: 1a6b50456a5dc3ff89fe7b513f406dc68bd2401e
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128457"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>對應資料流程效能和微調指南

對應 Azure Data Factory 中的資料流程提供無程式碼介面，以大規模設計、部署和協調資料轉換。 如果您不熟悉對應的資料流程，請參閱[對應資料流程總覽](concepts-data-flow-overview.md)。

當您從 ADF UX 設計和測試資料流程時，請務必切換開啟 [偵測模式]，以即時執行您的資料流程，而不需要等待叢集準備。 如需詳細資訊，請參閱[Debug Mode](concepts-data-flow-debug-mode.md)。

這段影片會示範將資料轉換成資料流程的一些範例時間：
> [!VIDEO https://www.youtube.com/watch?v=6CSbWm4lRhw]

## <a name="monitoring-data-flow-performance"></a>監視資料流程效能

設計對應資料流程時，您可以按一下 [設定] 面板中的 [資料預覽] 索引標籤，對每個轉換進行單元測試。 驗證邏輯之後，請以管線中的活動的端對端測試資料流程。 加入「執行資料流程」活動，並使用 [偵錯工具] 按鈕來測試資料流程的效能。 若要開啟資料流程的執行計畫和效能設定檔，請在管線的 [輸出] 索引標籤中，按一下 [動作] 底下的 [眼鏡] 圖示。

![資料流程監視](media/data-flow/mon002.png "資料流程監視器2")

 您可以使用這項資訊，針對不同大小的資料來源來估計資料流程的效能。 如需詳細資訊，請參閱[監視對應資料流程](concepts-data-flow-monitoring.md)。

![資料流程監視](media/data-flow/mon003.png "資料流程監視器3")

 針對「管線」 debug 執行，熱叢集需要大約一分鐘的整體效能計算中的叢集設定時間。 如果您要初始化預設 Azure Integration Runtime，加速時間可能需要大約5分鐘。

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>增加 Azure Integration Runtime 中的計算大小

具有更多核心的 Integration Runtime 會增加 Spark 計算環境中的節點數目，並提供讀取、寫入及轉換資料的更多處理能力。
* 如果您希望處理速率高於您的輸入速率，請嘗試**計算優化**叢集。
* 如果您想要在記憶體中快取更多資料，請嘗試使用**記憶體優化**的叢集。 記憶體優化比計算優化的每個核心具有更高的價格點，但可能會導致更快速的轉換速度。

![新增 IR](media/data-flow/ir-new.png "新增 IR")

如需如何建立 Integration Runtime 的詳細資訊，請參閱[Azure Data Factory 中的 Integration Runtime](concepts-integration-runtime.md)。

### <a name="increase-the-size-of-your-debug-cluster"></a>增加您的 debug 叢集大小

根據預設，開啟 debug 會使用針對每個資料處理站自動建立的預設 Azure 整合執行時間。 此預設 Azure IR 設定為八個核心，四個用於驅動程式節點，而四個用於背景工作節點，使用一般計算屬性。 當您使用較大的資料進行測試時，您可以藉由建立具有較大設定的 Azure IR 來增加您的偵錯工具大小，並在切換到 [debug] 時選擇這個新的 Azure IR。 這會指示 ADF 使用此 Azure IR 進行資料預覽，以及使用資料流程來進行管線的資料流程處理。

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Azure SQL Database 和 Azure SQL 資料倉儲的優化

### <a name="partitioning-on-source"></a>在來源上進行資料分割

1. 移至 [**優化**] 索引標籤，然後選取 [**設定資料分割**]
1. 選取 [**來源**]。
1. 在 [分割區**數目**] 底下，設定 AZURE SQL DB 的最大連接數目。 您可以嘗試較高的設定，以取得與資料庫的平行連接。 不過，某些情況下可能會因為連線數目有限而導致效能更快。
1. 選取要依特定資料表資料行還是查詢進行分割。
1. 如果您已選取 [資料**行**]，請挑選資料分割資料行。
1. 如果您選取 [**查詢**]，請輸入符合資料庫資料表之資料分割配置的查詢。 此查詢可讓源資料庫引擎利用分割區刪除。 您的源資料庫資料表不需要進行資料分割。 如果您的來源尚未分割，ADF 仍然會根據您在 [來源] 轉換中選取的金鑰，使用 Spark 轉換環境中的資料分割。

![來源部分](media/data-flow/sourcepart3.png "來源部分")

> [!NOTE]
> 協助您選擇來源的資料分割數目的最佳指南，取決於您為 Azure Integration Runtime 所設定的核心數目，並將該數位乘以5。 因此，比方說，如果您要轉換 ADLS 資料夾中的一系列檔案，而您要使用32核心 Azure IR，則目標的資料分割數目為 32 x 5 = 160 個數據分割。

### <a name="source-batch-size-input-and-isolation-level"></a>來源批次大小、輸入和隔離等級

在來源轉換的 [**來源選項**] 底下，下列設定可能會影響效能：

* 批次大小會指示 ADF 將資料儲存在記憶體中的集合中，而不是逐列。 [批次大小] 是選擇性的設定，如果計算節點的資源大小不正確，您可能會用盡這些資源。
* 設定查詢可以讓您篩選來源的資料列，然後才抵達資料流程以進行處理。 這可讓初始資料的取得速度更快。 如果您使用查詢，您可以為 Azure SQL DB 新增選擇性的查詢提示，例如讀取未認可。
* 讀取未認可的會在來源轉換時提供更快速的查詢結果

![Source](media/data-flow/source4.png "來源")

### <a name="sink-batch-size"></a>接收批次大小

若要避免對資料流程進行逐列處理，請在 Azure SQL DB 和 Azure SQL DW 接收的 [設定] 索引標籤中設定**批次大小**。 如果已設定批次大小，ADF 會根據所提供的大小，以批次方式處理資料庫寫入。

![接收](media/data-flow/sink4.png "接收")

### <a name="partitioning-on-sink"></a>在接收上分割

即使您的資料未分割在目的地資料表中，但建議您將資料分割在「接收」轉換中。 分割資料的載入頻率通常會比強制所有連接使用單一節點/資料分割更快。 移至接收的 [優化] 索引標籤，然後選取 [*迴圈*配置資源分割]，以選取要寫入至接收的理想分割區數目。

### <a name="disable-indexes-on-write"></a>在寫入時停用索引

在您的管線中，將[預存程式活動](transform-data-using-stored-procedure.md)加入至您的資料流程活動之前，會在您的接收所寫入的目標資料表上停用索引。 在資料流程活動之後，加入另一個可啟用這些索引的預存程式活動。 或利用資料庫接收中的前置處理和後置處理腳本。

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>增加您的 Azure SQL DB 和 DW 的大小

在您的管線執行之前，排程來源的調整大小並接收 Azure SQL DB 和 DW，以增加輸送量，並在達到 DTU 限制之後將 Azure 節流降至最低。 當您的管線執行完成之後，請將您的資料庫調整回其正常執行速率。

* 具有887k 資料列和74資料行的 SQL DB 來源資料表至具有單一衍生資料行轉換的 SQL DB 資料表，會使用記憶體優化的 80-核心 debug Azure IRs 來進行大約3分鐘的端對端處理。

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[僅限 Azure Synapse SQL DW]使用預備環境透過 Polybase 大量載入資料

若要避免將逐列插入至 DW，請核取 [在您的接收設定中**啟用暫存**]，讓 ADF 可以使用[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。 PolyBase 可讓 ADF 大量載入資料。
* 當您從管線執行「資料流程」活動時，您必須選取 Blob 或 ADLS Gen2 儲存體位置，以便在大量載入期間暫存您的資料。

* 包含74個數據行的421Mb 檔案的檔案來源到 Synapse 資料表，而單一衍生的資料行轉換需要大約4分鐘的端對端，使用記憶體優化 80-核心 debug Azure IRs。

## <a name="optimizing-for-files"></a>檔案的優化

在每次轉換時，您都可以在 [優化] 索引標籤中設定要讓 data factory 使用的資料分割配置。最好先測試以檔案為基礎的接收，保留預設的分割和優化。

* 對於較小的檔案，您可能會發現選擇較少的磁碟分割，有時可以比要求 Spark 分割小型檔案更好且快速。
* 如果您沒有來源資料的足夠資訊，請選擇 [*迴圈*配置資源分割]，並設定資料分割數目。
* 如果您的資料行有可能是良好的雜湊索引鍵，請選擇 [*雜湊分割*]。

* 具有74個數據行之421Mb 檔的 file 接收和單一衍生的資料行轉換，需要大約2分鐘的端對端使用記憶體優化 80-核心 debug Azure IRs。

在資料預覽和管線偵錯工具中進行偵錯工具時，檔案型源資料集的限制和取樣大小只會套用至傳回的資料列數目，而不會套用至讀取的資料列數目。 這可能會影響您的偵錯工具執行效能，而且可能會導致流程失敗。
* Debug 叢集預設為小型單一節點叢集，建議使用範例小型檔案進行偵錯工具。 移至 [偵錯工具] [設定]，並使用暫存檔案指向資料的小型子集。

    ![Debug 設定](media/data-flow/debugsettings3.png "偵錯設定")

### <a name="file-naming-options"></a>檔案命名選項

在對應資料流程中寫入 Blob 或 ADLS 檔案存放區的轉換資料時，最常見的方式。 在您的接收中，您必須選取指向容器或資料夾的資料集，而不是名為的檔案。 當對應資料流程使用 Spark 來執行時，系統會根據您的資料分割配置，將您的輸出分割成多個檔案。

常見的資料分割配置是選擇 [_輸出至單一_檔案]，這會將所有輸出元件檔案合併到接收中的單一檔案。 此作業需要輸出縮減為單一叢集節點上的單一分割區。 如果您要將許多大型來源檔案結合成單一輸出檔，您可以耗盡叢集節點資源。

若要避免耗盡計算節點資源，請在資料流程中保留預設的優化配置，並在管線中新增複製活動，以將輸出檔案夾中的所有元件檔案合併到新的單一檔案。 這項技術會將轉換的動作與檔案合併分開，並達到將_輸出設定為單一_檔案的相同結果。

### <a name="looping-through-file-lists"></a>迴圈流覽檔案清單

當來源轉換反復處理多個檔案，而不是透過每個活動的迴圈時，對應資料流程的執行效能會更好。 我們建議在您的來源轉換中使用萬用字元或檔案清單。 資料流程處理常式會藉由允許迴圈在 Spark 叢集中發生，而執行得更快。 如需詳細資訊，請參閱[原始檔轉換中的萬用字元](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

例如，如果您有2019年7月的資料檔案清單，而您想要在 Blob 儲存體的資料夾中處理該檔案，以下是您可以在來源轉換中使用的萬用字元。

```DateFiles/*_201907*.txt```

藉由使用萬用字元，您的管線將只會包含一個資料流程活動。 這會比對 Blob 存放區進行查閱的效果更好，然後使用 ForEach 搭配內的「執行資料流程」活動，逐一查看所有相符的檔案。

### <a name="optimizing-for-cosmosdb"></a>針對 CosmosDB 優化

在 CosmosDB 接收上設定輸送量和批次屬性，只會在從管線資料流程活動的資料流程執行期間生效。 在您的資料流程執行之後，CosmosDB 會接受原始的集合設定。

* 批次大小：計算資料的粗略資料列大小，並確認 rowSize * 批次大小小於2000000。 如果是，請增加批次大小以取得更佳的輸送量
* 輸送量：在這裡設定較高的輸送量設定，以允許檔更快速地寫入 CosmosDB。 請記住，以高輸送量設定為基礎的較高 RU 成本。
*   寫入輸送量預算：使用小於每分鐘的 ru 總數的值。 如果您的資料流程具有大量的 Spark 分割區，則設定預算輸送量會允許在這些分割區之間進行更多的平衡。

## <a name="join-performance"></a>聯結效能

管理資料流程中聯結的效能是一項很常見的作業，您會在資料轉換的整個生命週期中執行。 在 ADF 中，資料流程不需要在聯結之前排序資料，因為這些作業是以 Spark 中的雜湊聯結執行。 不過，您可以利用「廣播」聯結優化來改善效能。 這可避免洗牌，方法是將聯結關聯性任一端的內容向下推送至 Spark 節點。 這適用于用於參考查閱的小型資料表。 較大的資料表可能無法放入節點的記憶體中，對廣播優化而言是不錯的候選項目。

另一個聯結優化是以可避免 Spark 傾向于執行交叉聯結的方式來建立聯接。 例如，當您在聯結條件中包含常值時，Spark 可能會發現必須先執行完整的笛卡兒乘積，然後篩選出聯結的值。 但是，如果您確保聯結條件的兩端都有資料行值，您可以避免此 Spark 引發的笛卡兒乘積，並改善您的聯結和資料流程的效能。

## <a name="next-steps"></a>後續步驟

請參閱其他與效能相關的資料流程文章：

- [資料流程優化索引標籤](concepts-data-flow-overview.md#optimize)
- [資料流程活動](control-flow-execute-data-flow-activity.md)
- [監視資料流程效能](concepts-data-flow-monitoring.md)
