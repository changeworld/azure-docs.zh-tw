---
title: 使用 Azure Data Factory 和 Azure Data Share 進行資料整合
description: 使用 Azure Data Factory 和 Azure Data Share 來複製、轉換和共用資料
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: fa424f7e1f5e1f885dd433b8abc8aae1dc1bc206
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006132"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>使用 Azure Data Factory 和 Azure Data Share 進行資料整合

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

客戶在採用現代化資料倉儲和分析專案的過程中，不僅需要更多的資料，也必須更深入了解其資料資產中的資料。 此研討會將探討如何經由 Azure Data Factory 和 Azure Data Share 的改良來簡化 Azure 中的資料整合與管理。 

從啟用無程式碼的 ETL/ELT，乃至於對您的資料建立完整的檢視，Azure Data Factory 的改良都可讓資料工程師安心地為您的企業導入更多資料，進而提升價值。 Azure Data Share 可讓您以受到控管的方式進行企業對企業共用。

在此研討會中，您將使用 Azure Data Factory (ADF)，將 Azure SQL Database 中的資料內嵌至 Azure Data Lake Storage Gen2 (ADLS Gen2)。 在您將資料放入 Lake 之後，您會透過對應資料流程 (資料處理站的原生轉換服務) 進行資料轉換，並將其接收至 Azure Synapse Analytics 中。 然後，您將會使用 Azure Data Share 與已轉換的資料和一些額外資料共用資料表。 

本實驗室中使用的資料是紐約市計程車資料。 若要將其匯入您在 SQL Database 中的資料庫，請下載 [taxi-data bacpac 檔案](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/)。

* **Azure SQL Database**：如果您沒有 SQL DB，請了解如何 [建立 SQL DB 帳戶](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)

* **Azure Data Lake Storage Gen2 儲存體帳戶**：如果您沒有 ADLS Gen2 儲存體帳戶，請了解如何 [建立 ADLS Gen2 儲存體帳戶](../storage/common/storage-account-create.md)。

* **Azure Synapse Analytics**：如果您沒有 Azure Synapse Analytics，請了解如何 [建立 Azure Synapse Analytics 執行個體](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)。

* **Azure Data Factory**：如果您尚未建立資料處理站，請參閱如何 [建立資料處理站](./quickstart-create-data-factory-portal.md)。

* **Azure Data Share**：如果您尚未建立資料共用，請參閱如何 [建立資料共用](../data-share/share-your-data.md#create-a-data-share-account)。

## <a name="set-up-your-azure-data-factory-environment"></a>設定您的 Azure Data Factory 環境

在本節中，您將了解如何從 Azure 入口網站存取 Azure Data Factory 使用者體驗 (ADF UX)。 在 ADF UX 中，您將為我們使用的每個資料存放區設定三個連結服務：Azure SQL DB、ADLS Gen2 和 Azure Synapse Analytics。

在 Azure Data Factory 連結服務中，請定義外部資源的連線資訊。 Azure Data Factory 目前支援超過 85 個連接器。

### <a name="open-the-azure-data-factory-ux"></a>開啟 Azure Data Factory UX

1. 在 Microsoft Edge 或 Google Chrome 中開啟 [Azure 入口網站](https://portal.azure.com)。
1. 使用頁面頂端的搜尋列，搜尋「資料處理站」

    ![入口網站 1](media/lab-data-flow-data-share/portal1.png)
1. 按一下您的資料處理站資源，以開啟其資源刀鋒視窗。

    ![入口網站 2](media/lab-data-flow-data-share/portal2.png)
1. 按一下 [撰寫和監視] 以開啟 ADF UX。 您也可以在 adf.azure.com 上存取 ADF UX。

    ![入口網站 3](media/lab-data-flow-data-share/portal3.png)
1. 系統會將您重新導向至 ADF UX 的首頁。 此頁面包含可供學習資料處理站概念的快速入門、指導影片和教學課程連結。 若要開始撰寫，請按一下左側提要欄位中的鉛筆圖示。

    ![入口網站設定](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>建立 Azure SQL Database 連結服務

1. [撰寫] 頁面可讓您建立資料處理站資源，例如管線、資料集、資料流程、觸發程序和連結服務。 若要建立連結服務，請按一下右下角的 [連線] 按鈕。

    ![入口網站設定 2](media/lab-data-flow-data-share/configure2.png)
1. 在 [連線] 索引標籤中按一下 [新增]，以新增連結服務。

    ![入口網站設定 3](media/lab-data-flow-data-share/configure3.png)
1. 您設定的第一個連結服務將是 Azure SQL DB。 您可以使用搜尋列來篩選資料存放區清單。 按一下 [Azure SQL Database] 磚，然後按一下 [繼續]。

    ![入口網站設定 4](media/lab-data-flow-data-share/configure-4.png)
1. 在 SQL DB 組態窗格中，輸入 'SQLDB' 作為連結服務名稱。 輸入您的認證，以允許資料處理站連線至您的資料庫。 如果您使用 SQL 驗證，請輸入伺服器名稱、資料庫、您的使用者名稱和密碼。 您可以按一下 [測試連線]，以確認連線資訊正確無誤。 在完成作業後，按一下 [建立]。

    ![入口網站設定 5](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>建立 Azure Synapse Analytics 連結服務

1. 重複相同的程序，以新增 Azure Synapse Analytics 連結服務。 在 [連線] 索引標籤中，按一下 [新增]。 選取 [Azure Synapse Analytics] 磚，然後按一下 [繼續]。

    ![入口網站設定 6](media/lab-data-flow-data-share/configure-6.png)
1. 在連結服務組態窗格中，輸入 'SQLDW' 作為連結服務名稱。 輸入您的認證，以允許資料處理站連線至您的資料庫。 如果您使用 SQL 驗證，請輸入伺服器名稱、資料庫、您的使用者名稱和密碼。 您可以按一下 [測試連線]，以確認連線資訊正確無誤。 在完成作業後，按一下 [建立]。

    ![入口網站設定 7](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>建立 Azure Data Lake Storage Gen2 連結服務

1. 此實驗室所需的最後一個連結服務是 Azure Data Lake Storage Gen2。  在 [連線] 索引標籤中，按一下 [新增]。 選取 [Azure Data Lake Storage Gen2] 磚，然後按一下 [繼續]。

    ![入口網站設定 8](media/lab-data-flow-data-share/configure8.png)
1. 在連結服務組態窗格中，輸入 'ADLSGen2' 作為連結服務名稱。 如果您使用帳戶金鑰驗證，請從 [儲存體帳戶名稱] 下拉式清單中選取您的 ADLS Gen2 儲存體帳戶。 您可以按一下 [測試連線]，以確認連線資訊正確無誤。 在完成作業後，按一下 [建立]。

    ![入口網站設定 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>開啟資料流程偵錯模式

在 *使用對應資料流程來轉換資料* 一節中，您將建置對應資料流程。 建置對應資料流程的最佳做法是開啟偵測模式，這可讓您在作用中的 Spark 叢集上快速測試轉換邏輯。

若要開啟偵錯，請按一下處理站頂端列中的 [資料流程偵錯] 滑桿。 在確認對話方塊快顯時，按一下 [確定]。 啟動叢集約需要 5-7 分鐘的時間。 在初始化期間，請繼續「使用複製活動將資料從 Azure SQL DB 內嵌至 ADLS Gen2」。

![入口網站設定 10](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>使用複製活動內嵌資料

在本節中，您將建立具有複製活動的管線，以將 Azure SQL DB 中的一個資料表內嵌至 ADLS Gen2 儲存體帳戶。 您將了解如何透過 ADF UX 來新增管線、設定資料集，以及對管線進行偵錯。 本節中使用的設定模式，在從關聯式資料存放區複製到以檔案為基礎的資料存放區時也適用。

在 Azure Data Factory 中，管線是共同執行某項工作的活動所組成的邏輯群組。 活動會定義要對您的資料執行的作業。 資料集會指向您想要在連結服務中使用的資料。

### <a name="create-a-pipeline-with-a-copy-activity"></a>建立具有複製活動的管線

1. 在 [處理站資源] 窗格中按一下加號圖示，以開啟 [新增資源] 功能表。 選取 [管線]。

    ![入口網站複製 1](media/lab-data-flow-data-share/copy1.png)
1. 在管線畫布的 [一般] 索引標籤中，將您的管線命名為 'IngestAndTransformTaxiData' 之類的描述性名稱。

    ![入口網站複製 2](media/lab-data-flow-data-share/copy2.png)
1. 在管線畫布的 [活動] 窗格中，開啟 [移動和轉換] Accordion，並將 [複製資料] 活動拖曳至畫布上。 為複製活動提供描述性的名稱，例如 'IngestIntoADLS'。

    ![入口網站複製 3](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>設定 Azure SQL DB 來源資料集

1. 按一下複製活動的 [來源] 索引標籤。 若要建立新的資料集，請按一下 [新增]。 您的來源會是 'dbo.TripData' 資料表，位於先前設定的連結服務 'SQLDB' 中。

    ![入口網站複製 4](media/lab-data-flow-data-share/copy4.png)
1. 搜尋 **Azure SQL Database**，然後按一下 [繼續]。

    ![入口網站複製 5](media/lab-data-flow-data-share/copy-5.png)
1. 呼叫您的資料集 'TripData'。 選取 'SQLDB' 作為連結服務。 從資料表名稱下拉式清單中選取資料表名稱 'dbo.TripData'。 **從連線/存放區** 匯入結構描述。 在完成作業後，按一下 [確定]。

    ![入口網站複製 6](media/lab-data-flow-data-share/copy6.png)

您已成功建立來源資料集。 在來源設定中，請確定您已在 [使用查詢] 欄位中選取預設值 [資料表]。

### <a name="configure-adls-gen2-sink-dataset"></a>設定 ADLS Gen2 接收資料集

1. 按一下複製活動的 [接收] 索引標籤。 若要建立新的資料集，請按一下 [新增]。

    ![入口網站複製 7](media/lab-data-flow-data-share/copy7.png)
1. 搜尋 **Azure Data Lake Storage Gen2**，然後按一下 [繼續]。

    ![入口網站複製 8](media/lab-data-flow-data-share/copy8.png)
1. 在 [選取格式] 窗格中，和寫入至 csv 檔案一樣選取 [DelimitedText]。 按一下 [繼續]。

    ![入口網站複製 9](media/lab-data-flow-data-share/copy9.png)
1. 將您的接收資料集命名為 'TripDataCSV'。 選取 'ADLSGen2' 作為連結服務。 輸入您要在其中寫入 csv 檔案的位置。 例如，您可以將資料寫入至容器 `staging-container` 中的檔案 `trip-data.csv`。 將 [使用第一個資料列做為標頭] 設定為 true，因為您想要讓輸出資料具有標頭。 由於目的地尚無檔案存在，請將 [匯入結構描述] 設定為 [無]。 在完成作業後，按一下 [確定]。

    ![入口網站複製 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>使用管線偵錯執行測試複製活動

1. 若要驗證您的複製活動是否正常運作，請按一下管線畫布頂端的 [偵錯]，以執行偵錯執行。 偵錯執行可讓您先對管線進行端對端或尋找中斷點的測試，再將管線發佈至資料處理站服務。

    ![入口網站複製 11](media/lab-data-flow-data-share/copy11.png)
1. 若要監視您的偵錯執行，請移至管線畫布的 [輸出] 索引標籤。 監視畫面每隔 20 秒會自動重新整理一次，或是在您手動按一下 [重新整理] 按鈕時重新整理。 複製活動具有特殊的監視檢視，可藉由按一下 [動作] 資料行中的眼鏡圖示來存取。

    ![入口網站複製 12](media/lab-data-flow-data-share/copy12.png)
1. 複製監視檢視會提供活動的執行詳細資料和效能特性。 您可以查看已讀取/寫入的資料、已讀取/寫入的資料列、已讀取/寫入的檔案以及輸送量等資訊。 如果一切都已正確設定，您應該會看到 49,999 個資料列寫入至 ADLS 接收中的一個檔案。

    ![入口網站複製 13](media/lab-data-flow-data-share/copy13.png)
1. 繼續進行下一節之前，建議您按一下處理站頂端列中的 [全部發佈]，將變更發佈至資料處理站服務。 雖然此實驗室中並未涵蓋完整的 Git 整合，但 Azure Data Factory 可加以支援。 Git 整合可用於版本控制、在存放庫中反覆儲存，以及在資料處理站上進行共同作業。 如需詳細資訊，請參閱 [Azure Data Factory中的原始檔控制](./source-control.md#troubleshooting-git-integration)。

    ![入口網站發佈 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>使用對應資料流程來轉換資料

您已將資料成功複製到 Azure Data Lake Storage 中，接下來可以將該資料聯結並彙總到資料倉儲中。 我們將使用對應資料流程，這是 Azure Data Factory 的視覺化設計轉換服務。 對應資料流程可讓使用者開發無程式碼的轉換邏輯，並在由 ADF 服務管理的 Spark 叢集上加以執行。

在此步驟中建立的資料流程會根據四個索引鍵資料行，內部聯結上一節中建立的 'TripDataCSV' 資料集與儲存在 'SQLDB' 中的資料表 'dbo.TripFares'。 然後，資料會根據資料行 `payment_type` 進行彙總以計算特定欄位的平均值，並寫入 Azure Synapse Analytics 資料表中。

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>將資料流程活動新增至您的管線

1. 在管線畫布的 [活動] 窗格中，開啟 [移動和轉換] Accordion，並將 [資料流程] 活動拖曳至畫布上。

    ![入口網站資料流程 1](media/lab-data-flow-data-share/dataflow1.png)
1. 在開啟的側邊窗格中，選取 [建立新的資料流程]，然後選擇 [對應資料流程]。 按一下 [確定]。

    ![入口網站資料流程 2](media/lab-data-flow-data-share/dataflow2.png)
1. 系統會將您導向至資料流程畫布，您將在此處建置轉換邏輯。 在 [一般] 索引標籤中，將您的資料流程命名為 'JoinAndAggregateData'。

    ![入口網站資料流程 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>設定您的車程資料 csv 來源

1. 您的首要工作是設定兩個來源轉換。 第一個來源會指向 'TripDataCSV' DelimitedText 資料集。 若要新增來源轉換，請按一下畫布中的 [新增來源] 方塊。

    ![入口網站資料流程 4](media/lab-data-flow-data-share/dataflow4.png)
1. 將您的來源命名為 'TripDataCSV'，然後從 [來源] 下拉式清單中選取 [TripDataCSV] 資料集。 您應該還記得，您在建立此資料集之初並未匯入結構描述，因為沒有任何資料。 由於現已有 `trip-data.csv` 存在，請按一下 [編輯] 以移至 [資料集設定] 索引標籤。

    ![入口網站資料流程 5](media/lab-data-flow-data-share/dataflow5.png)
1. 移至 [結構描述] 索引標籤，然後按一下 [匯入結構描述]。 從 [從連線/存放區]，以直接從檔案存放區匯入。 此時應該會出現 14 個類型字串的資料行。

    ![入口網站資料流程 6](media/lab-data-flow-data-share/dataflow6.png)
1. 返回資料流程 'JoinAndAggregateData'。 如果您的偵錯叢集已啟動 (以 [偵錯] 滑桿旁的綠色圓圈表示)，您可以在 [資料預覽] 索引標籤中取得資料的快照集。按一下 [重新整理] 以擷取資料預覽。

    ![入口網站資料流程 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> 資料預覽不會寫入資料。

### <a name="configure-your-trip-fares-sql-db-source"></a>設定您的車資 SQL DB 來源

1. 您所新增的第二個來源會指向 SQL DB 資料表 'dbo.TripFares'。 在您的 'TripDataCSV' 來源底下，會有另一個 [新增來源] 方塊。 請按一下該方塊以新增來源轉換。

    ![入口網站資料流程 8](media/lab-data-flow-data-share/dataflow8.png)
1. 將此來源命名為 'TripFaresSQL'。 按一下 [來源資料集] 欄位旁的 [新增]，以建立新的 SQL DB 資料集。

    ![入口網站資料流程 9](media/lab-data-flow-data-share/dataflow9.png)
1. 選取 [Azure SQL Database] 磚，然後按一下 [繼續]。 *注意：您可能會發現，資料處理站中有許多連接器在對應資料流程中不受支援。若要轉換其中一個來源的資料，請使用複製活動將其內嵌至支援的來源中*。

    ![入口網站資料流程 10](media/lab-data-flow-data-share/dataflow-10.png)
1. 呼叫您的資料集 'TripFares'。 選取 'SQLDB' 作為連結服務。 從資料表名稱下拉式清單中選取資料表名稱 'dbo.TripFares'。 **從連線/存放區** 匯入結構描述。 在完成作業後，按一下 [確定]。

    ![入口網站資料流程 11](media/lab-data-flow-data-share/dataflow11.png)
1. 若要驗證您的資料，請在 [資料預覽] 索引標籤中擷取資料預覽。

    ![入口網站資料流程 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>內部聯結 TripDataCSV 與 TripFaresSQL

1. 若要新增轉換，請按一下 'TripDataCSV' 右下角的加號圖示。 在 [多個輸入/輸出] 底下，選取 [聯結]。

    ![入口網站聯結 1](media/lab-data-flow-data-share/join1.png)
1. 將聯結轉換命名為 'InnerJoinWithTripFares'。 從 [右側資料流] 下拉式清單中選取 [TripFaresSQL]。 選取 [內部] 作為聯結類型。 若要深入了解對應資料流程中的不同聯結類型，請參閱[聯結類型](./data-flow-join.md#join-types)。

    透過 [聯結條件] 下拉式清單，從每個資料流程中選取您要比對的資料行。 若要新增其他聯結條件，請按一下現有條件旁的加號圖示。 根據預設，所有聯結條件都會以 AND 運算子進行合併，這表示必須符合所有條件才會產生相符項目。 在此實驗室中，我們想要比對資料行 `medallion`、`hack_license`、`vendor_id` 和 `pickup_datetime`

    ![入口網站聯結 2](media/lab-data-flow-data-share/join2.png)
1. 確認您透過資料預覽成功將 25 個資料行聯結在一起。

    ![入口網站聯結 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>依 payment_type 彙總

1. 在完成聯結轉換後，按一下 'InnerJoinWithTripFares' 旁的加號圖示，以新增彙總轉換。 選擇 [結構描述修飾元] 底下的 [彙總]。

    ![入口網站彙總 1](media/lab-data-flow-data-share/agg1.png)
1. 將您的彙總轉換命名為 'AggregateByPaymentType'。 選取 `payment_type` 作為分組依據資料行。

    ![入口網站彙總 2](media/lab-data-flow-data-share/agg2.png)
1. 移至 [彙總] 索引標籤。在此處，您將指定兩個彙總：
    * 依付款類型分組的平均費用
    * 依付款類型分組的總車程距離

    首先，您將建立平均費用運算式。 在標示為 [新增或選取資料行] 的文字方塊中，輸入 'average_fare'。

    ![入口網站彙總 3](media/lab-data-flow-data-share/agg3.png)
1. 若要輸入彙總運算式，請按一下標示為 [輸入運算式] 的藍色方塊。 這會開啟資料流程運算式產生器，此工具可用來以視覺化方式建立使用輸入結構描述、內建函式和作業以及使用者定義參數的資料流程運算式。 如需運算式產生器功能的詳細資訊，請參閱[運算式產生器文件](./concepts-data-flow-expression-builder.md)。

    若要取得平均費用，請使用 `avg()` 彙總函式，透過 `toInteger()` 來彙總轉換成整數的 `total_amount` 資料行。 在資料流程運算式語言中，這會定義為 `avg(toInteger(total_amount))`。 在完成作業後，按一下 [儲存後結束]。

    ![入口網站彙總 4](media/lab-data-flow-data-share/agg4.png)
1. 若要新增其他彙總運算式，請按一下 `average_fare` 旁的加號圖示。 選取 [新增資料行]。

    ![入口網站彙總 5](media/lab-data-flow-data-share/agg5.png)
1. 在標示為 [新增或選取資料行] 的文字方塊中，輸入 'total_trip_distance'。 和上一個步驟相同，開啟運算式產生器，並在運算式中輸入。

    若要取得總車程距離，請使用 `sum()` 彙總函式，透過 `toInteger()` 來彙總轉換成整數的 `trip_distance` 資料行。 在資料流程運算式語言中，這會定義為 `sum(toInteger(trip_distance))`。 在完成作業後，按一下 [儲存後結束]。

    ![入口網站彙總 6](media/lab-data-flow-data-share/agg6.png)
1. 在 [資料預覽] 索引標籤中測試轉換邏輯。如您所見，資料列和資料行會遠比先前少。 只有在此轉換中定義的三個分組依據和彙總資料行會繼續往下執行。 由於範例中只有五個付款類型群組，因此只會輸出五個資料列。

    ![入口網站彙總 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>設定您的 Azure Synapse Analytics 接收

1. 我們已完成轉換邏輯，接著即可在 Azure Synapse Analytics 資料表中接收資料。 請在 [目的地] 區段底下新增接收轉換。

    ![入口網站接收 1](media/lab-data-flow-data-share/sink1.png)
1. 將您的接收命名為 'SQLDWSink'。 按一下 [接收資料集] 欄位旁的 [新增]，以建立新的 Azure Synapse Analytics 資料集。

    ![入口網站接收 2](media/lab-data-flow-data-share/sink2.png)

1. 選取 [Azure Synapse Analytics] 磚，然後按一下 [繼續]。

    ![入口網站接收 3](media/lab-data-flow-data-share/sink-3.png)
1. 呼叫您的資料集 'AggregatedTaxiData'。 選取 'SQLDW' 作為連結服務。 選取 [建立新的資料表]，並將新的資料表命名為 dbo.AggregateTaxiData。 在完成作業後，按一下 [確定]

    ![入口網站接收 4](media/lab-data-flow-data-share/sink4.png)
1. 移至接收的 [設定] 索引標籤。 我們要建立新的資料表，因此必須選取資料表動作底下的 [重新建立資料表]。 將 [啟用暫存] 取消選取，這會在逐列插入或按批次插入之間切換。

    ![入口網站接收 5](media/lab-data-flow-data-share/sink5.png)

您已成功建立資料流程。 接下來即可在管線活動中加以執行。

### <a name="debug-your-pipeline-end-to-end"></a>對管線進行端對端的偵錯

1. 回到 **IngestAndTransformData** 管線的索引標籤。 請留意 'IngestIntoADLS' 複製活動上的綠色方塊。 將其拖曳到 'JoinAndAggregateData' 資料流程活動上。 這會建立「成功時的動作」，而使資料流程活動只會在複製成功時執行。

    ![入口網站管線 1](media/lab-data-flow-data-share/pipeline1.png)
1. 和先前的複製活動作業一樣，按一下 [偵錯] 以執行偵錯執行。 針對偵錯執行，資料流程活動會使用作用中的偵錯叢集，而不是啟動新的叢集。 執行此管線需要一分多鐘的時間。

    ![入口網站管線 2](media/lab-data-flow-data-share/pipeline2.png)
1. 如同複製活動，資料流程具有特殊的監視檢視，可在活動完成時以眼鏡圖示來存取。

    ![入口網站管線 3](media/lab-data-flow-data-share/pipeline3.png)
1. 在 [監視] 檢視中，您可以看到簡化的資料流程圖，以及每個執行階段的執行時間和資料列。 如果正確完成，您應該會在此活動中將 49,999 個資料列彙總成五個資料列。

    ![入口網站管線 4](media/lab-data-flow-data-share/pipeline4.png)
1. 您可以按一下轉換來取得其執行的其他詳細資料，例如分割資訊和新增/更新/卸載的資料行。

    ![入口網站管線 5](media/lab-data-flow-data-share/pipeline5.png)

您現在已完成此實驗室的資料處理站部分。 如果您想要使用觸發程序加以運作，請發佈您的資源。 您已使用複製活動成功執行將 Azure SQL Database 中的資料內嵌至 Azure Data Lake Storage 的管線，然後將該資料彙總至 Azure Synapse Analytics 中。 您可以查看 SQL Server 本身，以確認資料已成功寫入。

## <a name="share-data-using-azure-data-share"></a>使用 Azure Data Share 共用資料

在本節中，您將了解如何使用 Azure 入口網站來設定新的資料共用。 進行此作業時，您將建立新的資料共用，其中包含來自 Azure Data Lake Store Gen2 和 Azure Synapse Analytics 的資料集。 接著，您將設定快照集排程，讓資料取用者能夠選擇自動重新整理其共用的資料。 然後，您將邀請收件者加入您的資料共用。 

建立資料共用後，您將切換身分，而成為 *資料取用者*。 身為資料取用者，您將逐步執行接受資料共用邀請的流程、設定要接收資料的位置，並將資料集對應至不同的儲存位置。 接著，您將觸發快照集，而將與您共用的資料複製到指定的目的地。 

### <a name="sharing-data-data-provider-flow"></a>共用資料 (Data Provider 流程)

1. 在 Microsoft Edge 或 Google Chrome 中開啟 Azure 入口網站。

1. 使用頁面頂端的搜尋列，搜尋 **資料共用**

    ![入口網站 adls](media/lab-data-flow-data-share/portal-ads.png)

1. 選取名稱中含有 'Provider' 的資料共用帳戶。 例如 **DataProvider0102**。 

1. 選取 [開始共用資料]

    ![開始共用](media/lab-data-flow-data-share/ads-start-sharing.png)

1. 選取 [+ 建立] 以開始設定新的資料共用。 

1. 在 [共用名稱] 底下，指定您選擇的名稱。 這是您的資料取用者所將看到的共用名稱，因此請務必為其提供描述性名稱，例如 TaxiData。

1. 在 [描述] 底下放入一個句子，以說明資料共用的內容。 資料共用將包含全球各地的計程車車程資料，這些資料儲存於多個存放區中，包括 Azure Synapse Analytics 和 Azure Data Lake Store。 

1. 在 [使用規定] 底下，指定您的資料取用者必須遵循的條款。 其範例包括「不得在組織外部散發這項資料」或「參考法律合約」。 

    ![共用詳細資料](media/lab-data-flow-data-share/ads-details.png)

1. 選取 [繼續]。 

1. 選取 [新增資料集] 

    ![新增資料集 1](media/lab-data-flow-data-share/add-dataset.png)

1. 選取 [Azure Synapse Analytics]，以從您的 ADF 轉換進入的 Azure Synapse Analytics 中選取資料表。

    ![新增資料集 SQL](media/lab-data-flow-data-share/add-dataset-sql.png)


1. 您在取得執行的指令碼後，才能繼續作業。 提供的指令碼會在 SQL 資料庫中建立使用者，以允許 Azure Data Share MSI 代表他進行驗證。 

> [!IMPORTANT]
> 執行指令碼之前，您必須將自己設定為 SQL Server 的 Active Directory 管理員。 

1. 開啟新的索引標籤並瀏覽至 Azure 入口網站。 複製提供的指令碼，以在您要從中共用資料的資料庫中建立使用者。 為此，請使用查詢總管 (預覽) 以 AAD 驗證登入 EDW 資料庫。 

    您必須修改指令碼，將建立的使用者包含在括弧內。 例如：
    
    create user [dataprovider-xxxx] from external login;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. 切換回您將資料集新增至資料共用的 Azure Data Share。 

1. 選取 [EDW]，然後選取資料表的 [AggregatedTaxiData]。 

1. 選取 [新增資料集]

    現在已有一個 SQL 資料表屬於我們的資料集。 接下來，我們將從 Azure Data Lake Store 新增其他資料集。 

1. 選取 [新增資料集]，然後選取 [Azure Data Lake Store Gen2]

    ![新增資料集 adls](media/lab-data-flow-data-share/add-dataset-adls.png)

1. 選取 [**下一步**]

1. 展開 *wwtaxidata*。 展開 *Boston Taxi Data*。 請注意，您可以向下共用至檔案層級。 

1. 選取 *Boston Taxi Data* 資料夾，將整個資料夾新增至您的資料共用。 

1. 選取 [新增資料集]

1. 檢閱已新增的資料集。 您應已將 SQL 資料表和 ADLS Gen2 資料夾新增至您的資料共用。 

1. 選取 [繼續]

1. 在此畫面中，您可以將收件者新增至資料共用。 您新增的收件者將會收到您資料共用的邀請。 基於此實驗室的目的，您必須新增 2 個電子郵件地址：

    1. 您所在之 Azure 訂用帳戶的電子郵件地址。 

        ![新增收件者](media/lab-data-flow-data-share/add-recipients.png)

    1. 新增名為 *janedoe@fabrikam.com* 的虛構資料取用者。

1. 在此畫面中，您可以為資料取用者設定快照集設定。 此設定可讓他們依據您定義的間隔接收資料的定期更新。 

1. 勾選 [快照集排程]，並使用 [週期性] 下拉式清單設定每小時一次的資料重新整理。  

1. 選取 [建立]。

    您現在已有作用中的資料共用。 請檢閱您在建立資料共用時所能看到的資料提供者。 

1. 選取您所建立的資料共用，其標題為 **DataProvider**。 您可以選取 [資料共用] 中的 [已傳送的共用]，以瀏覽至該共用。 

1. 按一下 [快照集排程]。 您可以停用您所選擇的快照集排程。 

1. 接著，選取 [資料集] 索引標籤。您可以在此資料共用建立後，將其他資料集新增至此共用。 

1. 選取 [共用訂用帳戶] 索引標籤。此時尚無共用訂用帳戶存在，因為資料取用者尚未接受您的邀請。

1. 瀏覽至 [邀請] 索引標籤。在此處，您會看到一份待決邀請清單。 

    ![待決邀請](media/lab-data-flow-data-share/pending-invites.png)

1. 選取 *janedoe@fabrikam.com* 的邀請。 選取 [刪除]。 如果您的收件者尚未接受邀請，他們將無法再執行此動作。 

1. 選取 **[記錄]** 索引標籤。此時尚未顯示任何項目，因為資料取用者尚未接受您的邀請並觸發快照集。 

### <a name="receiving-data-data-consumer-flow"></a>接收資料 (資料取用者流程)

我們已檢閱資料共用，接下來可以切換內容，並轉換為資料取用者的身分。 

您的收件匣中現在應該會有來自 Microsoft Azure 的 Azure Data Share 邀請。 請啟動 Outlook Web Access (outlook.com)，並使用為您的 Azure 訂用帳戶提供的認證登入。

在您應已收到的電子郵件中，按一下 [檢視邀請 >]。 此時，如果您接受資料提供者的資料共用邀請，您將會模擬資料取用者體驗。 

![電子郵件邀請](media/lab-data-flow-data-share/email-invite.png)

系統可能會提示您選取訂用帳戶。 請確實選取您在此實驗室中使用的訂用帳戶。 

1. 按一下標題為 *DataProvider* 的邀請。 

1. 在此邀請畫面中，您會看到您先前以資料提供者的身分設定之資料共用的各種詳細資訊。 請檢閱這些詳細資料，並接受使用規定 (如有提供)。

1. 選取您的實驗室已有的訂用帳戶和資源群組。 

1. 針對 [資料共用帳戶]，選取 [DataConsumer]。 您也可以建立新的資料共用帳戶。 

1. 在 [已接收的共用名稱] 旁，您會發現預設的共用名稱為資料提供者所指定的名稱。 請為共用提供易記名稱以說明您要接收的資料，例如 **TaxiDataShare**。

    ![邀請接受](media/lab-data-flow-data-share/consumer-accept.png)

1. 您可以選擇 [接受並立即設定] 或 [接受並稍後再設定]。 如果您選擇接受並立即設定，您將指定用來複製所有資料的儲存體帳戶。 如果您選擇接受但稍後再設定，共用中的資料集將不會對應，而您將需要手動加以對應。 我們稍後將選擇此選項。 

1. 選取 [接受並稍後再設定]。 

    設定此選項時將會建立共用訂用帳戶，但不會有可供資料存放之處，因為尚未對應目的地。 

    接下來，我們將設定資料共用的資料集對應。 

1. 選取 [已接收的共用] (您在步驟 5 中指定的名稱)。

    [觸發快照集] 會呈現為灰色，但共用處於作用中狀態。 

1. 選取 [資料集] 索引標籤。請注意，每個資料集都是未對應的，這表示資料沒有複製的目的地。 

    ![未對應的資料集](media/lab-data-flow-data-share/unmapped.png)

1. 選取 Azure Synapse Analytics 資料表，然後選取 [+ 對應至目標]。

1. 在畫面右側，選取 [目標資料類型] 下拉式清單。 

    您可以將 SQL 資料對應至各種不同的資料存放區。 在此案例中，我們會對應至 Azure SQL Database。

    ![對應](media/lab-data-flow-data-share/mapping-options.png)
    
    (選擇性) 選取 [Azure Data Lake Store Gen2] 作為目標資料類型。 
    
    (選擇性) 選取您先前一貫使用的訂用帳戶、資源群組和儲存體帳戶。 
    
    (選擇性) 您可以選擇以 csv 或 parquet 格式將資料接收到 Data Lake。 

1. 在 [目標資料類型] 旁，選取 [Azure SQL Database]。 

1. 選取您先前一貫使用的訂用帳戶、資源群組和儲存體帳戶。 

    ![對應至 sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. 在繼續作業前，您必須執行提供的指令碼，以在 SQL Server 中建立新的使用者。 首先，請將提供的指令碼複製到剪貼簿。 

1. 開啟新的 Azure 入口網站索引標籤。請不要關閉現有的索引標籤，因為您稍後必須回過頭來使用。 

1. 在您開啟的新索引標籤中，瀏覽至 [SQL 資料庫]。

1. 選取 SQL 資料庫 (您的訂用帳戶中應該只有一個資料庫)。 請小心不要選取資料倉儲。 

1. 選取 [查詢編輯器 (預覽)]

1. 使用 AAD 驗證登入查詢編輯器。 

1. 執行資料共用中提供的查詢 (已在步驟 14 中複製到剪貼簿)。 

    此命令可讓 Azure Data Share 服務使用 Azure 服務的受控識別向 SQL Server 進行驗證，以便能夠將資料複製到其中。 

1. 返回原始索引標籤，然後選取 [對應到目標]。

1. 接著，選取屬於資料集一部分的 Azure Data Lake Gen2 資料夾，並將其對應至 Azure Blob 儲存體帳戶。 

    ![儲存](media/lab-data-flow-data-share/storage-map.png)

    所有資料集皆已對應後，您現在即可開始接收資料提供者的資料。 

    ![已對應](media/lab-data-flow-data-share/all-mapped.png)
    
1. 選取 [詳細資料]。 

    請注意，[觸發快照集] 已不再呈現灰色，因為資料共用現在已有複製的目的地。

1. 選取 [觸發快照集] -> [完整複本]。 

    ![觸發程序 (trigger)](media/lab-data-flow-data-share/trigger-full.png)

    這會開始將資料複製到您的新資料共用帳戶中。 在實際案例中，這項資料會來於第三方。 

    大約需要 3-5 分鐘的時間，資料才會送達。 您可以按一下 [歷程記錄] 索引標籤以監視進度。 

    在等候期間，請瀏覽至原始資料共用 (DataProvider)，並檢視 [共用訂用帳戶] 和 [歷程記錄] 索引標籤的狀態。請注意，現在已有作用中的訂用帳戶，身為資料提供者，您也可以監視資料取用者何時開始接收其共用的資料。 

1. 瀏覽回資料取用者的資料共用。 在觸發程序的狀態顯示為成功後，瀏覽至目的地 SQL 資料庫和 Data Lake，以確認資料已進入個別的存放區中。 

恭喜，您已完成實驗室！