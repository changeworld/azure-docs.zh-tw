---
title: 建立自動化 ML 分類模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure Machine Learning 的自動化機器學習 (自動化 ML) 介面來定型及部署分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 03/04/2020
ms.openlocfilehash: 8af412fb2660625ffb413052b06d4429d7844e70
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656506"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>教學課程：在 Azure Machine Learning 中使用自動化 ML 建立分類模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在本教學課程中，您會了解如何透過 Azure Machine Learning 的自動化機器學習介面，建立基本的分類模型，而不需要撰寫任何一行程式碼。 此分類模型會建立分類模型來預測客戶是否會向金融機構申請定期存款。

透過自動化機器學習，您可以將耗費大量時間的工作自動化。 自動化機器學習會快速地逐一嘗試多種演算法和超參數的組合，協助您根據所選擇的成功計量找到最佳模型。

如需時間序列預測範例，請參閱[教學課程：需求預測與 AutoML](tutorial-automated-ml-forecast.md)。

在本教學課程中，您將了解如何執行下列工作：

> [!div class="checklist"]
> * 建立 Azure Machine Learning 工作區。
> * 執行自動化機器學習實驗。
> * 檢視實驗詳細資料。
> * 部署模型。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://aka.ms/AMLFree)。

* 下載 [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 資料檔案。 **y** 資料行指出客戶是否申請定期存款，稍後本教學課程會將其識別為預測的目標資料行。 

## <a name="create-a-workspace"></a>建立工作區

Azure Machine Learning 工作區是雲端中您用來實驗、定型及部署機器學習模型的基礎資源。 工作區可將您的 Azure 訂用帳戶和資源群組與服務中容易使用的物件結合。 

您透過 Azure 入口網站建立工作區 (管理 Azure 資源的 Web 型主控台)。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> 記下您的**工作區**和**訂用帳戶**。 您會需要這些項目，以確保您在正確位置建立實驗。 

## <a name="create-and-run-the-experiment"></a>建立及執行實驗

您會透過 Azure Machine Learning 在 https://ml.azure.com 中完成下列實驗設定及執行步驟；這是統一的 Web 介面，為所有技能等級的資料科學從業人員，提供執行資料科學案例的機器學習工具。 Internet Explorer 瀏覽器不支援此介面。

1. 登入 [Azure Machine Learning](https://ml.azure.com)。

1. 選取訂用帳戶與您建立的工作區。

1. 選取 [馬上開始]。

1. 在左側窗格中，選取 [撰寫] 區段下的 [自動化 ML]。 

   由於這是您的第一個自動化 ML 實驗，因此您會看到一個空白清單與文件連結。

   ![開始使用頁面](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. 選取 [新增自動化 ML 回合]。 

1. 透過從 [+建立資料集] 下拉式清單選取 [從本機檔案]，以建立新的資料集。 

    1. 在 [基本資訊] 表單上，為資料集提供名稱，並提供選擇性描述。 自動化 ML 介面目前僅支援表格式資料集，因此資料集類型應預設為*表格式*。

    1. 選取左下方的 [下一步]。

    1. 在 [資料存放區和檔案選取] 表單上，選取在建立工作區時自動設定的預設資料存放區 [workspaceblobstore (Azure Blob 儲存體)]。 您可以在這裡上傳資料檔案，以供工作區使用。

    1. 選取 [瀏覽]。
    
    1. 選擇您本機電腦上的 **bankmarketing_train.csv** 檔案。 這是您作為[必要條件](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)下載的檔案。

    1. 為您的資料集提供唯一名稱，並提供選擇性的描述。 

    1. 選取左下角 [下一步]，將它上傳至在您的工作區建立期間自動設定的預設容器。  
    
       上傳完成時，系統會根據檔案類型，預先填入 [設定與預覽] 表單。 
       
    1. 確認 **[設定與預覽]** 表單的填入方式如下，然後選取 **[下一步]** 。
        
        欄位|描述| 教學課程的值
        ---|---|---
        檔案格式|定義檔案中所儲存資料的版面配置和類型。| Delimited (分隔檔)
        分隔符號|一或多個字元，用來指定純文字或其他資料流程中個別獨立區域之間的界限。 |Comma (逗號)
        編碼|識別要用來讀取資料集之字元結構描述資料表的位元。| UTF-8
        資料行標題| 指出資料集標題 (如果有的話) 的處理方式。| All files have same headers (所有檔案都有相同的標頭)
        Skip rows (略過資料列) | 指出資料集內略過多少資料列 (如果有的話)。| None

    1. [Schema] \(結構描述\) 表單可讓您進一步設定此實驗的資料。 針對此範例，請選取 [day_of_week] 特徵的切換開關，如此一來，就不會將它包含在此實驗中。 選取 [下一步] 。

        ![預覽索引標籤的設定](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. 在 [確認詳細資料] 表單上，確認資訊符合先前在 [基本資訊] 與 [設定和預覽] 表單上填入的內容。
    1. 選取 [建立] 以完成資料集的建立。
    1. 當您的資料集出現在清單中時，請加以選取。
    1. 檢閱 [資料預覽] 以確保您未包含 **day_of_week**，然後選取 [確定]。

    1. 選取 [下一步]。

1. 如下所示填入 [設定執行] 表單：
    1. 輸入此實驗名稱：`my-1st-automl-experiment`

    1. 選取 [y] 作為您要預測的目標資料行。 此資料行會指出用戶端是否已申請定期存款。
    1. 選取 [建立新的計算]，並設定您的計算目標。 計算目標是用來執行定型指令碼或裝載服務部署的本機或雲端式資源環境。 針對此實驗，我們會使用雲端式計算。 

        欄位 | 描述 | 教學課程的值
        ----|---|---
        計算名稱 |可識別您計算內容的唯一名稱。|automl-compute
        虛擬機器大小&nbsp;&nbsp;| 為您的計算選取虛擬機器大小。|Standard_DS12_V2
        最小/最大節點數 (在 [進階設定] 中)| 若要分析資料，您必須指定一個或多個節點。|最小節點數：1<br>最大節點數：6
  
        1. 選取 [建立] 以取得計算目標。 

            **這需要幾分鐘來完成。** 

        1. 建立完成後，請從下拉式清單選取新的計算目標。

    1. 選取 [下一步] 。

1. 在 [工作類型和設定] 表單上，選取 [分類] 作為機器學習工作類型。

    1. 選取 [檢視其他組態設定] 並填入欄位，如下所示。 這些設定可進一步控制訓練作業。 否則會根據實驗選取範圍和資料來套用預設值。

        >[!NOTE]
        > 在此教學課程中，您不會為每個反覆項目的計量分數或最大核心數設定閾值。 您也不會防止演算法受到測試。
   
        其他設定&nbsp;|描述|教學課程的值&nbsp;&nbsp;
        ------|---------|---
        主要計量| 用於測量機器學習演算法的評估計量。|AUC_weighted
        自動特製化| 啟用前置處理。 這包括用以產生綜合特性的自動化資料清理、準備和轉換。| 啟用
        封鎖的演算法 | 您要從定型作業中排除的演算法| None
        結束準則| 如果符合條件，訓練作業就會停止。 |定型作業時間 (小時)：&nbsp;&nbsp;1 <br> 計量分數閾值：&nbsp;&nbsp;None
        驗證 | 選擇交叉驗證類型與測試次數。|驗證類型：<br>K 折交叉驗證&nbsp;&nbsp; <br> <br> 驗證次數：2
        並行| 每個反覆運算已執行的平行反覆運算數目上限| 並行反覆運算上限：&nbsp;&nbsp;5
        
        選取 [儲存]。

1. 選取 [完成] 以執行實驗。 當實驗準備開始時，[回合詳細資料] 畫面隨即開啟，其頂端顯示 [執行狀態]。

>[!IMPORTANT]
> 準備實驗執行需要 **10-15 分鐘**的時間。
> 執行之後，**每個反覆項目需要 2-3 分鐘以上的時間**。  
> 定期選取 [重新整理] 以查看實驗進行時的執行狀態。
>
> 在生產環境中，您可以先離開一下。 但是在此教學課程中，在其他項目仍在執行時，我們建議您開始探索 [模型] 索引標籤上完成的已測試演算法。 

##  <a name="explore-models"></a>探索模型

瀏覽至 [模型] 索引標籤，以查看已測試的演算法 (模型)。 根據預設，模型會在完成時依計量分數排序。 在本教學課程中，根據所選 **AUC_weighted** 計量評分最高的模型會在清單頂端。

當您等候所有實驗模型完成時，可選取已完成模型的**演算法名稱**來探索其效能詳細資料。 

以下內容在 [模型詳細資料] 與 [視覺效果] 索引標籤中進行瀏覽，以查看所選模型的屬性、計量與效能圖表。 

![執行反覆項目的詳細資料](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>部署最佳模型

自動化機器學習介面可讓您透過幾個步驟，將最佳模型部署為 Web 服務。 部署是模型的整合，因此可以根據新資料進行預測，並找出潛在的商機區域。 

此實驗中對 Web 服務的部署表示金融機構現在有可反覆進行且可調整的 Web 解決方案，能識別潛在的定期存款客戶。 

回合完成後，瀏覽回到 [回合詳細資料] 頁面，然後選取 [模型] 索引標籤。

在此實驗內容中，根據 **AUC_weighted** 計量，**VotingEnsemble** 會被視為最佳模型。  我們會部署此模型，但提醒您部署大約需要 20 分鐘的時間才能完成。 部署程序需要幾個步驟，包括註冊模型、產生資源，以及為 Web 服務設定這些資源。

1. 選取左下角的 [部署最佳模型] 按鈕。

1. 填入 [部署模型] 窗格，如下所示：

    欄位| 值
    ----|----
    部署名稱| my-automl-deploy
    部署描述| 我的第一個自動化機器學習實驗部署
    計算類型 | 選取 Azure 計算執行個體 (ACI)
    啟用驗證| 停用。 
    使用自訂部署| 停用。 允許自動產生預設驅動程式檔案 (計分指令碼) 和環境檔案。 
    
    在此範例中，我們使用 [進階] 功能表中提供的預設值。 

1. 選取 [部署]。  

    [執行] 畫面頂端會出現綠色成功訊息，而在 [建議的模型] 窗格中，狀態訊息會顯示在 [部署狀態] 底下。 定期選取 [重新整理] 以檢查部署狀態。
    
現在您已有可運作的 Web 服務，可用來產生預測。 

若要深入了解如何取用新的 Web 服務及如何使用 Power BI 內建的 Azure Machine Learning 支援來測試您的預測，請繼續進行[**後續步驟**](#next-steps)。

## <a name="clean-up-resources"></a>清除資源

部署檔案比資料和實驗檔案大，因此儲存的成本會較高。 如果您想要保留工作區和實驗檔案，那麼僅刪除部署檔案可將成本降到最低。 或者，如果您不打算使用任何檔案，您可以刪除整個資源群組。  

### <a name="delete-the-deployment-instance"></a>刪除部署執行個體

如果您想保留資源群組與工作區以進行其他教學課程和探索，可以在 https:\//ml.azure.com/ 只刪除 Azure Machine Learning 的部署執行個體。 

1. 移至 [Azure Machine Learning](https://ml.azure.com/)。 瀏覽至您的工作區，並在左側的 [資產] 窗格下，選取 [端點]。 

1. 選取您想要刪除的部署，然後選取 [刪除]。 

1. 選取 [繼續]。

### <a name="delete-the-resource-group"></a>刪除資源群組

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在此自動化機器學習教學課程中，您已使用 Azure Machine Learning 的自動化 ML 介面來建立及部署分類模型。 請參閱下列文章，以了解更多資訊及接下來的步驟：

> [!div class="nextstepaction"]
> [取用 Web 服務](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ 深入了解[自動化機器學習](concept-automated-ml.md)。
+ 如需分類計量與圖表的詳細資訊，請參閱[了解自動化機器學習結果](how-to-understand-automated-ml.md#classification)一文。
+ 深入了解[特徵化](how-to-use-automated-ml-for-ml-models.md#featurization)。
+ 深入了解[資料分析](how-to-use-automated-ml-for-ml-models.md#profile)。


>[!NOTE]
> 此銀行行銷資料集可在 [Creative Commons (CCO：公用網域) 授權](https://creativecommons.org/publicdomain/zero/1.0/)底下取得。 個別資料庫內容中的任何權限都是以[資料庫內容授權](https://creativecommons.org/publicdomain/zero/1.0/)為依據，並可在 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset) 上取得。 此資料集原本位在 [UCI Machine Learning 資料庫](https://archive.ics.uci.edu/ml/datasets/bank+marketing)內。<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez and P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, June 2014.
