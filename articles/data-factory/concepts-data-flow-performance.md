---
title: 對應資料流的效能和調整指南
description: 了解哪些關鍵因素會影響 Azure Data Factory 中對應資料流的效能。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 07/27/2020
ms.openlocfilehash: 55483b93b770687703b381366d48edbc7d48f26e
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475292"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>對應資料流的效能和調整指南

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

對應 Azure Data Factory 中的資料流程提供無程式碼介面，以大規模設計和執行資料轉換。 如果您不熟悉對應資料流，請參閱[對應資料流概觀](concepts-data-flow-overview.md)。 本文著重于各種微調和優化資料流程的方式，使其符合您的效能基準測試。

觀看下列影片，以瞭解如何將資料轉換成資料流程的一些範例時間。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>測試資料流程邏輯

當您從 ADF UX 設計和測試資料流程時，debug 模式可讓您以互動方式對即時 Spark 叢集進行測試。 這可讓您預覽資料並執行您的資料流程，而不需要等候叢集準備。 如需詳細資訊，請參閱[偵錯模式](concepts-data-flow-debug-mode.md)。

## <a name="monitoring-data-flow-performance"></a>監視資料流程效能

一旦您使用「偵測」模式來驗證轉換邏輯，請在管線中以活動的形式執行資料流程端對端。 資料流程會使用「[執行資料流程」活動](control-flow-execute-data-flow-activity.md)在管線中運作。 相較于顯示轉換邏輯詳細執行計畫和效能設定檔的其他 Azure Data Factory 活動，資料流程活動具有獨特的監視體驗。 若要查看資料流程的詳細監視資訊，請按一下管線的活動執行輸出中的 [眼鏡] 圖示。 如需詳細資訊，請參閱[監視對應資料流](concepts-data-flow-monitoring.md)。

![資料流程監視](media/data-flow/monitoring-details.png "資料流程監視 2")

監視資料流程效能時，有四個可能的瓶頸需要注意：

* 叢集啟動時間
* 從來源讀取
* 轉換時間
* 寫入至接收 

![資料流程監視](media/data-flow/monitoring-performance.png "資料流程監視 3")

叢集啟動時間是加速 Apache Spark 叢集所需的時間。 這個值位於 [監視] 畫面的右上角。 資料流程會在即時模型上執行，其中每個工作都使用隔離的叢集。 此啟動時間通常需要3-5 分鐘。 若為連續作業，可以藉由啟用存留時間值來減少這種情況。 如需詳細資訊，請參閱[優化 Azure Integration Runtime](#ir)。

資料流程利用 Spark 優化程式，在「階段」中重新排序並執行您的商務邏輯，以儘快執行。 對於資料流程寫入的每個接收，監視輸出會列出每個轉換階段的持續時間，以及將資料寫入接收器所需的時間。 最大的時間可能是資料流程的瓶頸。 如果採用最大的轉換階段包含來源，您可能會想要進一步優化您的讀取時間。 如果轉換耗費較長的時間，則您可能需要重新分割或增加您的 integration runtime 大小。 如果接收處理時間很大，您可能需要相應增加您的資料庫，或確認您不會輸出到單一檔案。

一旦您識別資料流程的瓶頸之後，請使用下列優化策略來改善效能。

## <a name="optimize-tab"></a>最佳化索引標籤

[**優化**] 索引標籤包含設定 Spark 叢集之資料分割配置的設定。 此索引標籤存在於每個資料流程轉換中，並指定是否要在轉換完成**之後**重新分割資料。 調整資料分割可讓您控制跨計算節點和資料位置的優化，對整體資料流程效能有正面和負面影響。

![最佳化](media/data-flow/optimize.png "最佳化")

預設會選取 [*使用目前*的資料分割]，以指示 Azure Data Factory 保留轉換目前的輸出資料分割。 當重新分割資料需要時間時，在大部分的情況下建議*使用目前的分割*區。 您可能想要重新分割資料的案例包括在匯總和聯結之後，大幅扭曲您的資料，或在 SQL DB 上使用來源分割。

若要變更任何轉換的資料分割，請選取 [**優化**] 索引標籤，然後選取 [**設定分割**] 選項按鈕。 您會看到一系列的資料分割選項。 分割的最佳方法會根據您的資料磁片區、候選索引鍵、null 值和基數而有所不同。 

> [!IMPORTANT]
> 單一分割區會將所有分散式資料結合成單一資料分割。 這是非常緩慢的作業，它也會大幅影響所有下游轉換和寫入。 Azure Data Factory 強烈建議使用此選項，除非有明確的商業理由要這麼做。

每個轉換都有下列資料分割選項可用：

### <a name="round-robin"></a>迴圈配置資源 

迴圈配置資源會在分割區之間平均分散資料。 當您沒有良好的金鑰候選項目來實行穩固的智慧型資料分割策略時，請使用迴圈配置資源。 您可以設定實體分割區的數目。

### <a name="hash"></a>雜湊

Azure Data Factory 會產生資料行的雜湊來產生統一的分割區，讓具有類似值的資料列落在相同的分割區中。 當您使用 Hash 選項時，請測試可能的分割區誤差。 您可以設定實體分割區的數目。

### <a name="dynamic-range"></a>動態範圍

動態範圍會根據您提供的資料行或運算式來使用 Spark 動態範圍。 您可以設定實體分割區的數目。 

### <a name="fixed-range"></a>固定範圍

建立運算式，為分割資料行內的值提供固定範圍。 若要避免分割區扭曲，您應該先充分瞭解您的資料，再使用此選項。 您為運算式輸入的值會當做資料分割函數的一部分來使用。 您可以設定實體分割區的數目。

### <a name="key"></a>機碼

如果您對資料的基數有充分的瞭解，則索引鍵分割可能是很好的策略。 索引鍵分割會針對資料行中的每個唯一值建立資料分割。 您無法設定分割區數目，因為此數目是以資料中的唯一值為基礎。

> [!TIP]
> 手動設定資料分割配置會 reshuffles 資料，而且可以彌補 Spark 優化器的優點。 最佳做法是不要手動設定資料分割，除非您需要。

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a>優化 Azure Integration Runtime

資料流程會在執行時間啟動的 Spark 叢集上執行。 所用叢集的設定是在活動的 integration runtime （IR）中定義。 定義您的整合執行時間時，需要進行三個效能考慮：叢集類型、叢集大小和存留時間。

如需如何建立 Integration Runtime 的詳細資訊，請參閱 [Azure Data Factory 中的 Integration Runtime](concepts-integration-runtime.md)。

### <a name="cluster-type"></a>叢集類型

有三個可用的 Spark 叢集類型選項： [一般用途]、[記憶體優化] 和 [計算優化]。

**一般用途**叢集是預設選項，適用于大多數的資料流程工作負載。 這些通常是效能和成本的最佳平衡。

如果您的資料流程有許多聯結和查閱，您可能會想要使用**記憶體優化**的叢集。 記憶體優化叢集可以將更多的資料儲存在記憶體中，並將您可能得到的任何記憶體不足錯誤降至最低。 記憶體優化具有每個核心的最高價格點，但也傾向于導致管線更成功。 如果您在執行資料流程時遇到記憶體不足的錯誤，請切換至記憶體優化的 Azure IR 設定。 

**計算優化**不適用於 ETL 工作流程，Azure Data Factory 小組不建議用於大部分的生產環境工作負載。 針對較簡單的非記憶體密集型資料轉換（例如篩選資料或新增衍生的資料行），每個核心可使用計算優化的叢集，價格較低。

### <a name="cluster-size"></a>叢集大小

資料流程會將資料處理散發到 Spark 叢集中的不同節點，以平行方式執行作業。 具有更多核心的 Spark 叢集會增加計算環境中的節點數目。 更多節點會增加資料流程的處理能力。 增加叢集的大小通常是減少處理時間的簡單方法。

預設叢集大小是四個驅動程式節點和四個背景工作節點。  當您處理更多資料時，建議使用較大的群集。 以下是可能的調整大小選項：

| 工作者核心 | 驅動程式核心 | 核心總數 | 備忘稿 |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | 不適用於計算優化 |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

資料流程的定價是 vcore-小時，這表示叢集大小和執行時間因素都在此。 當您相應增加時，您的叢集每分鐘成本會增加，但整體時間將會降低。

> [!TIP]
> 叢集的大小有多大的影響資料流程的效能。 視您的資料大小而定，增加叢集大小的一點將會停止改善效能。 例如，如果您的節點數超過資料的分割區，則新增其他節點將不會有説明。 最佳做法是從小規模開始，並相應增加以符合您的效能需求。 

### <a name="time-to-live"></a>存留時間

根據預設，每個資料流程活動都會依據 IR 設定來旋轉新的叢集。 叢集啟動需要幾分鐘的時間，而且資料處理無法啟動直到完成為止。 如果您的管線包含多個**連續**的資料流程，您可以啟用存留時間（TTL）值。 指定存留時間值可讓叢集在執行完成後的一段特定時間保持運作。 如果新作業在 TTL 期間開始使用 IR，它會重複使用現有的叢集，而啟動時間將會以秒為單位，而不是分鐘。 第二個作業完成之後，叢集會再次保持運作，以取得 TTL 時間。

一次只能在單一叢集上執行一項作業。 如果有可用的叢集，但有兩個數據流開始，則只有一個會使用即時叢集。 第二個作業會啟動自己的隔離叢集。

如果大部分的資料流程都以平行方式執行，則不建議您啟用 TTL。 

> [!NOTE]
> 使用自動解析整合執行時間時，無法使用存留時間

## <a name="optimizing-sources"></a>優化來源

對於 Azure SQL Database 以外的每個來源，建議您保留**使用目前**的資料分割做為選取的值。 從所有其他來源系統讀取時，資料流程會根據資料的大小，將資料平均地分割。 大約每 128 MB 的資料會建立新的分割區。 隨著資料大小的增加，磁碟分割數目也會增加。

任何自訂的資料分割都會在 Spark 讀取資料*之後*發生，而且會對資料流程效能造成負面影響。 由於資料會在讀取時平均分割，因此不建議這麼做。 

> [!NOTE]
> 讀取速度可以受到來源系統的輸送量限制。

### <a name="azure-sql-database-sources"></a>Azure SQL Database 來源

Azure SQL Database 具有稱為「來源」資料分割的唯一資料分割選項。 啟用來源資料分割可以在來源系統上啟用平行連線，藉此改善 Azure SQL DB 的讀取時間。 指定分割區的數目，以及如何分割資料。 使用具有高基數的分割區資料行。 您也可以輸入符合來源資料表之資料分割配置的查詢。

> [!TIP]
> 針對來源資料分割，SQL Server 的 i/o 是瓶頸。 加入太多資料分割可能會使源資料庫飽和。 使用此選項時，通常會有四或五個磁碟分割。

![來源分割](media/data-flow/sourcepart3.png "來源分割")

#### <a name="isolation-level"></a>隔離等級

Azure SQL 來源系統上讀取的隔離等級會影響效能。 選擇 [讀取未認可] 將會提供最快的效能，並防止任何資料庫鎖定。 若要深入瞭解 SQL 隔離等級，請參閱[瞭解隔離等級](https://docs.microsoft.com/sql/connect/jdbc/understanding-isolation-levels?view=sql-server-ver15)。

#### <a name="read-using-query"></a>使用查詢讀取

您可以使用資料表或 SQL 查詢來讀取 Azure SQL Database。 如果您執行的是 SQL 查詢，則必須先完成查詢，才能開始轉換。 SQL 查詢可以用來將執行速度較快的作業，減少從 SQL Server 讀取的資料量（例如 SELECT、WHERE 和 JOIN 語句）。 當您推送作業時，您會在資料進入資料流程之前，失去追蹤轉換歷程和效能的能力。

### <a name="azure-synapse-analytics-sources"></a>Azure Synapse 分析來源

使用 Azure Synapse 分析時，來源選項中會有一個稱為 [**啟用預備**環境] 的設定。 這可讓 ADF 使用[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15)從 Synapse 讀取，進而大幅提升讀取效能。 啟用 PolyBase 需要您指定 [資料流程] 活動設定中的 Azure Blob 儲存體或 Azure Data Lake Storage gen2 預備位置。

![啟用暫存](media/data-flow/enable-staging.png "啟用暫存")

### <a name="file-based-sources"></a>以檔案為基礎的來源

雖然資料流程支援各種不同的檔案類型，但 Azure Data Factory 建議使用 Spark 原生 Parquet 格式來取得最佳的讀取和寫入時間。

如果您在一組檔案上執行相同的資料流程，建議您從資料夾讀取，使用萬用字元路徑或從檔案清單讀取。 單一資料流程活動執行可以在 batch 中處理您的所有檔案。 如需如何設定這些設定的詳細資訊，請參閱連接器檔，例如[Azure Blob 儲存體](connector-azure-blob-storage.md#source-transformation)。

可能的話，請避免使用 For-每個活動，以執行一組檔案的資料流程。 這會導致每個的每個反復專案啟動自己的 Spark 叢集，這通常不是必要的，而且可能會很耗費資源。 

## <a name="optimizing-sinks"></a>優化接收

當資料流程寫入到接收時，任何自訂資料分割都會在寫入之前立即發生。 就像來源，在大多數情況下，建議您保留**使用目前**的資料分割做為選取的分割區選項。 分割資料的寫入速度會比未分割的資料快很多，即使您的目的地並未分割也一樣。 以下是各種接收類型的個別考慮。 

### <a name="azure-sql-database-sinks"></a>Azure SQL Database 接收

使用 Azure SQL Database，在大多數情況下，預設資料分割應可正常執行。 您的接收可能有太多資料分割，供您的 SQL 資料庫處理。 如果您遇到這種情況，請減少 SQL Database 接收所輸出的資料分割數目。

#### <a name="disabling-indexes-using-a-sql-script"></a>使用 SQL 腳本停用索引

在 SQL 資料庫中的負載前停用索引，可以大幅改善寫入資料表的效能。 在寫入您的 SQL 接收器之前，請執行下列命令。

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

寫入完成之後，請使用下列命令重建索引：

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

您可以使用 Azure SQL DB 或 Synapse 接收中的前置和後置 SQL 腳本，在對應的資料流程中以原生方式完成這些作業。

![停用索引](media/data-flow/disable-indexes-sql.png "停用索引")

> [!WARNING]
> 停用索引時，資料流程實際上會取得資料庫的控制權，而且查詢目前不太可能會成功。 因此，許多 ETL 作業會在夜間觸發，以避免發生這種衝突。 如需詳細資訊，請瞭解[停用索引的條件約束](https://docs.microsoft.com/sql/relational-databases/indexes/disable-indexes-and-constraints?view=sql-server-ver15)

#### <a name="scaling-up-your-database"></a>相應增加您的資料庫

在您的管線執行之前，您可以為來源及接收 Azure SQL DB 和 DW 排定調整大小作業來增加輸送量，並在達到 DTU 限制時將 Azure 節流降至最低。 當您的管線執行完成之後，請將您的資料庫大小調整回正常執行比率。

### <a name="azure-synapse-analytics-sinks"></a>Azure Synapse 分析接收

寫入 Azure Synapse 分析時，請確定 [**啟用預備**] 已設定為 [true]。 這可讓 ADF 使用[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)寫入，以有效地大量載入資料。 使用 PolyBase 時，您將需要參考 Azure Data Lake Storage gen2 或 Azure Blob 儲存體帳戶來暫存資料。

與 PolyBase 不同的是，相同的最佳作法適用于 Azure Synapse 分析，做為 Azure SQL Database。

### <a name="file-based-sinks"></a>以檔案為基礎的接收 

雖然資料流程支援各種不同的檔案類型，但 Azure Data Factory 建議使用 Spark 原生 Parquet 格式來取得最佳的讀取和寫入時間。

如果資料平均分佈，**使用目前的分割**區將會是寫入檔案的最快速分割選項。

#### <a name="file-name-options"></a>檔案名稱選項

寫入檔案時，您可以選擇每個都有效能影響的命名選項。

![接收選項](media/data-flow/file-sink-settings.png "接收選項")

選取**預設**選項會寫入最快。 每個分割區會等同于具有 Spark 預設名稱的檔案。 如果您只是從資料的資料夾讀取，這就很有用。

設定命名**模式**會將每個分割區檔案重新命名為更好用的使用者名稱。 此作業會在寫入後發生，而且會比選擇預設值稍微慢一點。 每個分割區可讓您手動命名每個個別的資料分割。

如果資料行對應至您想要輸出資料的方式，您可以選取 [當做**資料行中的資料**]。 這會 reshuffles 資料，而且如果資料行未平均散發，可能會影響效能。

**單一**檔案的輸出會將所有資料結合成單一分割區。 這會導致長時間的寫入時間，特別是針對大型資料集。 Azure Data Factory 團隊強烈建議您**不要**選擇此選項，除非有明確的商業理由要這麼做。

### <a name="cosmosdb-sinks"></a>CosmosDB 接收器

寫入至 CosmosDB 時，在資料流程執行期間改變輸送量和批次大小可改善效能。 這些變更只會在資料流程活動執行期間生效，並會在結束後回到原創組合設定。 

**批次大小：** 計算資料的粗略資料列大小，並確認資料列大小 * 批次大小小於2000000。 如果是，請增加批次大小以取得更好的輸送量

**輸送量：** 在這裡設定較高的輸送量設定，以允許檔更快速地寫入 CosmosDB。 請記住，以高輸送量設定為基礎的較高 RU 成本。

**寫入輸送量預算：** 使用小於每分鐘的 ru 總數的值。 如果您的資料流程具有大量的 Spark 分割區，則設定預算輸送量會允許在這些分割區之間進行更多的平衡。


## <a name="optimizing-transformations"></a>優化轉換

### <a name="optimizing-joins-exists-and-lookups"></a>優化聯結、存在和查閱

#### <a name="broadcasting"></a>廣播

在聯結、查閱和 exists 轉換中，如果其中一個或兩個數據流夠小，可納入背景工作節點記憶體，您可以藉由啟用**廣播**來優化效能。 廣播是指您將小型資料框架傳送到叢集中的所有節點。 這可讓 Spark 引擎執行聯結，而不需要重新輪換大型資料流程中的資料。 根據預設，Spark 引擎會自動決定是否要廣播聯結的一端。 如果您熟悉傳入的資料，並知道某個資料流程會明顯地小於另一個串流，則可以選取 [**固定**廣播]。 已修正廣播會強制 Spark 廣播選取的串流。 

如果廣播的資料大小太大而無法用於 Spark 節點，您可能會收到記憶體不足的錯誤。 若要避免記憶體不足的錯誤，請使用**記憶體優化**的叢集。 如果您在資料流程執行期間遇到廣播超時，您可以關閉廣播優化。 不過，這會造成資料流程的執行速度變慢。

![聯結轉換最佳化](media/data-flow/joinoptimize.png "聯結最佳化")

#### <a name="cross-joins"></a>交叉聯結

如果您在聯結條件中使用常值，或在聯結的兩端都有多個相符專案，則 Spark 會以交叉聯結的形式執行聯結。 交叉聯結是完整的笛卡兒乘積，會篩選出聯結的值。 這會比其他聯結類型明顯地慢。 請確定您在聯結條件的兩端都有資料行參考，以避免影響效能。

#### <a name="sorting-before-joins"></a>聯結前排序

不同於 SSIS 之類工具中的合併聯結，聯結轉換不是必要的合併聯結作業。 聯結索引鍵不需要在轉換之前進行排序。 Azure Data Factory 小組不建議在對應的資料流程中使用排序轉換。

### <a name="repartitioning-skewed-data"></a>重新分割扭曲的資料

某些轉換（例如聯結和匯總）會重新您的資料分割，有時可能會導致扭曲的資料。 扭曲的資料表示資料不會平均分散到分割區。 嚴重扭曲的資料可能會導致較慢的下游轉換和接收寫入。 您可以在資料流程執行中的任何時間點，按一下 [監視] 顯示中的轉換，以檢查資料的偏斜。

![偏斜和峰](media/data-flow/skewness-kurtosis.png "偏斜和峰")

監視顯示會顯示如何將資料分散到每個分割區，以及兩個度量、偏斜和峰。 **偏斜**指的是資料的非對稱程度，而且可以有正、零、負或未定義的值。 負誤差表示左側結尾比右邊長。 「**峰**度」（measure）是指資料為高尾或淺尾的量值。 不需要高峰度的值。 理想的偏斜範圍介於-3 到3之間，而峰的範圍則小於10。 解讀這些數位的簡單方法是查看分割區圖表，並查看1個橫條是否明顯大於其餘部分。

如果您的資料未在轉換後平均分割，您可以使用 [[優化]](#optimize-tab)索引標籤來重新分割。 重新輪換資料需要一些時間，而且可能無法改善您的資料流程效能。

> [!TIP]
> 如果您重新分割您的資料，但有重新資料的下游轉換，請在當做聯結索引鍵使用的資料行上使用雜湊分割。

## <a name="next-steps"></a>後續步驟

請參閱其他與效能相關的資料流程文章：

- [資料流程活動](control-flow-execute-data-flow-activity.md)
- [監視資料流程效能](concepts-data-flow-monitoring.md)
