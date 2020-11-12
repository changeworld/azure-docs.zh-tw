---
title: 使用 AutoML 建立模型 & 部署
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning studio 建立、檢查和部署自動化機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: aniththa
ms.reviewer: nibaccam
ms.date: 07/10/2020
ms.topic: conceptual
ms.custom: how-to, automl
ms.openlocfilehash: aa45bc9f70bf05074391dd14cc5fc774eb77c762
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536246"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>使用 Azure Machine Learning 建立、檢閱和部署自動化機器學習模型


在本文中，您將瞭解如何在 Azure Machine Learning studio 中不需要一行程式碼，即可建立、探索及部署自動化機器學習模型。

自動化機器學習是針對特定資料來選取最佳機器學習服務演算法的流程。 此流程可讓您快速產生機器學習模型。 [深入了解自動化機器學習](concept-automated-ml.md)。
 
如需端對端範例，請嘗試[使用 Azure Machine Learning 自動化 ML 介面建立分類模型的教學課程](tutorial-first-experiment-automated-ml.md)。 

如需以 Python 程式碼為基礎的體驗，請使用 Azure Machine Learning SDK [設定自動化機器學習實驗](how-to-configure-auto-train.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* Azure Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。 

## <a name="get-started"></a>開始使用

1. 在 https://ml.azure.com 登入 Azure Machine Learning。 

1. 選取訂用帳戶及工作區。 

1. 巡覽至左側窗格。 選取位於 [撰寫] 區段下的 [自動化 ML]。

[![Azure Machine Learning Studio 導覽窗格](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 若這是第一次執行任何實驗，則將會看到空白清單，以及前往文件的連結。 

否則，將會看到最近自動化機器學習實驗的清單，其中包括使用 SDK 建立的項目。 

## <a name="create-and-run-experiment"></a>建立及執行實驗

1. 選取 [+ 新增自動化 ML 執行] 並填入表單。

1. 從儲存體容器選取資料集，或建立新的資料集。 資料集可從本機檔案、Web URL、資料存放區或 Azure 開放資料集建立。 深入瞭解 [資料集建立](how-to-create-register-datasets.md)。  

    >[!Important]
    > 訓練資料的需求：
    >* 資料必須是表格式形式。
    >* 您想要預測的值 (目標資料行) 必須存在於資料中。

    1. 若要從本機電腦上的檔案建立新的資料集，請選取 [ **+ 建立資料集** ]，然後選取 [ **從本機** 檔案]。 

    1. 在 [ **基本資訊** ] 表單中，為您的資料集提供一個唯一的名稱，並提供選擇性的描述。 

    1. 選取 [下一步] 來開啟 [資料存放區和檔案選取表單]。 在此表單上，您將會選取上傳資料集的位置：與工作區一同自動建立的預設儲存體容器，或選取想要用於實驗的儲存體容器。 
    
        1. 如果您的資料位於虛擬網路後方，您必須啟用 [ **略過驗證** ] 功能，以確保工作區可以存取您的資料。 如需詳細資訊，請參閱 [在 Azure 虛擬網路中使用 Azure Machine Learning studio](how-to-enable-studio-virtual-network.md)。 
    
    1. 選取 **[流覽]** ，為您的資料集上傳資料檔案。 

    1. 檢閱 [設定和預覽] 表單以進行確認。 表單會根據檔案類型以智慧方式填入。 

        欄位| 描述
        ----|----
        檔案格式| 定義檔案中所儲存資料的版面配置和類型。
        分隔符號| 一或多個字元，其用來指定純文字或其他資料流中個別獨立區域之間的界限。
        編碼| 識別要用來讀取資料集之字元結構描述資料表的位元。
        資料行標題| 指出資料集標題 (如果有的話) 的處理方式。
        Skip rows (略過資料列) | 指出資料集內略過多少資料列 (如果有的話)。
    
        選取 [下一步] 。

    1. [結構描述] 表單會根據在 [設定與預覽] 表單中選取的項目以智慧方式填入。 請在此處設定每個資料行的資料類型、檢閱資料行名稱，以及選取針對實驗 **不要包含** 哪些資料行。 
            
        選取 [下一步]。

    1. [確認詳細資料] 表單上會顯示先前在 [基本資訊] 和 [設定與預覽] 表單中填入的資訊摘要。 您也可以選擇使用啟用分析的計算，為資料集建立資料設定檔。 深入了解[資料分析](how-to-connect-data-ui.md#profile)。

        選取 [下一步] 。
1. 在新建立的資料集出現後選取該資料集。 您也可以檢視資料集的預覽和範例統計資料。 

1. 在 [設定執行] 表單上，輸入唯一的實驗名稱。

1. 選取目標資料行；這是將要進行預測的資料行。

1. 為資料分析和訓練作業選取計算。 現有的計算清單會出現在下拉式清單中。 若要建立新的計算，請遵循步驟 7 中的指示。

1. 選取 [建立新的計算] 來針對此實驗設定計算內容。

    欄位|描述
    ---|---
    計算名稱| 輸入可識別您計算內容的唯一名稱。
    虛擬機器優先順序| 低優先順序的虛擬機器較便宜，但不保證計算節點。 
    虛擬機器類型| 選取虛擬機器類型的 CPU 或 GPU。
    虛擬機器大小| 為您的計算選取虛擬機器大小。
    最小/最大節點數| 若要分析資料，您必須指定一個或多個節點。 輸入所計算的節點數上限。 AML Compute 的預設為 6 個節點。
    進階設定 | 這些設定可讓您為您的實驗設定使用者帳戶和現有的虛擬網路。 
    
    選取 [建立]。 建立新的計算可能會需要數分鐘。

    >[!NOTE]
    > 計算名稱會指出所選/建立的計算是否已「啟用分析」。 (如需詳細資料，請參閱[資料分析](how-to-connect-data-ui.md#profile))。

    選取 [下一步] 。

1. 在 [工作類型和設定] 表單上，選取工作類型：分類、迴歸，或預測。 如需詳細資訊，請參閱支援的工作 [類型](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) 。

    1. 針對 **分類** ，您也可以啟用深度學習。
    
        如果啟用了深度學習，則僅限 _train_validation 分割_ 的驗證。 [深入瞭解驗證選項](how-to-configure-cross-validation-data-splits.md)。


    1. 針對 **預測** ，您可以 
    
        1. 啟用深度學習。
    
        1. 選取 *時間資料行* ：此資料行包含要使用的時間資料。

        1. 選取 *預測範圍* ：指出模型能夠預測到未來的時間單位數 (分鐘/小時/天/周/月/年) 。 模型需要針對未來預測的時間越長，其正確性越低。 [深入了解預測及預測範圍](how-to-auto-train-forecast.md)。

1. (選擇性) 檢視其他組態設定：可用來更進一步控制訓練作業的其他設定。 否則會根據實驗選取範圍和資料來套用預設值。 

    其他組態|描述
    ------|------
    主要計量| 用來評分模型的主要計量。 [深入了解模型計量](how-to-configure-auto-train.md#primary-metric)。
    解釋最佳模型 | 選取以啟用或停用，以顯示建議最佳模型的說明。 <br> 這項功能目前不適用於 [特定的預測演算法](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model)。 
    封鎖的演算法| 選取要從訓練作業中排除的演算法。 <br><br> 允許演算法僅適用于 [SDK 實驗](how-to-configure-auto-train.md#supported-models)。 <br> 請參閱 [每種工作類型支援的模型](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels?preserve-view=true&view=azure-ml-py)。
    結束準則| 當符合其中任何一項準則時，訓練作業即會停止。 <br> 訓練作業時間 (小時)：允許訓練作業執行的時間長度。 <br> 計量分數閾值：所有管線的最低計量分數。 這可確保若擁有想要達到的已定義目標計量，則不會在訓練作業上花費超過必要程度的時間。
    驗證| 選取要在訓練作業中使用的交叉驗證選項。 <br> [深入了解交叉驗證](how-to-configure-cross-validation-data-splits.md#prerequisites)。<br> <br>預測只支援 k 折交叉驗證。
    並行| *並行反覆項目上限* ：要在訓練作業中測試的管線 (反覆項目) 數量上限。 作業不會執行超過指定數量的反覆項目。

1.  (選擇性的) View 特徵化設定：如果您選擇在 [ **其他設定** ] 表單中啟用 **自動特徵化** ，則會套用預設的特徵化技術。 在 **View 特徵化設定** 中，您可以變更這些預設值，並據以進行自訂。 瞭解如何 [自訂 featurizations](#customize-featurization)。 

    ![螢幕擷取畫面顯示 [選取工作類型] 對話方塊，其中已呼叫 [View 特徵化 settings]。](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

## <a name="customize-featurization"></a>自訂特徵化

在 **特徵化** 表單中，您可以啟用/停用自動特徵化，以及為您的實驗自訂自動特徵化設定。 若要開啟此表單，請參閱 [建立和執行實驗](#create-and-run-experiment) 一節中的步驟10。 

下表摘要說明目前可透過 studio 取得的自訂。 

資料行| 自訂
---|---
已包括 | 指定要包含哪些資料行以供定型。
功能類型| 變更所選資料行的數值型別。
插補| 選取要在您的資料中插補遺漏值的值。

![Azure Machine Learning studio 自訂特徵化](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>執行實驗並檢視結果

選取 [完成] 以執行實驗。 實驗準備流程最多需要 10 分鐘。 訓練作業可能需要額外 2-3 分鐘不等，才能讓每個管線完成執行。

> [!NOTE]
> 自動化 ML 採用的演算法具有固有的隨機性，可能會導致建議模型的最終計量分數稍微變化，例如精確度。 自動化 ML 也會在必要時對資料（例如，訓練-測試分割、定型驗證分割或交叉驗證）執行作業。 因此，如果您多次執行具有相同設定和主要計量的實驗，您可能會在每個實驗中看到由於這些因素的最終計量分數變化。 

### <a name="view-experiment-details"></a>檢視實驗詳細資料

[執行詳細資料] 畫面會在 [詳細資料] 索引標籤中開啟。此畫面會顯示實驗執行的摘要，並會在頂端執行編號的旁邊包含狀態列。 

[模型] 索引標籤包含依計量分數所建立的模型清單。 依預設，根據所選計量評分最高的模型會出現在清單頂端。 如果訓練作業嘗試多個模型，系統會將所有結果新增到清單中。 使用此方式快速比較到目前為止所產生的各個模型計量。

[![執行詳細資料儀表板](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>檢視訓練執行的詳細資料

向下切入任何已完成的模型，以查看定型執行詳細資料，例如 [ **模型** ] 索引標籤上的模型摘要或 [ **計量** ] 索引標籤上的 [效能度量圖表]。 [深入瞭解圖表](how-to-understand-automated-ml.md)。

[![反覆項目詳細資料](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>部署模型

當手邊具備最佳模型時，即可將其作為 Web 服務部署以預測新的資料。

自動化 ML 可協助部署模型，而無須撰寫程式碼：

1. 您有數個部署選項。 

    + 選項1：根據您定義的度量準則，部署最佳模型。 
        1. 實驗完成之後，請選取畫面頂端的 [ **執行 1** ]，以流覽至父執行頁面。 
        1.  選取 [ **最佳模型摘要** ] 區段中所列的模型。 
        1. 選取視窗左上角的 [ **部署** ]。 

    + 選項2：從此實驗部署特定模型反復專案。
        1. 從 [模型] 索引標籤中選取所需的模型
        1. 選取視窗左上角的 [ **部署** ]。

1. 填入 [部署模型] 窗格。

    欄位| 值
    ----|----
    名稱| 輸入部署的唯一名稱。
    描述| 輸入描述以更清楚地識別此部署的用途。
    計算類型| 選取想要部署的端點類型： *Azure Kubernetes Service (AKS)* 或 *Azure 容器執行個體 (ACI)* 。
    計算名稱| 僅適用於 AKS：選取想要部署的目標 AKS 叢集名稱。
    啟用驗證 | 選取允許以權杖為基礎或以金鑰為基礎的驗證。
    使用自訂部署資產| 若想要上傳自己的評分指令碼和環境檔案，請啟用此功能。 [深入了解評分指令碼](how-to-deploy-and-where.md)。

    >[!Important]
    > 檔案名稱必須少於 32 個字元，且必須以英數字元開始及結束。 其中可包含虛線、底線、點和英數字元。 不允許空格。

    「進階」功能表提供預設部署功能，例如[資料收集](how-to-enable-app-insights.md)和資源使用率設定。 若想要覆寫這些預設，請在此功能表中進行。

1. 選取 [部署]。 部署需要約 20 分鐘才能完成。
    開始部署後，會出現 [模型摘要] 索引標籤。 請參閱 **部署狀態** 一節底下的部署進度。 

現在您已擁有可運作的 Web 服務，可用來產生預測！ 您可從 [Power BI 內建的 Azure Machine Learning 支援](how-to-consume-web-service.md#consume-the-service-from-power-bi)以透過查詢服務來測試預測。

## <a name="next-steps"></a>後續步驟

* [了解如何取用 Web 服務](./how-to-consume-web-service.md)。
* [了解自動化機器學習結果](how-to-understand-automated-ml.md)。
* [深入了解自動化機器學習](concept-automated-ml.md)和 Azure Machine Learning。
