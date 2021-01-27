---
title: 教學課程：需求預測與 AutoML
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure Machine Learning Studio 中使用自動化機器學習來定型及部署需求預測模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: 2653161b5828d89858234a9ca98fe432e0eacb5c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879355"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>教學課程：使用自動化機器學習來預測需求


在本教學課程中，您會在 Azure Machine Learning Studio 中使用自動化機器學習 (簡稱自動化 ML) 來建立時間序列預測模型，以預測自行車共享服務的租賃需求。

如需分類模型範例，請參閱[教學課程：在 Azure Machine Learning 中使用自動化 ML 建立分類模型](tutorial-first-experiment-automated-ml.md)。

在本教學課程中，您將了解如何執行下列工作：

> [!div class="checklist"]
> * 建立和載入資料集。
> * 設定和執行自動化 ML 實驗。
> * 指定預測設定。
> * 探索實驗結果。
> * 部署最佳模型。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。 

* 下載 [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) 資料檔案

## <a name="get-started-in-azure-machine-learning-studio"></a>在 Azure Machine Learning Studio 中開始使用

在本教學課程中，您會在 Azure Machine Learning Studio 中建立自動化 ML 實驗執行，Azure Machine Learning Studio 是一種整合 Web 介面，可為所有技能等級的資料科學從業人員，提供用以執行資料科學案例的機器學習工具。 Internet Explorer 瀏覽器不支援 Studio。

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. 選取訂用帳戶與您建立的工作區。

1. 選取 [馬上開始]。 

1. 在左側窗格中，選取 [撰寫] 區段下的 [自動化 ML]。

1. 選取 [+ 新增自動化 ML 執行]。 

## <a name="create-and-load-dataset"></a>建立和載入資料集

在設定實驗之前，請先將資料檔案以 Azure Machine Learning 資料集的形式上傳到工作區。 如此一來，您就可以確保資料會格式化為適合進行實驗。

1. 在 [選取資料集] 表單上，從 [+ 建立資料集] 下拉式清單中選取 [從本機檔案]。 

    1. 在 [基本資訊] 表單上，為資料集提供名稱，並提供選擇性描述。 Azure Machine Learning Studio 中的自動化 ML 目前僅支援表格式資料集，因此資料集類型應預設為 [表格式]。
    
    1. 選取左下方的 [下一步]。

    1. 在 [資料存放區和檔案選取] 表單上，選取在建立工作區時自動設定的預設資料存放區 [workspaceblobstore (Azure Blob 儲存體)]。 這是您將在其中上傳資料檔案的儲存位置。 

    1. 選取 [瀏覽]。 
    
    1. 選擇本機電腦上的 **bike-no.csv** 檔案。 這是您作為[必要條件](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)下載的檔案。

    1. 選取 [**下一步**]

       上傳完成時，系統會根據檔案類型，預先填入 [設定與預覽] 表單。 
       
    1. 確認 **[設定與預覽]** 表單的填入方式如下，然後選取 **[下一步]** 。
        
        欄位|描述| 教學課程的值
        ---|---|---
        檔案格式|定義檔案中所儲存資料的版面配置和類型。| Delimited (分隔檔)
        分隔符號|一或多個字元，用來指定純文字或其他資料流程中個別獨立區域之間的界限。 |Comma (逗號)
        編碼|識別要用來讀取資料集之字元結構描述資料表的位元。| UTF-8
        資料行標題| 指出資料集標題 (如果有的話) 的處理方式。| 使用第一個檔案中的標頭
        Skip rows (略過資料列) | 指出資料集內略過多少資料列 (如果有的話)。| None

    1. [Schema] \(結構描述\) 表單可讓您進一步設定此實驗的資料。 
    
        1. 在此範例中，請選擇忽略 [臨時] 和 [已註冊] 資料行。 這些資料行是 **cnt** 資料行的明細，因此不會納入。

        1. 此外，在此範例中，保留 [屬性] 和 [類型] 的預設值。 
        
        1. 選取 [下一步] 。

    1. 在 [確認詳細資料] 表單上，確認資訊符合先前在 [基本資訊] 與 [設定和預覽] 表單上填入的內容。

    1. 選取 [建立] 以完成資料集的建立。

    1. 當您的資料集出現在清單中時，請加以選取。

    1. 選取 [下一步]。

## <a name="configure-run"></a>設定執行

載入並設定資料之後，請設定遠端計算目標，並選取資料中所要預測的資料行。

1. 如下所示填入 [設定執行] 表單：
    1. 輸入實驗名稱：`automl-bikeshare`

    1. 選取 [cnt] 作為您要預測的目標資料行。 此資料行代表自行車共享租賃總數。

    1. 選取 [建立新的計算]，並設定您的計算目標。 自動化 ML 僅支援 Azure Machine Learning 計算。 

        1. 填入 **虛擬機器** 表單，以設定您的計算。

            欄位 | 描述 | 教學課程的值
            ----|---|---
            虛擬&nbsp;機器&nbsp;優先順序 |選取您的實驗應具備的優先順序| 專用
            虛擬機器類型&nbsp;&nbsp;| 為您的計算選取虛擬機器類型。|CPU (中央處理器)
            虛擬機器大小&nbsp;&nbsp;| 為您的計算選取虛擬機器大小。 系統會根據您的資料和實驗類型提供建議的大小清單。 |Standard_DS12_V2
        
        1. 選取 [下一步]，以填入 [設定設定表單]。
        
             欄位 | 描述 | 教學課程的值
            ----|---|---
            計算名稱 |  可識別您計算內容的唯一名稱。 | bike-compute
            最小/最大節點數| 若要分析資料，您必須指定一個或多個節點。|最小節點數：1<br>最大節點數：6
            縮小之前的閒置秒數 | 叢集自動縮小至最小節點計數之前的閒置時間。|120 (預設值)
            進階設定 | 用於設定和授權虛擬網路以進行實驗的設定。| None 
  
        1. 選取 [建立] 以取得計算目標。 

            **這需要幾分鐘來完成。** 

        1. 建立完成後，請從下拉式清單選取新的計算目標。

    1. 選取 [下一步]  。

## <a name="select-forecast-settings"></a>選取預測設定

藉由指定機器學習工作類型和組態設定，來完成自動化 ML 實驗的設定。

1. 在 [工作類型和設定] 表單上，選取 [時間序列預測] 作為機器學習工作類型。

1. 選取 [日期] 作為 [時間資料行]，並讓 [時間序列識別碼] 保持空白。 

1. [預測範圍] 是您想要預測到多久以後的未來。  取消選取 [自動偵測]，然後在欄位中輸入14。 

1. 選取 [檢視其他組態設定] 並填入欄位，如下所示。 這些設定可讓您更有效地控制訓練作業，並指定預測的設定。 否則會根據實驗選取範圍和資料來套用預設值。

    其他設定&nbsp;|描述|教學課程的值&nbsp;&nbsp;
    ------|---------|---
    主要計量| 用於測量機器學習演算法的評估計量。|標準化均方根誤差
    解釋最佳模型| 自動在自動化 ML 所建立的最佳模型上顯示可解釋性。| 啟用
    封鎖的演算法 | 您要從定型作業中排除的演算法| 極端隨機樹狀結構
    其他預測設定| 這些設定有助於改善模型的正確性。 <br><br> **預測目標延隔：** 您想要將目標變數的延隔往回建構多久 <br> **目標滾動時間範圍**：指定將會產生特徵 (例如「最大值」、「最小值」和「總和」) 的滾動時間範圍大小。 | <br><br>預測&nbsp;目標&nbsp;延隔：None <br> 目標&nbsp;滾動&nbsp;時間範圍&nbsp;大小：None
    結束準則| 如果符合條件，訓練作業就會停止。 |定型作業時間 (小時)：&nbsp;&nbsp;3 <br> 計量分數閾值：&nbsp;&nbsp;None
    驗證 | 選擇交叉驗證類型與測試次數。|驗證類型：<br>K 折交叉驗證&nbsp;&nbsp; <br> <br> 驗證次數：5
    並行| 每個反覆運算已執行的平行反覆運算數目上限| 並行反覆運算上限：&nbsp;&nbsp;6
    
    選取 [儲存]。

## <a name="run-experiment"></a>執行實驗

若要執行實驗，請選取 [完成]。 [執行詳細資料] 畫面隨即開啟，且在頂端的執行編號旁會顯示 [執行狀態]。 此狀態會隨著實驗的進行而更新。 通知也會出現在 Studio 的右上角，以通知您實驗的狀態。

>[!IMPORTANT]
> 準備實驗執行需要 **10-15 分鐘** 的時間。
> 執行之後，**每個反覆項目需要 2-3 分鐘以上的時間**。<br> <br>
> 在生產環境中，此程序需要進行一段時間，因此您可以先離開一會。 在等待時，建議您開始探索 [模型] 索引標籤上已完成測試的演算法。 

##  <a name="explore-models"></a>探索模型

瀏覽至 [模型] 索引標籤，以查看已測試的演算法 (模型)。 根據預設，模型會在完成時依計量分數排序。 在本教學課程中，根據所選計量而獲得最高評分的模型會顯示在清單頂端。

當您等候所有實驗模型完成時，可選取已完成模型的 **演算法名稱** 來探索其效能詳細資料。 

以下範例在 [詳細資料] 與 [計量] 索引標籤中進行瀏覽，以查看所選模型的屬性、計量與效能圖表。 

![執行詳細資料](./media/tutorial-automated-ml-forecast/explore-models.gif)

## <a name="deploy-the-model"></a>部署模型

Azure Machine Learning Studio 中的自動化機器學習可讓您透過幾個步驟，將最佳模型部署為 Web 服務。 部署是模型的整合，因此可以根據新資料進行預測，並找出潛在的商機區域。 

在此實驗中，部署至 Web 服務表示自行車共享公司現在具有可反覆進行且可擴充的 Web 解決方案，而可預測自行車共享租賃需求。 

一旦執行完成後，就請選取畫面頂端的 [執行 1]，瀏覽回到父執行頁面。

在 [最佳模型摘要] 區段中，此實驗內容會根據 [標準化均方根誤差] 計量將 [StackEnsemble] 視為最佳模型。  

我們會部署此模型，但提醒您部署大約需要 20 分鐘的時間才能完成。 部署程序需要幾個步驟，包括註冊模型、產生資源，以及為 Web 服務設定這些資源。

1. 選取 [StackEnsemble] 以開啟模型特定頁面。

1. 選取位於畫面左上方區域的 [部署] 按鈕。

1. 填入 [部署模型] 窗格，如下所示：

    欄位| 值
    ----|----
    部署名稱| bikeshare-deploy
    部署描述| 自行車共享需求部署
    計算類型 | 選取 Azure 計算執行個體 (ACI)
    啟用驗證| 停用。 
    使用自訂部署資產| 停用。 停用可讓系統自動產生預設騎乘者檔案 (評分指令碼) 和環境檔案。 
    
    在此範例中，我們使用 [進階] 功能表中提供的預設值。 

1. 選取 [部署]。  

    [執行] 畫面頂端會出現一個綠色的成功訊息，指出已成功啟動部署。 部署進度可以在 [模型摘要] 窗格的 [部署狀態] 底下找到。
    
一旦部署成功，您就會有可運作的 Web 服務來產生預測。 

若要深入了解如何取用新的 Web 服務及如何使用 Power BI 內建的 Azure Machine Learning 支援來測試您的預測，請繼續進行 [**後續步驟**](#next-steps)。

## <a name="clean-up-resources"></a>清除資源

部署檔案比資料和實驗檔案大，因此儲存的成本會較高。 如果您想要保留工作區和實驗檔案，那麼僅刪除部署檔案可將成本降到最低。 或者，如果您不打算使用任何檔案，您可以刪除整個資源群組。  

### <a name="delete-the-deployment-instance"></a>刪除部署執行個體

如果您想要保留資源群組與工作區以進行其他教學課程和探索，您可以只從 Azure Machine Learning Studio 刪除部署執行個體。 

1. 移至 [Azure Machine Learning Studio](https://ml.azure.com/)。 瀏覽至您的工作區，並在左側的 [資產] 窗格下，選取 [端點]。 

1. 選取您想要刪除的部署，然後選取 [刪除]。 

1. 選取 [繼續]。

### <a name="delete-the-resource-group"></a>刪除資源群組

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 Azure Machine Learning Studio 中使用自動化 ML 來建立及部署可預測自行車共享租賃需求的時間序列預測模型。 

請參閱下面這篇文章中的步驟，來了解如何建立 Power BI 支援的結構描述以便能夠取用新部署的 Web 服務：

> [!div class="nextstepaction"]
> [取用 Web 服務](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ 深入了解[自動化機器學習](concept-automated-ml.md)。
+ 如需分類計量與圖表的詳細資訊，請參閱[了解自動化機器學習結果](how-to-understand-automated-ml.md)一文。
+ 深入了解[特徵化](how-to-configure-auto-features.md#featurization)。
+ 深入了解[資料分析](how-to-connect-data-ui.md#profile)。

>[!NOTE]
> 此自行車共享資料集已因應本教學課程而做了修改。 此資料集當初是從 [Kaggle 競賽](https://www.kaggle.com/c/bike-sharing-demand/data)中取得的，原本則可透過 [Capital Bikeshare](https://www.capitalbikeshare.com/system-data) 來取得。 您也可以在 [UCI Machine Learning 資料庫](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)中找到此資料集。<br><br>
> 來源：Fanaee-T, Hadi, and Gama, Joao, Event labeling combining ensemble detectors and background knowledge, Progress in Artificial Intelligence (2013): pp. 1-15, Springer Berlin Heidelberg.