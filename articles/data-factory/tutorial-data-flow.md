---
title: 使用對應資料流程轉換資料
description: 本教學課程提供逐步指示，說明如何使用 Azure Data Factory 來轉換資料與對應資料流程
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: 917a8d6edf04d8a160c3a6a5ac59949623dfee5c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81418672"
---
# <a name="transform-data-using-mapping-data-flows"></a>使用對應資料流程轉換資料

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果您不熟悉 Azure Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)。

在本教學課程中，您將使用 Azure Data Factory 使用者介面（UX）來建立管線，以使用對應資料流程，將資料從 Azure Data Lake Storage （ADLS） Gen2 來源複製及轉換為 ADLS Gen2 的接收。 此教學課程中的設定模式可以在使用對應資料流程轉換資料時展開

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立具有資料流程活動的管線。
> * 建立具有四個轉換的對應資料流程。
> * 對管線執行測試。
> * 監視資料流程活動

## <a name="prerequisites"></a>必要條件
* **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* **Azure 儲存體帳戶**。 您可以使用 ADLS 儲存體作為*來源*和*接收*資料存放區。 如果您沒有儲存體帳戶，請參閱[建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md)，按照步驟建立此帳戶。

我們在本教學課程中轉換的檔案是 MoviesDB.csv，可以在[這裡](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)找到。 若要從 GitHub 抓取檔案，請將內容複寫到您選擇的文字編輯器，以便在本機儲存為 .csv 檔案。 若要將檔案上傳到您的儲存體帳戶，請參閱[使用 Azure 入口網站上傳 blob](../storage/blobs/storage-quickstart-blobs-portal.md)。 這些範例會參考名為「範例-資料」的容器。

## <a name="create-a-data-factory"></a>建立 Data Factory

在此步驟中，您會建立資料處理站，並開啟 Data Factory UX，以在 data factory 中建立管線。

1. 開啟 **Microsoft Edge** 或 **Google Chrome**。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 Data Factory UI。
2. 在左側功能表上，選取 [建立資源] > [分析] > [資料處理站]：

   ![在 [新增] 窗格中選取資料處理站](./media/doc-common-process/new-azure-data-factory-menu.png)

3. 在 [新增資料處理站] 頁面的 [名稱] 下，輸入 **ADFTutorialDataFactory**。

   Azure Data Factory 的名稱必須是 *全域唯一的*。 如果您收到有關名稱值的錯誤訊息，請輸入不同的資料處理站名稱。 (例如，使用 yournameADFTutorialDataFactory)。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。

     ![新增 Data Factory](./media/doc-common-process/name-not-available-error.png)
4. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。
5. 針對 [資源群組]，採取下列其中一個步驟︰

    a. 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。

    b. 選取 [建立新的] ，然後輸入資源群組的名稱。 
         
    若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。 
6. 在 [版本] 下，選取 [V2]。
7. 在 [位置] 下，選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區（例如，Azure 儲存體和 SQL Database）和計算（例如 Azure HDInsight）可位於其他區域。
8. 選取 [建立]。
9. 建立完成後，您會在 [通知中心] 看到通知。 選取 [移至資源]，以瀏覽至 Data Factory 頁面。
10. 選取 [編寫與監視]，以在個別索引標籤中啟動 Data Factory 使用者介面。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>建立具有資料流程活動的管線

在此步驟中，您將建立包含「資料流程」活動的管線。

1. 在 [現在就開始吧] 頁面中，選取 [建立管線]。

   ![建立管線](./media/doc-common-process/get-started-page.png)

1. 在管線的 [**一般**] 索引標籤中，為管線的 [**名稱**] 輸入**TransformMovies** 。
1. 在 factory 頂端列中，滑動 [**資料流程 debug** ] 滑杆。 「偵錯工具模式」可讓您對即時 Spark 叢集進行轉換邏輯的互動式測試。 資料流程叢集需要5-7 分鐘的時間來準備，而如果使用者打算進行資料流程開發，建議您先開啟 debug。 如需詳細資訊，請參閱[偵錯模式](concepts-data-flow-debug-mode.md)。

    ![資料流程活動](media/tutorial-data-flow/dataflow1.png)
1. 在 [**活動**] 窗格中，展開 [**移動和轉換**可折疊]。 將 [**資料流程**] 活動從窗格拖放到管線畫布。

    ![資料流程活動](media/tutorial-data-flow/activity1.png)
1. 在 [**加入資料流程**] 快顯視窗中，選取 [**建立新**的資料流程]，然後為您的資料流程**TransformMovies**命名。 完成時按一下 [完成]。

    ![資料流程活動](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在資料流程畫布中建立轉換邏輯

建立資料流程之後，系統會自動將您傳送到資料流程畫布。 在此步驟中，您將建立一個會在 ADLS 儲存體中取得 moviesDB.csv 的資料流程，並將 comedies 的平均評等從1910匯總至2000。 接著，您會將此檔案寫回 ADLS 儲存體。

1. 在 [資料流程] 畫布中，按一下 [**加入來源**] 方塊來新增來源。

    ![資料流程畫布](media/tutorial-data-flow/dataflow2.png)
1. 將您的來源命名為**MoviesDB**。 按一下 [**新增**] 以建立新的源資料集。

    ![資料流程畫布](media/tutorial-data-flow/dataflow3.png)
1. 選擇 [ **Azure Data Lake Storage Gen2**]。 按一下 [繼續]。

    ![資料集](media/tutorial-data-flow/dataset1.png)
1. 選擇 [ **DelimitedText**]。 按一下 [繼續]。

    ![資料集](media/tutorial-data-flow/dataset2.png)
1. 將您的資料集命名為**MoviesDB**。 在 [連結服務] 下拉式清單中，選擇 [**新增**]。

    ![資料集](media/tutorial-data-flow/dataset3.png)
1. 在 [已連結的服務建立] 畫面中，為您的 ADLS gen2 連結服務**ADLSGen2**命名，並指定您的驗證方法。 然後輸入您的連接認證。 在本教學課程中，我們會使用帳戶金鑰來連接到我們的儲存體帳戶。 您可以按一下 [**測試連接**] 來確認您的認證已正確輸入。 在完成作業後，按一下 [建立]。

    ![連結服務](media/tutorial-data-flow/ls1.png)
1. 回到 [資料集建立] 畫面之後，請在 [檔案**路徑**] 欄位底下輸入檔案所在的位置。 在本教學課程中，moviesDB.csv 的檔案位於 [容器範例-資料] 中。 當檔案具有標頭時，請核取**第一個資料列做為標頭**。 選取 [**從連接/存放區**]，直接從儲存體中的檔案匯入標頭架構。 完成時按一下 [確定]。

    ![資料集](media/tutorial-data-flow/dataset4.png)
1. 如果您的 debug 叢集已啟動，請移至來源轉換的 [**資料預覽**] 索引標籤，**然後按一下 [** 重新整理] 以取得資料的快照集。 您可以使用 [資料預覽] 來確認您的轉換已正確設定。

    ![資料流程畫布](media/tutorial-data-flow/dataflow4.png)
1. 在 [資料流程] 畫布上的來源節點旁，按一下加號圖示以加入新的轉換。 您要新增的第一個轉換是**篩選準則**。

    ![資料流程畫布](media/tutorial-data-flow/dataflow5.png)
1. 將篩選準則轉換命名為**FilterYears**。 按一下 [**篩選準則**] 旁的 [運算式] 方塊，以開啟 [運算式產生器]。 在這裡，您將指定篩選準則。

    ![篩選](media/tutorial-data-flow/filter1.png)
1. 資料流程運算式產生器可讓您以互動方式建立要用於各種轉換的運算式。 運算式可以包含內建函數、輸入架構中的資料行，以及使用者定義的參數。 如需如何建立運算式的詳細資訊，請參閱[資料流程運算式](concepts-data-flow-expression-builder.md)產生器。

    在本教學課程中，您會想要篩選在1910和2000年之間的內容類型喜劇電影。 當 year 目前是字串時，您必須使用函數將它轉換成整數 ```toInteger()``` 。 使用 [大於或等於] （>=）和 [小於或等於] （<=）運算子來比較常值的年份值1910和 200-。 將這些運算式與 and （&&）運算子結合在一起。 運算式的形式如下：

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    若要找出哪些電影已 comedies，您可以使用函式 ```rlike()``` 來尋找資料行內容中的 ' 喜劇 ' 模式。 Rlike 運算式的聯集與要取得的年份比較：

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    如果您有作用中的 debug 叢集，您可以按一下 [重新整理] 來驗證**邏輯，以**查看與使用的輸入相比的運算式輸出。 您可以使用資料流程運算式語言來完成這項邏輯，有一個以上的正確解答。

    ![篩選](media/tutorial-data-flow/filter2.png)

    完成運算式之後，請按一下 **[儲存並完成]** 。

1. 提取**資料預覽**，以確認篩選器運作正常。

    ![篩選](media/tutorial-data-flow/filter3.png)
1. 下一個要新增的轉換是**架構修飾**詞底下的**匯總**轉換。

    ![Aggregate](media/tutorial-data-flow/agg1.png)
1. 命名您的匯總轉換**AggregateComedyRatings**。 在 [**分組方式**] 索引標籤中，從下拉式清單中選取 [**年**]，以依據電影的年份將匯總分組。

    ![Aggregate](media/tutorial-data-flow/agg2.png)
1. 移至 [**匯總**] 索引標籤。在左側文字方塊中，將匯總資料行命名為**AverageComedyRating**。 按一下 [右運算式] 方塊，透過運算式產生器輸入匯總運算式。

    ![Aggregate](media/tutorial-data-flow/agg3.png)
1. 若要取得資料行**分級**的平均值，請使用 ```avg()``` 彙總函式。 當**評**等是字串並 ```avg()``` 接受數值輸入時，我們必須透過函式將值轉換成數位 ```toInteger()``` 。 這個運算式看起來像這樣：

    ```avg(toInteger(Rating))```

    完成時 **，按一下 [儲存並完成]** 。

    ![Aggregate](media/tutorial-data-flow/agg4.png)
1. 移至 [**資料預覽**] 索引標籤，以查看轉換輸出。 請注意，只有兩個數據行是**year**和**AverageComedyRating**。

    ![Aggregate](media/tutorial-data-flow/agg3.png)
1. 接下來，您想要在 [**目的地**] 底下新增 [**接收**] 轉換。

    ![接收](media/tutorial-data-flow/sink1.png)
1. 為接收**接收**端命名。 按一下 [**新增**] 以建立接收資料集。

    ![接收](media/tutorial-data-flow/sink2.png)
1. 選擇 [ **Azure Data Lake Storage Gen2**]。 按一下 [繼續]。

    ![資料集](media/tutorial-data-flow/dataset1.png)
1. 選擇 [ **DelimitedText**]。 按一下 [繼續]。

    ![資料集](media/tutorial-data-flow/dataset2.png)
1. 將您的接收資料集命名為**MoviesSink**。 針對 [已連結的服務]，選擇您在步驟6中建立的 ADLS gen2 連結服務。 輸入要寫入資料的輸出檔案夾。 在本教學課程中，我們會寫入容器「範例-資料」中的資料夾「輸出」。 資料夾不需要事先存在，而且可以動態建立。 將**第一個資料列**設定為 true，並針對 [匯**入架構**] 選取 [**無**]。 按一下 [完成]。

    ![接收](media/tutorial-data-flow/sink3.png)

現在您已經完成資料流程的建立作業。 您已經準備好在管線中執行它。

## <a name="running-and-monitoring-the-data-flow"></a>執行和監視資料流程

您可以在發行管線之前進行調試。 在此步驟中，您將會觸發資料流程管線的「檢查」執行。 雖然資料預覽不會寫入資料，但 debug 執行會將資料寫入至您的接收目的地。

1. 移至管線畫布。 按一下 [ **debug** ] 以觸發「執行偵錯工具」。

    ![管線](media/tutorial-data-flow/pipeline1.png)
1. 資料流程活動的管線 debug 會使用作用中的 debug 叢集，但至少需要一分鐘的時間來初始化。 您可以透過 [**輸出**] 索引標籤來追蹤進度。執行成功之後，請按一下 [眼鏡] 圖示以開啟 [監視中] 窗格。

    ![管線](media/tutorial-data-flow/pipeline2.png)
1. 在 [監視中] 窗格中，您可以看到每個轉換步驟所花費的資料列數和時間。

    ![監視](media/tutorial-data-flow/pipeline3.png)
1. 按一下轉換以取得資料行和資料分割的詳細資訊。

    ![監視](media/tutorial-data-flow/pipeline4.png)

如果您已正確遵循本教學課程，您應該已在接收資料夾中寫入83個數據列和2個數據行。 您可以藉由檢查 blob 儲存體來確認資料是否正確。

## <a name="next-steps"></a>後續步驟

本教學課程中的管線會執行將 comedies 的平均評等匯總從1910到2000的資料流程，並將資料寫入至 ADLS。 您已了解如何︰

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立具有資料流程活動的管線。
> * 建立具有四個轉換的對應資料流程。
> * 對管線執行測試。
> * 監視資料流程活動

深入瞭解[資料流程運算式語言](data-flow-expression-functions.md)。
