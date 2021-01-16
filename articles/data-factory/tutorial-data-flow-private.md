---
title: 使用 Azure Data Factory 受控虛擬網路對應資料流程來轉換資料
description: 本教學課程提供逐步指示，說明如何使用 Azure Data Factory 來轉換具有對應資料流程的資料。
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: a5c93244862d72f9c8ea2928c41e699302b1752b
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249428"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>使用對應資料流程安全地轉換資料

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果您不熟悉 Azure Data Factory，請參閱 [Azure Data Factory 簡介](./introduction.md)。

在本教學課程中，您將使用 Data Factory 使用者介面 (UI) 建立管線，以將資料 *從 Azure Data Lake Storage Gen2 來源複製和轉換成 Data Lake Storage Gen2 接收 (兩者都允許* 使用 [) 受控虛擬網路](managed-virtual-network-private-endpoint.md)中的對應資料流程來存取選取的網路 Data Factory。 當您使用對應資料流程來轉換資料時，可以在本教學課程中擴充設定模式。

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
>
> * 建立資料處理站。
> * 使用資料流程活動建立管線。
> * 使用四個轉換建立對應的資料流程。
> * 對管線執行測試。
> * 監視「資料流程」活動。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* **Azure 儲存體帳戶**。 您可以使用 Data Lake Storage 作為 *來源* 和 *接收* 資料存放區。 如果您沒有儲存體帳戶，請參閱[建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)，按照步驟建立此帳戶。 請確定儲存體帳戶只允許從選取的網路存取。 

我們將在本教學課程中轉換的檔案是 moviesDB.csv，可在此 [GitHub 內容網站](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)找到。 若要從 GitHub 取出檔案，請將內容複寫到您選擇的文字編輯器，以將它儲存在本機做為 .csv 檔案。 若要將檔案上傳至您的儲存體帳戶，請參閱 [使用 Azure 入口網站上傳 blob](../storage/blobs/storage-quickstart-blobs-portal.md)。 這些範例會參考名為 **sample** 的容器。

## <a name="create-a-data-factory"></a>建立資料處理站

在此步驟中，您會建立資料處理站，並開啟 Data Factory UI，以在 data factory 中建立管線。

1. 開啟 Microsoft Edge 或 Google Chrome。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 Data Factory UI。
1. 在左側功能表上，選取 [建立資源] > [分析] > [資料處理站]。
1. 在 [新增資料處理站] 頁面的 [名稱] 下，輸入 **ADFTutorialDataFactory**。

   資料處理站的名稱必須是「全域唯一」的名稱。 如果您收到有關名稱值的錯誤訊息，請輸入不同的資料處理站名稱 (例如 yournameADFTutorialDataFactory)。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。

1. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。
1. 針對 [資源群組]，採取下列其中一個步驟︰

    * 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。
    * 選取 [建立新的] ，然後輸入資源群組的名稱。 
         
    若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。 
1. 在 [版本] 下，選取 [V2]。
1. 在 [位置] 下，選取資料處理站的位置。 只有受到支援的位置會出現在下拉式清單中。 資料存放區 (例如 Azure 儲存體和 Azure SQL Database) 和計算 (例如，資料處理站所使用的 Azure HDInsight) 可位於其他區域。

1. 選取 [建立]  。
1. 建立完成後，您會在通知中心看到通知。 選取 [移至資源]，以移至 **Data Factory** 頁面。
1. 選取 [編寫與監視]，以在個別索引標籤中啟動 Data Factory 使用者介面。

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>在 Data Factory 受控虛擬網路中建立 Azure IR

在此步驟中，您會建立 Azure IR，並啟用 Data Factory 受控虛擬網路。

1. 在 Data Factory 入口網站中，移至 [ **管理**]，然後選取 [ **新增** ] 以建立新的 Azure IR。

   ![顯示建立新 Azure IR 的螢幕擷取畫面。](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. 在 [ **整合執行時間設定** ] 頁面上，根據所需的功能選擇要建立的整合執行時間。 在本教學課程中，選取 [ **Azure]、[自我** 裝載]，然後按一下 [ **繼續**]。 
1. 選取 [ **azure** ]，然後按一下 [ **繼續** ] 建立 azure 整合執行時間。

   ![顯示新 Azure IR 的螢幕擷取畫面。](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. 在 [虛擬網路設定 (預覽)] 中，選取 [啟用]。

   ![顯示啟用新 Azure IR 的螢幕擷取畫面。](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. 選取 [建立]。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>使用資料流程活動建立管線

在此步驟中，您將建立包含「資料流程」活動的管線。

1. 在 [現在就開始吧] 頁面中，選取 [建立管線]。

   ![顯示建立管線的螢幕擷取畫面。](./media/doc-common-process/get-started-page.png)

1. 在管線的 [屬性] 窗格中，針對 [管線名稱] 輸入 **TransformMovies** 。
1. 在 factory 的頂端列中，滑出 [ **資料流程] 調試** 滑杆。 偵錯工具模式允許針對即時 Spark 叢集進行轉換邏輯的互動式測試。 資料流程叢集需要五到七分鐘的時間來準備。 如果您打算進行資料流程開發， **請先開啟 [資料流程]** 。 如需詳細資訊，請參閱 [偵錯工具模式](./concepts-data-flow-debug-mode.md)。

    ![顯示 [資料流程] debug 滑杆的螢幕擷取畫面。](media/tutorial-data-flow-private/dataflow-debug.png)
1. 在 [ **活動** ] 窗格中，展開 [ **移動和轉換**]。 將 [ **資料流程** ] 活動從窗格拖曳到管線畫布。

1. 在 [ **加入** 資料流程] 快顯視窗中，選取 [ **建立新** 的資料流程]，然後選取 [ **對應資料流程**]。 當您完成時，請選取 **[確定]** 。

    ![顯示對應資料流程的螢幕擷取畫面。](media/tutorial-data-flow-private/mapping-dataflow.png)

1. 在 [屬性] 窗格中，將您的資料流程命名為 **TransformMovies** 。

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在資料流程畫布中建立轉換邏輯

建立資料流程之後，系統會自動將您傳送到資料流程畫布。 在此步驟中，您將建立會在 Data Lake Storage 中使用 moviesDB.csv 檔案的資料流程，並將 comedies 從1910的平均評等匯總至2000。 然後，您會將此檔案寫回 Data Lake Storage。

### <a name="add-the-source-transformation"></a>新增來源轉換

在此步驟中，您會將 Data Lake Storage Gen2 設定為來源。

1. 在 [資料流程] 畫布中，選取 [ **新增來源** ] 方塊來新增來源。

1. 命名您的來源 **MoviesDB**。 選取 [ **新增** ] 以建立新的源資料集。

1. 選取 **Azure Data Lake Storage Gen2**，然後選取 [ **繼續**]。

1. 選取 [ **DelimitedText**]，然後選取 [ **繼續**]。

1. 將您的資料集命名為 **MoviesDB**。 在 [連結服務] 下拉式清單中，選取 [ **新增**]。

1. 在 [連結服務建立] 畫面中，為您的 Data Lake Storage Gen2 連結服務 **ADLSGen2** 命名，然後指定您的驗證方法。 然後輸入您的連接認證。 在本教學課程中，我們將使用 **帳戶金鑰** 來連線到儲存體帳戶。 

1. 請務必啟用 **互動式製作**。 可能需要一分鐘的時間來啟用。

    ![顯示互動式製作的螢幕擷取畫面。](./media/tutorial-data-flow-private/interactive-authoring.png)

1. 選取 [測試連線]。 它應該會失敗，因為儲存體帳戶不會啟用存取權，而不需要建立和核准私人端點。 在錯誤訊息中，您應該會看到一個連結，可讓您建立私人端點，以供您建立受控私人端點。 替代方式是直接移至 [ **管理** ] 索引標籤，並 [遵循本節中](#create-a-managed-private-endpoint) 的指示來建立受控私人端點。

1. 讓對話方塊保持開啟，然後移至儲存體帳戶。

1. 遵循[本節](#approval-of-a-private-link-in-a-storage-account)的指示來核准私人連結。

1. 返回對話方塊。 選取 [測試連線]，然後選取 [建立] 以部署已連結的服務。

1. 在 [資料集建立] 畫面的 [檔案 **路徑** ] 欄位下，輸入檔案的所在位置。 在本教學課程中，檔案 moviesDB.csv 位於容器 **範例-資料** 中。 因為檔案具有標頭，所以請選取 [ **第一個資料列做為標頭** ] 核取方塊。 **從連線/存放區** 選取，直接從儲存體中的檔案匯入標頭架構。 當您完成時，請選取 **[確定]** 。

    ![顯示來源路徑的螢幕擷取畫面。](media/tutorial-data-flow-private/source-file-path.png)

1. 如果您的 debug 叢集已啟動，請移至來源轉換的 [ **資料預覽** ] 索引標籤， **然後選取 [** 重新整理] 以取得資料的快照集。 您可以使用資料預覽來確認是否已正確設定您的轉換。

    ![顯示 [資料預覽] 索引標籤的螢幕擷取畫面。](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>建立受控私人端點

如果您在測試上述連接時未使用超連結，請遵循路徑。 現在您需要建立受控私人端點，以連線至所建立的已連結服務。

1. 移至 **管理** 索引標籤。

   > [!NOTE]
   > 並非所有 Data Factory 執行個體圴可使用 **管理** 索引標籤。 如果您沒有看到該索引標籤，可以選取 [作者] > [連線] > [私人端點] 來存取私人端點。

1. 移至 **受控私人端點** 區段。
1. 在 **受控私人端點** 之下選取 [+新增]。

    ![顯示 [受控私人端點新增] 按鈕的螢幕擷取畫面。](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. 從清單中選取 **Azure Data Lake Storage Gen2** 圖格，然後選取 [ **繼續**]。
1. 輸入建立之儲存體帳戶的名稱。
1. 選取 [建立]。
1. 在等候幾秒鐘之後，您應該會看到建立的私人連結需要核准。
1. 選取之前建立的私人端點。 您會看到超連結，引導您在儲存體帳戶層級核准私人端點。

    ![顯示 [管理私人端點] 窗格的螢幕擷取畫面。](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>在儲存體帳戶中核准私人連結

1. 在儲存體帳戶中，移至 **設定** 區段下的 **私人端點連線**。

1. 選取您所建立私人端點的核取方塊，然後選取 [ **核准**]。

    ![顯示 [私人端點核准] 按鈕的螢幕擷取畫面。](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. 新增描述，然後選取 [是]。
1. 回到 Data Factory 中 **管理** 索引標籤的 **受控私人端點** 區段。
1. 大約一分鐘之後，您應該會看到私人端點的核准。

### <a name="add-the-filter-transformation"></a>新增篩選準則轉換

1. 在 [資料流程] 畫布的來源節點旁，選取加號圖示以新增轉換。 您將新增的第一個轉換是 **篩選準則**。

    ![顯示新增篩選準則的螢幕擷取畫面。](media/tutorial-data-flow-private/add-filter.png)
1. 命名您的篩選轉換 **FilterYears**。 選取 [ **篩選準則** ] 旁的 [運算式] 方塊，以開啟 [運算式產生器]。 您將在這裡指定篩選準則。

    ![顯示 FilterYears 的螢幕擷取畫面。](media/tutorial-data-flow-private/filter-years.png)
1. 資料流程運算式產生器可讓您以互動方式建立要在各種轉換中使用的運算式。 運算式可以包含內建函數、輸入架構中的資料行，以及使用者定義的參數。 如需有關如何建立運算式的詳細資訊，請參閱 [資料流程運算式](./concepts-data-flow-expression-builder.md)產生器。

    * 在本教學課程中，您會想要篩選喜劇類型中的電影，以在1910年和2000年之間推出。 因為年份目前為字串，所以您需要使用函數將它轉換成整數 ```toInteger()``` 。 使用大於或等於 ( # B0 =) 且小於或等於 ( # B1 =) 運算子來與常值年份值1910和2000進行比較。 將這些運算式與 and ( # A2) 運算子結合在一起。 運算式的形式如下：

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * 若要找出哪些電影 comedies，您可以使用函式 ```rlike()``` 來尋找資料行內容中的「喜劇」模式。 Rlike 運算式的聯集與年度比較以取得：

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * 如果您有使用中的偵錯工具，您可以選取 [重新整理] 來確認您的 **邏輯，以** 查看與所用輸入相較的運算式輸出。 您可以使用資料流程運算式語言來完成這項邏輯，以提供多個正確的答案。

        ![顯示篩選運算式的螢幕擷取畫面。](media/tutorial-data-flow-private/filter-expression.png)

    * 當您完成運算式之後，請選取 **[儲存並完成]** 。

1. 提取 **資料預覽** ，以確認篩選器可正常運作。

    ![顯示已篩選資料預覽的螢幕擷取畫面。](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>新增匯總轉換

1. 您將新增的下一個轉換是 **架構修飾** 詞下的 **匯總** 轉換。

    ![顯示新增匯總的螢幕擷取畫面。](media/tutorial-data-flow-private/add-aggregate.png)
1. 命名您的匯總轉換 **AggregateComedyRating**。 在 [ **分組方式** ] 索引標籤上，從下拉式方塊中選取 [ **年** ]，以依電影的年份將匯總分組。

    ![顯示匯總群組的螢幕擷取畫面。](media/tutorial-data-flow-private/group-by-year.png)
1. 移至 [ **匯總** ] 索引標籤。在左邊的文字方塊中，將匯總資料行命名為 **AverageComedyRating**。 選取 [右運算式] 方塊，即可透過運算式產生器來輸入匯總運算式。

    ![顯示匯總資料行名稱的螢幕擷取畫面。](media/tutorial-data-flow-private/name-column.png)
1. 若要取得資料行 **分級** 的平均值，請使用 ```avg()``` 彙總函式。 由於 **評** 等是字串並 ```avg()``` 接受數位輸入，因此我們必須透過函數將值轉換成數位 ```toInteger()``` 。 此運算式看起來像這樣：

    ```avg(toInteger(Rating))```

1. 選取 **[儲存並** 在完成之後完成]。

    ![顯示儲存匯總的螢幕擷取畫面。](media/tutorial-data-flow-private/save-aggregate.png)
1. 移至 [ **資料預覽** ] 索引標籤，以查看轉換輸出。 請注意，其中只有兩個數據行： **year** 和 **AverageComedyRating**。

### <a name="add-the-sink-transformation"></a>新增接收轉換

1. 接下來，您想要在 **目的地** 下新增 **接收** 轉換。

    ![顯示新增接收的螢幕擷取畫面。](media/tutorial-data-flow-private/add-sink.png)
1. 命名接收 **接收器**。 選取 [ **新增** ] 以建立接收資料集。

    ![顯示建立接收器的螢幕擷取畫面。](media/tutorial-data-flow-private/create-sink.png)
1. 在 [ **新增資料集** ] 頁面上，選取 **Azure Data Lake Storage Gen2** 然後選取 [ **繼續**]。

1. 在 [ **選取格式** ] 頁面上，選取 [ **DelimitedText** ]，然後選取 [ **繼續**]。

1. 將接收資料集命名為 **MoviesSink**。 針對 [已連結的服務]，選擇您為來源轉換所建立的相同 **ADLSGen2** 連結服務。 輸入要寫入資料的輸出檔案夾。 在本教學課程中，我們會寫入容器 **範例資料** 中的資料夾 **輸出**。 資料夾不需要事先存在，而且可以動態建立。 選取 [ **第一個資料列做為標頭** ] 核取方塊，然後選取 [ **無** 匯 **入架構**]。 選取 [確定]。

    ![顯示接收路徑的螢幕擷取畫面。](media/tutorial-data-flow-private/sink-file-path.png)

現在您已完成建立資料流程。 您已經準備好在您的管線中執行它。

## <a name="run-and-monitor-the-data-flow"></a>執行和監視資料流程

您可以在發行管線之前進行調試。 在這個步驟中，您會觸發資料流程管線的 debug 執行。 雖然資料預覽不會寫入資料，但 debug 回合會將資料寫入至您的接收目的地。

1. 移至管線畫布。 選取 [ **debug** ] 以觸發偵錯工具執行。

1. 資料流程活動的管線偵錯工具會使用作用中的 debug 叢集，但仍需要至少一分鐘的時間來初始化。 您可以透過 [ **輸出** ] 索引標籤來追蹤進度。執行成功後，請選取 [執行詳細資料] 的眼鏡圖示。

1. 在 [詳細資料] 頁面上，您可以看到資料列數和每個轉換步驟所花的時間。

    ![顯示監視執行的螢幕擷取畫面。](media/tutorial-data-flow-private/run-details.png)
1. 選取轉換來取得資料行和資料分割的詳細資訊。

如果您已正確遵循本教學課程，您應該將83個數據列和2個數據行寫入至您的接收資料夾。 您可以藉由檢查 blob 儲存體來確認資料是否正確。

## <a name="summary"></a>[摘要]

在本教學課程中，您已使用 Data Factory UI 來建立管線，以將資料從 Data Lake Storage Gen2 來源複製和轉換成 Data Lake Storage Gen2 接收 (兩者都只允許使用 [) 受控虛擬網路](managed-virtual-network-private-endpoint.md)中的對應資料流程來存取選取的網路 Data Factory。