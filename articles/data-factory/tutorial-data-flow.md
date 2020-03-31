---
title: 使用映射資料流程轉換資料
description: 本教程提供了使用 Azure 資料工廠使用映射資料流程轉換資料的分步說明
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: e6ca8007a96cc63b51b4f79b69029cbf0799e71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979186"
---
# <a name="transform-data-using-mapping-data-flows"></a>使用映射資料流程轉換資料

如果您不熟悉 Azure Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)。

在本教程中，您將使用 Azure 資料工廠使用者介面 （UX） 創建管道，使用映射資料流程將資料從 Azure 資料存儲 （ADLS） Gen2 源複製並轉換為 ADLS Gen2 接收器。 使用映射資料流程轉換資料時，可以擴展本教程中的配置模式

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 創建具有資料流程活動的管道。
> * 使用四個轉換構建映射資料流程。
> * 對管線執行測試。
> * 監視資料流程活動

## <a name="prerequisites"></a>Prerequisites
* **Azure 訂閱**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* **Azure 存儲帳戶**。 使用 ADLS 存儲作為*源*和*接收器*資料存儲。 如果您沒有儲存體帳戶，請參閱[建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md)，按照步驟建立此帳戶。

我們在本教程中轉換的檔是 MoviesDB.csv，可以[在這裡](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)找到。 若要從 GitHub 檢索檔，請將內容複寫到您選擇的文字編輯器中，以本地保存為 .csv 檔。 要將檔上載到存儲帳戶，請參閱[使用 Azure 門戶上載 Blob。](../storage/blobs/storage-quickstart-blobs-portal.md) 這些示例將引用名為"示例資料"的容器。

## <a name="create-a-data-factory"></a>建立 Data Factory

在此步驟中，您將創建一個資料工廠並打開資料工廠 UX 以在資料工廠中創建管道。

1. 開啟 **Microsoft Edge** 或 **Google Chrome**。 目前，資料工廠 UI 僅在 Microsoft 邊緣瀏覽器和 Google Chrome 網路瀏覽器中支援。
2. 在左側功能表上，選擇 **"創建資源** > **分析** > **資料工廠**"：

   ![在 [新增] 窗格中選取資料處理站](./media/doc-common-process/new-azure-data-factory-menu.png)

3. 在 [新增資料處理站]**** 頁面的 [名稱]**** 下，輸入 **ADFTutorialDataFactory**。

   Azure 資料工廠的名稱必須*全域唯一*。 如果您收到有關名稱值的錯誤訊息，請輸入不同的資料處理站名稱。 (例如，使用 yournameADFTutorialDataFactory)。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory 命名規則](naming-rules.md)。

     ![新增 Data Factory](./media/doc-common-process/name-not-available-error.png)
4. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。
5. 針對 [資源群組]****，採取下列其中一個步驟︰

    a. 選取 [使用現有的] ****，然後從下拉式清單選取現有的資源群組。

    b. 選取 [建立新的] ****，然後輸入資源群組的名稱。 
         
    若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../azure-resource-manager/management/overview.md)。 
6. 在 [版本]**** 下，選取 [V2]****。
7. 在 [位置]**** 下，選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料工廠使用的資料存儲（例如 Azure 存儲和 SQL 資料庫）和計算（例如 Azure HDInsight）可以位於其他區域。
8. 選取 [建立]****。
9. 建立完成後，您會在 [通知中心] 看到通知。 選取 [移至資源]****，以瀏覽至 Data Factory 頁面。
10. 選取 [編寫與監視]****，以在個別索引標籤中啟動 Data Factory 使用者介面。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>使用資料流程活動創建管道

在此步驟中，您將創建包含資料流程活動的管道。

1. 在 [現在就開始吧]**** 頁面中，選取 [建立管線]****。

   ![建立管線](./media/doc-common-process/get-started-page.png)

1. 在管道的 **"常規**"選項卡中，輸入管道**名稱**的 **"轉換電影**"。
1. 在出廠頂部欄中，打開 **"資料流程"調試**滑塊。 偵錯模式允許針對即時 Spark 群集對轉換邏輯進行互動式測試。 資料流程群集需要 5-7 分鐘才能預熱，如果使用者計畫執行資料流程開發，建議他們首先打開調試。 有關詳細資訊，請參閱[偵錯模式](concepts-data-flow-debug-mode.md)。

    ![資料流程活動](media/tutorial-data-flow/dataflow1.png)
1. 在 **"活動"** 窗格中，展開 **"移動和轉換**手風琴"。 將 **"資料流程"** 活動從窗格拖放到管道畫布上。

    ![資料流程活動](media/tutorial-data-flow/activity1.png)
1. 在 **"添加資料流程**"快顯視窗中，選擇 **"創建新的資料流程"，** 然後命名資料流程**轉換影片**。 完成時按一下 [完成]。

    ![資料流程活動](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在資料流程畫布中構建轉換邏輯

創建資料流程後，將自動發送到資料流程畫布。 在此步驟中，您將構建一個資料流程，該流將 moviesDB.csv 用於 ADLS 存儲，並聚合 1910 年至 2000 年喜劇的平均評級。 然後，您將此檔寫回 ADLS 存儲。

1. 在資料流程畫布中，通過按一下"**添加源**"框添加源。

    ![資料流程畫布](media/tutorial-data-flow/dataflow2.png)
1. 命名源**MoviesDB**。 按一下 **"新建"** 以創建新的源資料集。

    ![資料流程畫布](media/tutorial-data-flow/dataflow3.png)
1. 選擇**Azure 資料存儲第 2 代**。 按一下 [繼續]。

    ![資料集](media/tutorial-data-flow/dataset1.png)
1. 選擇**分隔文本**。 按一下 [繼續]。

    ![資料集](media/tutorial-data-flow/dataset2.png)
1. 命名資料集 **"電影資料庫**"。 在連結服務下拉清單中，選擇 **"新建**"。

    ![資料集](media/tutorial-data-flow/dataset3.png)
1. 在連結的服務創建螢幕中，命名您的 ADLS gen2 連結服務**ADLSGen2**並指定身份驗證方法。 然後輸入連接憑據。 在本教程中，我們使用帳戶金鑰連接到我們的存儲帳戶。 您可以按一下 **"測試連接**"以驗證您的憑據輸入正確。 在完成作業後，按一下 [建立]。

    ![連結服務](media/tutorial-data-flow/ls1.png)
1. 返回資料集創建螢幕後，輸入檔位於 **"檔路徑**"欄位下的位置。 在本教程中，檔電影DB.csv位於容器示例資料中。 由於檔具有標頭，因此選中**第一行作為標頭**。 選擇 **"從連接/存儲**"直接從存儲中的檔導入標頭架構。 完成時按一下 [確定]。

    ![資料集](media/tutorial-data-flow/dataset4.png)
1. 如果調試群集已啟動，請轉到源轉換的 **"資料預覽"** 選項卡，然後按一下 **"刷新"** 以獲取資料的快照。 您可以使用資料預覽來驗證轉換配置正確。

    ![資料流程畫布](media/tutorial-data-flow/dataflow4.png)
1. 在資料流程畫布上的源節點旁邊，按一下加號圖示以添加新轉換。 要添加的第一個轉換是**篩選器**。

    ![資料流程畫布](media/tutorial-data-flow/dataflow5.png)
1. 命名篩選器轉換**篩選器年份**。 按一下 **"篩選"** 旁邊的運算式框以打開運算式產生器。 在這裡，您將指定篩選準則。

    ![Filter](media/tutorial-data-flow/filter1.png)
1. 資料流程運算式產生器允許您以對話模式生成用於各種轉換的運算式。 運算式可以包括內建函數、輸入架構中的列和使用者定義的參數。 有關如何生成運算式的詳細資訊，請參閱[資料流程運算式產生器](concepts-data-flow-expression-builder.md)。

    在本教程中，您希望過濾 1910 年至 2000 年間的流派喜劇電影。 由於年份當前是字串，因此您需要使用 函數```toInteger()```將其轉換為整數。 使用大於或等於 （>+） 和小於或等於 （<=） 運算子來比較與字面年份值 1910 和 200-進行比較。 將這些運算式與 和 （&&） 運算子結合在一起。 運算式以：

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    要查找哪些電影是喜劇，可以使用 該```rlike()```函數在列流派中查找圖案"喜劇"。 將 rlike 運算式與年份比較結合，以獲得：

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    如果調試群集處於活動狀態，則可以通過按一下 **"刷新"** 來驗證邏輯，以查看與使用的輸入相比的運算式輸出。 關於如何使用資料流程運算式語言完成此邏輯，有一個以上正確的答案。

    ![Filter](media/tutorial-data-flow/filter2.png)

    完成運算式後，按一下"**保存"和"完成**"。

1. 獲取**資料預覽**以驗證篩選器是否正常工作。

    ![Filter](media/tutorial-data-flow/filter3.png)
1. 要添加的下一個轉換是在**架構修改器**下的**聚合**變換。

    ![Aggregate](media/tutorial-data-flow/agg1.png)
1. 命名聚合轉換**聚合喜劇評級**。 在 **"按組"** 選項卡中，從下拉清單中選擇**年份**，按影片出名的年份對聚合進行分組。

    ![Aggregate](media/tutorial-data-flow/agg2.png)
1. 轉到 **"聚合"** 選項卡。在左側文字方塊中，命名聚合列 **"平均喜劇"。** 按一下右運算式框通過運算式產生器輸入聚合運算式。

    ![Aggregate](media/tutorial-data-flow/agg3.png)
1. 要獲取列**評級**的平均值，請使用```avg()```彙總函式。 由於**評級**是一個字串```avg()```，並且採用數位輸入，因此必須通過```toInteger()```函數將值轉換為數字。 這是運算式看起來像：

    ```avg(toInteger(Rating))```

    完成後按一下 **"保存並完成**"。

    ![Aggregate](media/tutorial-data-flow/agg4.png)
1. 轉到"**資料預覽"** 選項卡以查看轉換輸出。 注意只有兩列存在，**年份**和**平均喜劇。**

    ![Aggregate](media/tutorial-data-flow/agg3.png)
1. 接下來，您希望在 **"目標**"下添加**Sink**轉換。

    ![接收](media/tutorial-data-flow/sink1.png)
1. 命名你的**水槽**。 按一下 **"新建**"以創建接收器資料集。

    ![接收](media/tutorial-data-flow/sink2.png)
1. 選擇**Azure 資料存儲第 2 代**。 按一下 [繼續]。

    ![資料集](media/tutorial-data-flow/dataset1.png)
1. 選擇**分隔文本**。 按一下 [繼續]。

    ![資料集](media/tutorial-data-flow/dataset2.png)
1. 命名您的接收器資料集**電影沉淪**。 對於連結服務，請選擇您在步驟 6 中創建的 ADLS gen2 連結服務。 輸入要將資料寫入的輸出檔案夾。 在本教程中，我們將編寫到容器"示例資料"中的資料夾"輸出"。 該資料夾不需要事先存在，可以動態創建。 將**第一行設置為標題**為 true，並為**導入架構**選擇 **"無**"。 按一下 [完成]。

    ![接收](media/tutorial-data-flow/sink3.png)

現在，您已完成構建資料流程。 您已準備好在管道中運行它。

## <a name="running-and-monitoring-the-data-flow"></a>運行和監視資料流程

您可以在發佈管道之前調試管道。 在此步驟中，您將觸發資料流程管道的調試運行。 雖然資料預覽不會寫入資料，但調試運行會將資料寫入接收器目標。

1. 轉到管道畫布。 按一下 **"調試"** 以觸發調試運行。

    ![管線](media/tutorial-data-flow/pipeline1.png)
1. 資料流程活動的管道調試使用活動調試群集，但至少需要一分鐘才能初始化。 您可以通過 **"輸出"** 選項卡跟蹤進度。運行成功後，按一下眼鏡圖示以打開監視窗格。

    ![管線](media/tutorial-data-flow/pipeline2.png)
1. 在監視窗格中，您可以看到每個轉換步驟中花費的行數和時間。

    ![監視](media/tutorial-data-flow/pipeline3.png)
1. 按一下轉換以獲取有關資料的列和分區的詳細資訊。

    ![監視](media/tutorial-data-flow/pipeline4.png)

如果正確遵循本教程，則應將 83 行和 2 列寫入接收器資料夾。 您可以通過檢查 Blob 存儲來驗證資料是否正確。

## <a name="next-steps"></a>後續步驟

本教程中的管道運行一個資料流程，該資料流程匯總了 1910 到 2000 年喜劇的平均評級，並將資料寫入 ADLS。 您已了解如何︰

> [!div class="checklist"]
> * 建立資料處理站。
> * 創建具有資料流程活動的管道。
> * 使用四個轉換構建映射資料流程。
> * 對管線執行測試。
> * 監視資料流程活動

瞭解有關[資料流程運算式語言](data-flow-expression-functions.md)的更多詳細資訊。
