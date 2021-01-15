---
title: 快速入門：使用對應資料流程來轉換資料
description: 本教學課程提供逐步指示，說明如何使用 Azure Synapse Analytics 來轉換資料與對應資料流程。
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: cb0fd5464f7025d71dd384c56233aefa6a6cd364
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218717"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>快速入門：使用對應資料流程來轉換資料

在本快速入門中，您將使用 Azure Synapse Analytics 建立管線，以使用對應資料流程將 Azure Data Lake Storage Gen2 (ADLS Gen2) 來源的資料轉換成 ADLS Gen2 接收。 使用對應資料流程來轉換資料時，可以擴充此快速入門中的設定模式

在本快速入門中，您將執行下列步驟：

> [!div class="checklist"]
> * 在 Azure Synapse Analytics 中建立具有資料流程活動的管線。
> * 使用四個轉換建立對應的資料流程。
> * 對管線執行測試。
> * 監視資料流程活動

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/)。
* **Azure Synapse 工作區**：使用 Azure 入口網站遵循 [快速入門：建立 Synapse 工作區](quickstart-create-workspace.md)中的指示，建立 Synapse 工作區。
* **Azure 儲存體帳戶**：您可以使用 ADLS 儲存體作為 *來源* 和 *接收* 資料存放區。 如果您沒有儲存體帳戶，請參閱[建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md)，按照步驟建立此帳戶。

    我們在本教學 [課程](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)中轉換的檔案是 MoviesDB.csv，可在此找到。 若要從 GitHub 取出檔案，請將內容複寫到您選擇的文字編輯器，以將本機儲存為 .csv 檔案。 若要將檔案上傳至您的儲存體帳戶，請參閱 [使用 Azure 入口網站上傳 blob](../storage/blobs/storage-quickstart-blobs-portal.md)。 這些範例會參考名為「範例-資料」的容器。

### <a name="navigate-to-the-synapse-studio"></a>瀏覽至 Synapse Studio

建立 Azure Synapse 工作區之後，有兩種方式可以開啟 Synapse Studio：

* 在 [Azure 入口網站](https://ms.portal.azure.com/#home)中開啟 Synapse 工作區。 在 [開始使用] 底下的 [開啟 Synapse Studio] 卡片上，選取 [開啟]。
* 開啟 [Azure Synapse Analytics](https://web.azuresynapse.net/) 並登入您的工作區。

在本快速入門中，我們會使用名為 "adftest2020" 的工作區作為範例。 其會自動讓您瀏覽至 Synapse Studio 首頁。

![Synapse Studio 首頁](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>使用資料流程活動建立管線

管線包含執行一組活動的邏輯流程。 在本節中，您將建立包含資料流程活動的管線。

1. 移至 [整合] 索引標籤。選取管線標頭旁的加號圖示，然後選取 [管線]。

   ![建立新管線](media/doc-common-process/new-pipeline.png)

1. 在管線的 [ **屬性** 設定] 頁面中，輸入 **TransformMovies** 作為 [ **名稱**]。

1. 在 [*活動*] 窗格中的 [*移動和轉換*] 底下，將 [資料流程] 拖曳至管線 **畫布上。**

1. 在 [**加入資料流程**] 頁面快顯視窗中，選取 [**建立新的資料流程**  ->  **資料流程**]。 完成時按一下 [確定]。

   ![建立資料流程](media/quickstart-data-flow/new-data-flow.png)

1. 在 [**屬性**] 頁面上，將您的資料流程命名為 **TransformMovies** 。

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在資料流程畫布中建立轉換邏輯

建立資料流程之後，系統會自動將您傳送到資料流程畫布。 在此步驟中，您將建立會在 ADLS 儲存體中採用 MoviesDB.csv 的資料流程，並將 comedies 的平均評等從1910匯總至2000。 然後，您會將此檔案寫回 ADLS 儲存體。

1. 在 [資料流程] 畫布上方，滑動 [ **資料流程調試** ] 滑杆。 偵錯工具模式允許針對即時 Spark 叢集進行轉換邏輯的互動式測試。 資料流程叢集需要5-7 分鐘的時間來準備，且如果使用者打算進行資料流程開發，建議先開啟 debug。 如需詳細資訊，請參閱[偵錯模式](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)。

    ![將 debug 滑開啟](media/quickstart-data-flow/debug-on.png)

1. 在 [資料流程] 畫布中，按一下 [ **加入來源** ] 方塊來新增來源。

1. 命名您的來源 **MoviesDB**。 按一下 [ **新增** ] 以建立新的源資料集。

    ![建立新的源資料集](media/quickstart-data-flow/new-source-dataset.png)

1. 選擇 [ **Azure Data Lake Storage Gen2**]。 按一下 [繼續]。

    ![選擇 Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. 選擇 [ **DelimitedText**]。 按一下 [繼續]。

1. 將您的資料集命名為 **MoviesDB**。 在 [連結服務] 下拉式清單中，選擇 [ **新增**]。

1. 在 [連結服務建立] 畫面中，為您的 ADLS Gen2 連結服務 **ADLSGen2** 命名，然後指定您的驗證方法。 然後輸入您的連接認證。 在本快速入門中，我們將使用帳戶金鑰來連線到我們的儲存體帳戶。 您可以按一下 [ **測試連接** ]，確認您的認證是否輸入正確。 在完成作業後，按一下 [建立]。

    ![建立來源連結服務](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. 當您回到 [資料集建立] 畫面之後，請在 [檔案 **路徑** ] 欄位下，輸入檔案的所在位置。 在本快速入門中，"MoviesDB.csv" 檔案位於容器「範例-資料」中。 當檔案有標頭時，請核取 **第一個資料列做為標頭**。 **從連線/存放區** 選取，直接從儲存體中的檔案匯入標頭架構。 完成時按一下 [確定]。

    ![來源資料集設定](media/quickstart-data-flow/source-dataset-properties.png)

1. 如果您的 debug 叢集已啟動，請移至來源轉換的 [ **資料預覽** ] 索引標籤， **然後按一下 [** 重新整理] 以取得資料的快照集。 您可以使用 [資料預覽] 來確認是否已正確設定轉換。

    ![資料預覽](media/quickstart-data-flow/data-preview.png)

1. 在 [資料流程] 畫布的來源節點旁，按一下加號圖示以新增轉換。 您要新增的第一個轉換是 **篩選準則**。

    ![新增篩選](media/quickstart-data-flow/add-filter.png)

1. 命名您的篩選轉換 **FilterYears**。 按一下 [ **篩選開啟** ] 旁的 [運算式] 方塊，以開啟 [運算式產生器]。 您將在這裡指定篩選準則。

1. 資料流程運算式產生器可讓您以互動方式建立要在各種轉換中使用的運算式。 運算式可以包含內建函數、輸入架構中的資料行，以及使用者定義的參數。 如需有關如何建立運算式的詳細資訊，請參閱 [資料流程運算式](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)產生器。

    在本快速入門中，您想要篩選在1910年和2000年之間的內容類型喜劇電影。 當 year 目前是字串時，您必須使用函式將它轉換成整數 ```toInteger()``` 。 使用大於或等於 ( # B0 =) 且小於或等於 ( # B1 =) 運算子，以針對常值年份值1910和200進行比較。 將這些運算式與 and ( # A2) 運算子結合在一起。 運算式的形式如下：

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    若要找出哪些電影 comedies，您可以使用函式 ```rlike()``` 來尋找資料行內容中的模式 ' 喜劇 '。 Rlike 運算式的聯集與年度比較以取得：

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![指定篩選準則](media/quickstart-data-flow/visual-expression-builder.png)

    如果您有作用中的偵錯工具，您可以按一下 [重新整理] 來確認您的邏輯，以 **查看與所** 用輸入相較的運算式輸出。 您可以使用資料流程運算式語言來完成這項邏輯，以提供多個正確的答案。

    當您完成運算式之後，請按一下 **[儲存並完成]** 。

1. 提取 **資料預覽** ，以確認篩選器可正常運作。

1. 您將新增的下一個轉換是 **架構修飾** 詞下的 **匯總** 轉換。

    ![新增匯總](media/quickstart-data-flow/add-aggregate.png)

1. 命名您的匯總轉換 **AggregateComedyRatings**。 在 [ **分組方式** ] 索引標籤中，從下拉式清單中選取 [ **年** ]，以將匯總的年份依電影出的年份分組。

    ![匯總設定1](media/quickstart-data-flow/aggregate-settings.png)

1. 移至 [ **匯總** ] 索引標籤。在左邊的文字方塊中，將匯總資料行命名為 **AverageComedyRating**。 按一下右邊的 [運算式] 方塊，透過運算式產生器輸入匯總運算式。

    ![匯總設定2](media/quickstart-data-flow/aggregate-settings-2.png)

1. 若要取得資料行 **分級** 的平均值，請使用 ```avg()``` 彙總函式。 由於 **評** 等是字串並 ```avg()``` 接受數位輸入，因此我們必須透過函數將值轉換成數位 ```toInteger()``` 。 此運算式看起來像這樣：

    ```avg(toInteger(Rating))```

    按一下 **[儲存並完成]** 。

    ![平均喜劇評等](media/quickstart-data-flow/average-comedy-rating.png)

1. 移至 [ **資料預覽** ] 索引標籤，以查看轉換輸出。 請注意，其中只有兩個數據行： **year** 和 **AverageComedyRating**。

    ![匯總資料預覽](media/quickstart-data-flow/transformation-output.png)

1. 接下來，您想要在 **目的地** 下新增 **接收** 轉換。

    ![新增接收](media/quickstart-data-flow/add-sink.png)

1. 命名接收 **接收器**。 按一下 [ **新增** ] 以建立接收資料集。

1. 選擇 [ **Azure Data Lake Storage Gen2**]。 按一下 [繼續]。

1. 選擇 [ **DelimitedText**]。 按一下 [繼續]。

1. 將接收資料集命名為 **MoviesSink**。 針對 [已連結的服務]，選擇您在步驟7中建立的 ADLS Gen2 連結服務。 輸入要寫入資料的輸出檔案夾。 在本快速入門中，我們會寫入容器「範例資料」中的「輸出」資料夾。 資料夾不需要事先存在，而且可以動態建立。 將 **第一個資料列設定為標頭** 為 true，並針對匯 **入架構** 選取 [**無**]。 完成時按一下 [確定]。

    ![接收資料集屬性](media/quickstart-data-flow/sink-dataset-properties.png)

現在您已完成建立資料流程。 您已經準備好在您的管線中執行它。

## <a name="running-and-monitoring-the-data-flow"></a>執行和監視資料流程

您可以在發行管線之前進行調試。 在此步驟中，您將會觸發資料流程管線的 debug 執行。 雖然資料預覽不會寫入資料，但 debug 回合會將資料寫入至您的接收目的地。

1. 移至管線畫布。 按一下 [ **debug** ] 以觸發「偵錯工具執行」。

    ![Debug 管線](media/quickstart-data-flow/debug-pipeline.png)

1. 資料流程活動的管線偵測會使用作用中的 debug 叢集，但仍需要至少一分鐘的時間來初始化。 您可以透過 [ **輸出** ] 索引標籤來追蹤進度。執行成功後，按一下 [眼鏡] 圖示以開啟 [監視中] 窗格。

    ![調試輸出](media/quickstart-data-flow/debugging-output.png)

1. 在 [監視中] 窗格中，您可以看到每個轉換步驟所花費的資料列數和時間。

    ![轉換監視](media/quickstart-data-flow/4-transformations.png)

1. 按一下轉換可取得資料行和資料分割的詳細資訊。

    ![轉換詳細資料](media/quickstart-data-flow/transformation-details.png)

如果您已正確遵循本快速入門，您應該將83個數據列和2個數據行寫入至您的接收資料夾。 您可以藉由檢查 blob 儲存體來確認資料。


## <a name="next-steps"></a>後續步驟

請繼續閱讀下列文章，以瞭解 Azure Synapse Analytics 支援：

> [!div class="nextstepaction"]
> [管線和活動](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
> [對應資料流程總覽](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
> [資料流程運算式語言](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)