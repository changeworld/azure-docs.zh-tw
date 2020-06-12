---
title: 對應資料流的效能和調整指南
description: 了解哪些關鍵因素會影響 Azure Data Factory 中對應資料流的效能。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: 327fffd807d93fda67ff650954ece65e5db58e63
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798102"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>對應資料流的效能和調整指南

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 中的對應資料流提供無程式碼介面，可讓您大規模地設計、部署和協調資料轉換。 如果您不熟悉對應資料流，請參閱[對應資料流概觀](concepts-data-flow-overview.md)。

當您從 ADF UX 中設計和測試資料流程時，請務必切換至偵錯模式來即時執行您的資料流程，而不需要等待叢集準備就緒。 如需詳細資訊，請參閱[偵錯模式](concepts-data-flow-debug-mode.md)。

這段影片會示範將資料轉換成資料流程的一些時機範例：
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>監視資料流程效能

設計對應資料流時，您可以按一下設定面板中的 [資料預覽] 索引標籤，對每個轉換進行單元測試。 驗證邏輯之後，請以活動的形式，在管線中對資料流程進行端對端測試。 新增「執行資料流程」活動，並使用 [偵錯] 按鈕來測試資料流程的效能。 若要開啟資料流程的執行計畫和效能設定檔，請在管線的 [輸出] 索引標籤中，按一下 [動作] 底下的 [眼鏡] 圖示。

![資料流程監視](media/data-flow/mon002.png "資料流程監視 2")

 您可以使用這項資訊，預估不同資料來源大小的資料流程效能。 如需詳細資訊，請參閱[監視對應資料流](concepts-data-flow-monitoring.md)。

![資料流程監視](media/data-flow/mon003.png "資料流程監視 3")

 針對管線偵錯執行，在整體效能計算中，暖叢集 (warm cluster) 大約需要一分鐘的叢集設定時間。 如果您要初始化預設的 Azure Integration Runtime，啟動時間可能需要大約 5 分鐘。

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>增加 Azure Integration Runtime 中的計算大小

具有更多核心的 Integration Runtime 會增加 Spark 計算環境中的節點數目，並為讀取、寫入及轉換資料提供更強的處理能力。 ADF 資料流程會利用 Spark 來作為計算引擎。 Spark 環境非常適合用於記憶體最佳化的資源。
* 如果您希望處理速率高於輸入速率，請嘗試使用**計算最佳化**的叢集。
* 如果您想要在記憶體中快取更多資料，請嘗試使用**記憶體最佳化**的叢集。 記憶體最佳化在每個核心上的價位 (price-point) 會比計算最佳化高，但可能會帶來更快的轉換速度。 如果您在執行資料流程時遇到記憶體不足的錯誤，請切換至記憶體最佳化的 Azure IR 設定。

![新增 IR](media/data-flow/ir-new.png "新增 IR")

如需如何建立 Integration Runtime 的詳細資訊，請參閱 [Azure Data Factory 中的 Integration Runtime](concepts-integration-runtime.md)。

### <a name="increase-the-size-of-your-debug-cluster"></a>增加您的偵錯叢集大小

根據預設，開啟偵錯時會使用為每個資料處理站自動建立的預設 Azure Integration Runtime。 此預設 Azure IR 會設定為使用八個核心，四個用於驅動程式節點，另位四個用於背景工作節點，並且使用一般計算屬性。 當您使用較大的資料進行測試時，您可以建立具有較大組態的 Azure IR 來增加您偵錯叢集的大小，並在切換到偵錯時選擇這個新的 Azure IR。 這會指示 ADF 使用此 Azure IR 進行資料流程的資料預覽及管線偵錯。

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>使用 TTL 減少叢集計算啟動時間

[資料流程屬性] 底下的 Azure IR 中有一個屬性，可讓您為處理站的叢集計算資源建立集區。 透過此集區，您就可以依序提交要執行的資料流程活動。 建立集區之後，每個後續作業將需要 1-2 分鐘的時間來讓隨選 Spark 叢集執行作業。 資源集區的初始設定大約需要 6 分鐘的時間。 在存留時間 (TTL) 設定中，指定您想要維護資源集區的時間。

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>最佳化 Azure SQL Database 和 Azure SQL 資料倉儲 Synapse

### <a name="partitioning-on-source"></a>在來源上進行資料分割

1. 移至 [最佳化] 索引標籤，然後選取 [設定資料分割]
1. 選取 [來源]。
1. 在 [分割區數目] 中，設定 Azure SQL DB 的最大連線數目。 您可以嘗試較高的設定，以提高資料庫的平行連線數目。 不過，在某些情況下，限制連線數目可能可以帶來更快的效能。
1. 選取是否要依特定資料表的資料行或查詢進行分割。
1. 如果您選取**資料行**，請挑選分割資料行。
1. 如果您選取 [查詢]，請針對資料庫資料表的資料分割配置，輸入相符的查詢。 此查詢可讓來源資料庫引擎利用分割區刪除功能。 您的來源資料庫資料表不需要進行分割。 如果您的來源尚未分割，ADF 仍然會根據您在來源轉換中選取的金鑰，使用 Spark 轉換環境中的資料分割功能。

![來源部分](media/data-flow/sourcepart3.png "來源部分")

> [!NOTE]
> 協助您選擇來源分割數目的最佳指南就是，將您為 Azure Integration Runtime 所設定的核心數目乘以 5。 例如，如果您要轉換 ADLS 資料夾中的一系列檔案，而您將使用 32 個核心的 Azure IR，則資料分割數目就會以 32 x 5 = 160 個分割區為目標。

### <a name="source-batch-size-input-and-isolation-level"></a>來源的批次大小、輸入和隔離等級

在來源轉換的 [來源選項] 底下，下列設定可能會影響效能：

* 批次大小會指示 ADF 以集合的形式將資料儲存在 Spark 記憶體中，而不是逐列儲存。 [批次大小] 是選擇性的設定，如果資源大小不正確，計算節點上的資源可能會不夠。 未設定此屬性時，將會使用 Spark 快取批次預設值。
* 設定查詢可以讓您先篩選來源上的資料列，再讓這些資料列進入資料流程來進行處理。 這可讓初始資料的取得速度更快。 如果您使用查詢，您可以為 Azure SQL DB 新增選擇性的查詢提示，例如 READ UNCOMMITTED (讀取未認可)。
* 「讀取未認可」會在來源轉換上提供更快速的查詢結果

![Source](media/data-flow/source4.png "來源")

### <a name="sink-batch-size"></a>接收批次大小

若要避免對資料流程進行逐列處理，請在 Azure SQL DB 和 Azure SQL DW 接收的 [設定] 索引標籤中設定 [批次大小]。 如果已設定批次大小，ADF 就會根據所提供的大小，以批次方式處理資料庫寫入。 未設定此屬性時，將會使用 Spark 快取批次預設值。

![接收](media/data-flow/sink4.png "接收")

### <a name="partitioning-on-sink"></a>在接收上進行資料分割

即使您沒有讓資料在目的地資料表中進行分割，我們仍建議您在接收轉換中分割資料。 比起強制所有連線使用單一節點/分割，分割的資料通常可以大幅加快載入速度。 移至接收的 [最佳化] 索引標籤，然後選取 [循環配置資源] 分割方式，以選取要寫入至接收端的理想分割數目。

### <a name="disable-indexes-on-write"></a>在寫入時停用索引

在您的管線中，將[預存程序活動](transform-data-using-stored-procedure.md)新增至您的資料流程活動之前，這會在從接收端寫入的目標資料表上停用索引。 在資料流程活動之後新增另一個可啟用這些索引的預存程序活動。 或者您可以利用資料庫接收端中的前置處理和後置處理指令碼。

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>增加您 Azure SQL DB 和 DW 的大小

在您的管線執行之前，您可以為來源及接收 Azure SQL DB 和 DW 排定調整大小作業來增加輸送量，並在達到 DTU 限制時將 Azure 節流降至最低。 當您的管線執行完成之後，請將您的資料庫大小調整回正常執行比率。

* 使用記憶體最佳化的偵錯 Azure IR (80 個核心) 時，若 SQL DB 來源資料表具有 887k 個資料列和 74 個資料行，則端對端地傳送至具有單一衍生資料行轉換的 SQL DB 資料表約需要 3 分鐘。

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[僅限 Azure Synapse SQL DW] 使用暫存功能來透過 Polybase 大量載入資料

若要避免逐列插入 DW，請在接收設定中核取 [啟用暫存]，讓 ADF 可以使用 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。 PolyBase 可讓 ADF 大量載入資料。
* 當您從管線執行資料流程活動時，您必須選取 Blob 或 ADLS Gen2 儲存體位置，以在大量載入期間暫存您的資料。

* 使用記憶體最佳化的偵錯 Azure IR (80 個核心) 時，若檔案來源是 421Mb 且具有 74 個資料行的檔案，則端對端地傳送至 Synapse 資料表和單一衍生資料行轉換約需要 4 分鐘。

## <a name="optimizing-for-files"></a>檔案的最佳化

在每次轉換時，您都可以在 [最佳化] 索引標籤中設定要讓資料處理站使用的資料分割配置。比較好的方式是在保留預設分割和最佳化的情況下，先測試檔案型的接收。

* 對於較小的檔案，您可能會發現，有時候比起要求 Spark 分割小型檔案，選擇較少的資料分割可提升工作品質和速度。
* 如果您的來源資料沒有足夠的資訊，請選擇 [循環配置資源] 分割方式，並設定分割數目。
* 如果您的資料具有可以成為良好雜湊索引鍵的資料行，請選擇 [雜湊分割]。

* 使用記憶體最佳化的偵錯 Azure IR (80 個核心) 時，若檔案來源具有 421 Mb 且包含 74 個資料行的檔案接收和單一衍生資料行轉換，則進行端對端傳送約需要 2 分鐘。

在資料預覽和管線偵錯中進行偵錯時，檔案型來源資料集的限制和取樣大小只會套用至傳回的資料列數目，而不會套用至讀取的資料列數目。 這可能會影響您的偵錯執行效能，而且可能會導致流程失敗。
* 偵錯叢集預設為小型單一節點叢集，建議使用小型檔案範例進行偵錯。 移至 [偵錯設定]，並使用暫存檔案來指向小型資料子集。

    ![偵錯設定](media/data-flow/debugsettings3.png "偵錯設定")

### <a name="file-naming-options"></a>檔案命名選項

在對應資料流中寫入已轉換資料最常見的方式是寫入 Blob 或 ADLS 檔案存放區。 在您的接收中，您必須選取指向容器或資料夾的資料集，而不是具名檔案。 當對應資料流使用 Spark 來執行時，系統會根據您的資料分割配置，將您的輸出分割成多個檔案。

常見的資料分割配置是選擇 [輸出到單一檔案]，也就是將所有輸出 PART 檔案合併到接收中的單一檔案。 此作業需要輸出縮減為單一叢集節點上的單一分割區。 如果您要將許多大型來源檔案結合成單一輸出檔案，您可能會耗盡叢集節點資源。

若要避免耗盡計算節點資源，請在資料流程保留預設的最佳化配置，並在管線中新增複製活動，以將輸出檔案夾中的所有 PART 檔案合併到新的單一檔案。 這項技術會將轉換的動作與檔案合併分開，並達到 [輸出到單一檔案] 設定的相同結果。

### <a name="looping-through-file-lists"></a>重複瀏覽檔案清單

如果來源轉換會反覆處理多個檔案 (而不是透過 For Each 活動來循環)，對應資料流的執行效能會更好。 我們建議在您的來源轉換中使用萬用字元或檔案清單。 如果允許 Spark 叢集中進行迴圈瀏覽，則資料流程程序會執行得更快。 如需詳細資訊，請參閱[在來源轉換中使用萬用字元](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

例如，如果您想要在 Blob 儲存體的資料夾中處理自 2019 年 7 月以來的資料檔案清單，以下是您可以在來源轉換中使用的萬用字元。

```DateFiles/*_201907*.txt```

藉由使用萬用字元，您的管線將只會包含一個資料流程活動。 比起使用 ForEach 搭配內部「執行資料流程」活動來對 Blob 存放區進行查閱，然後再逐一查看所有相符檔案，此方式可提供更好的效能。

針對每個已執行的資料流程活動，平行模式中的 For Each 管線都會藉由啟動作業叢集來繁衍多個叢集。 這可能會導致 Azure 服務因為大量平行執行而產生節流。 不過，如果在 For Each 內部使用「執行資料流程」，而且管線中已設定順序，則可避免節流和資源耗盡。 這會強制 Data Factory 依序對資料流程執行每個檔案。

如果您搭配有順序的資料流程來使用 For Each，建議您利用 Azure Integration Runtime 中的 TTL 設定。 這是因為每個檔案都會在您的迭代器內產生完整的 5 分鐘叢集啟動時間。

### <a name="optimizing-for-cosmosdb"></a>針對 CosmosDB 進行最佳化

如果在 CosmosDB 接收上設定輸送量和批次屬性，則只會在從管線資料流程活動執行該資料流程時生效。 執行資料流程之後，CosmosDB 就會接受原始的集合設定。

* 批次大小：計算資料的粗略資料列大小，並確認 rowSize * 批次大小是否小於200 萬。 如果是，請增加批次大小以取得更好的輸送量
* 輸送量：請在這裡設定較高的輸送量設定，以允許文件更快速地寫入 CosmosDB。 請記住，高輸送量設定會產生較高的 RU 成本。
*   寫入輸送量預算：使用小於每分鐘總 RU 數的值。 如果您的資料流程具有大量的 Spark 分割區，則設定預算輸送量會允許在這些分割區之間達到更好的平衡。

## <a name="join-performance"></a>聯結效能

管理資料流程中的聯結效能是一項很常見的作業，您會在資料轉換的整個生命週期中執行此作業。 在 ADF 中，資料流程不需要在聯結之前排序資料，因為這些作業是以 Spark 中的雜湊聯結執行。 不過，您可以透過適用於聯結 (Join)、存在 (Exist) 和查閱 (Lookup) 轉換的「廣播」聯結最佳化，來獲得改善的效能。

這可藉由將聯結關聯性任一端的內容向下推送至 Spark 節點，來避免即時重組。 這適用於參考查閱所用的小型資料表。 可能無法放入節點記憶體中的較大資料表不適用於廣播最佳化。

對於具有許多聯結作業的資料流程，建議的設定是將最佳化設為「自動」進行「廣播」，並使用記憶體最佳化的 Azure Integration Runtime 設定。 如果您在資料流程執行期間遇到記憶體不足的錯誤或廣播逾時，您可以關閉廣播最佳化。 不過，這會造成資料流程的執行速度變慢。 (選擇性) 您可以指示資料流程只下推聯結的左側或右側 (或兩者)。

![廣播設定](media/data-flow/newbroad.png "廣播設定")

另一個聯結最佳化是讓建置的聯結可避免 Spark 傾向於執行交叉聯結。 例如，當您在聯結條件中包含常值時，Spark 可能會認為必須先執行完整的笛卡兒乘積，然後再篩選出聯結的值。 但是，如果您確定聯結條件的兩端都有資料行值，您可以避免此 Spark 引發的笛卡兒乘積，並改善您的聯結和資料流程效能。

## <a name="next-steps"></a>後續步驟

請參閱其他與效能相關的資料流程文章：

- [資料流程最佳化索引標籤](concepts-data-flow-overview.md#optimize)
- [資料流程活動](control-flow-execute-data-flow-activity.md)
- [監視資料流程效能](concepts-data-flow-monitoring.md)
