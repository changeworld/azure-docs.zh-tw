---
title: 使用 autoML 建立模型及部署
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 建立、檢閱和部署自動化機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 841d518c02dbc76a172890f6019d78d048f4e8bb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653842"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>使用 Azure Machine Learning 建立、檢閱和部署自動化機器學習模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在本文中，您將會了解如何在 Azure Machine Learning 的工作室介面中建立、探索和部署自動化機器學習模型，而無須撰寫任何一行程式碼。 自動化機器學習是針對特定資料來選取最佳機器學習服務演算法的流程。 此流程可讓您快速產生機器學習模型。 [深入了解自動化機器學習](concept-automated-ml.md)。
 
如需端對端範例，請嘗試[使用 Azure Machine Learning 自動化 ML 介面建立分類模型的教學課程](tutorial-first-experiment-automated-ml.md)。 

如需以 Python 程式碼為基礎的體驗，請使用 Azure Machine Learning SDK [設定自動化機器學習實驗](how-to-configure-auto-train.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* **Enterprise 版**的 Azure Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。  若要將現有的工作區升級到 Enterprise 版，請參閱[升級至 Enterprise 版](how-to-manage-workspace.md#upgrade)。

## <a name="get-started"></a>開始使用

1. 在 https://ml.azure.com 登入 Azure Machine Learning。 

1. 選取訂用帳戶及工作區。 

1. 巡覽至左側窗格。 選取位於 [撰寫] 區段下的 [自動化 ML]。

[![Azure Machine Learning Studio 導覽窗格](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 若這是第一次執行任何實驗，則將會看到空白清單，以及前往文件的連結。 

否則，將會看到最近自動化機器學習實驗的清單，其中包括使用 SDK 建立的項目。 

## <a name="create-and-run-experiment"></a>建立及執行實驗

1. 選取 [+ 新增自動化 ML 執行] 並填入表單。

1. 從儲存體容器選取資料集，或建立新的資料集。 資料集可從本機檔案、Web URL、資料存放區或 Azure 開放資料集建立。 

    >[!Important]
    > 訓練資料的需求：
    >* 資料必須是表格式形式。
    >* 您想要預測的值 (目標資料行) 必須存在於資料中。

    1. 若要從本機電腦上的檔案建立新資料集，請選取 [瀏覽]，然後選取檔案。 

    1. 為您的資料集提供唯一名稱，並提供選擇性的描述。 

    1. 選取 [下一步] 來開啟 [資料存放區和檔案選取表單]。 在此表單上，您將會選取上傳資料集的位置：與工作區一同自動建立的預設儲存體容器，或選取想要用於實驗的儲存體容器。 

    1. 檢閱 [設定和預覽] 表單以進行確認。 表單會根據檔案類型以智慧方式填入。 

        欄位| 描述
        ----|----
        檔案格式| 定義檔案中所儲存資料的版面配置和類型。
        分隔符號| 一或多個字元，其用來指定純文字或其他資料流中個別獨立區域之間的界限。
        編碼| 識別要用來讀取資料集之字元結構描述資料表的位元。
        資料行標題| 指出資料集標題 (如果有的話) 的處理方式。
        Skip rows (略過資料列) | 指出資料集內略過多少資料列 (如果有的話)。
    
        選取 [下一步] 。

    1. [結構描述] 表單會根據在 [設定與預覽] 表單中選取的項目以智慧方式填入。 請在此處設定每個資料行的資料類型、檢閱資料行名稱，以及選取針對實驗**不要包含**哪些資料行。 
            
        選取 [下一步]。

    1. [確認詳細資料] 表單上會顯示先前在 [基本資訊] 和 [設定與預覽] 表單中填入的資訊摘要。 您也可以選擇使用啟用分析的計算，為資料集建立資料設定檔。 深入了解[資料分析](#profile)。

        選取 [下一步] 。
1. 在新建立的資料集出現後選取該資料集。 您也可以檢視資料集的預覽和範例統計資料。 

1. 在 [設定執行] 表單上，輸入唯一的實驗名稱。

1. 選取目標資料行；這是將要進行預測的資料行。

1. 為資料分析和訓練作業選取計算。 現有的計算清單會出現在下拉式清單中。 若要建立新的計算，請遵循步驟 7 中的指示。

1. 選取 [建立新的計算] 來針對此實驗設定計算內容。

    欄位|描述
    ---|---
    計算名稱| 輸入可識別您計算內容的唯一名稱。
    虛擬機器大小| 為您的計算選取虛擬機器大小。
    最小/最大節點數 (在 [進階設定] 中)| 若要分析資料，您必須指定一個或多個節點。 輸入所計算的節點數上限。 AML Compute 的預設為 6 個節點。
    
    選取 [建立]。 建立新的計算可能會需要數分鐘。

    >[!NOTE]
    > 計算名稱會指出所選/建立的計算是否已「啟用分析」。 (如需詳細資料，請參閱[資料分析](#profile))。

    選取 [下一步] 。

1. 在 [工作類型和設定] 表單上，選取工作類型：分類、迴歸，或預測。 如需詳細資訊，請參閱[如何定義工作類型](how-to-define-task-type.md)。

    1. 針對分類，您也可以啟用用於文字特徵化的深度學習。

    1. 針對預測：
        1. 選取時間資料行：這個資料行包含要使用的時間資料。

        1. 選取預測範圍：指出模型能夠預測未來多少時間單位 (分鐘/小時/天/週/月/年)。 模型需要針對未來預測的時間越長，其正確性越低。 [深入了解預測及預測範圍](how-to-auto-train-forecast.md)。

1. (選擇性) 檢視其他組態設定：可用來更進一步控制訓練作業的其他設定。 否則會根據實驗選取範圍和資料來套用預設值。 

    其他組態|描述
    ------|------
    主要計量| 用來評分模型的主要計量。 [深入了解模型計量](how-to-configure-auto-train.md#explore-model-metrics)。
    自動特製化| 選取以啟用或停用自動化機器學習執行的前置處理。 前置處理包括自動資料清理、準備以及轉換，以產生綜合功能。 不支援時間序列預測工作類型。 [深入了解前置處理](#featurization)。 
    解釋最佳模型 | 選取以啟用或停用顯示建議最佳模型的說明能力
    封鎖的演算法| 選取要從訓練作業中排除的演算法。
    結束準則| 當符合其中任何一項準則時，訓練作業即會停止。 <br> 訓練作業時間 (小時)：允許訓練作業執行的時間長度。 <br> 計量分數閾值：所有管線的最低計量分數。 這可確保若擁有想要達到的已定義目標計量，則不會在訓練作業上花費超過必要程度的時間。
    驗證| 選取要在訓練作業中使用的交叉驗證選項。 [深入了解交叉驗證](how-to-configure-auto-train.md)。
    並行| *並行反覆項目上限*：要在訓練作業中測試的管線 (反覆項目) 數量上限。 作業不會執行超過指定數量的反覆項目。

1. (選擇性) 檢視特徵化設定：若選擇在 [其他組態設定] 中啟用 [自動特徵化]，則可在此表單中指定要在其上執行特徵化的資料行，以及選取要針對遺漏值插補使用的統計值。

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>資料分析與摘要統計資料

您可在資料集中取得各式各樣的摘要統計資料來驗證資料集是否已針對 ML 準備就緒。 針對非數值資料行，其僅會包含如最小值、最大值和錯誤計數等基本統計資料。 針對數值資料行，您也可以檢閱其統計時間和估計分位數。 具體而言，資料設定檔包含：

>[!NOTE]
> 針對無關類型特徵顯示的空白項目。

統計資料|描述
------|------
功能| 要進行摘要的資料行名稱。
設定檔| 以推斷類型為基礎的內嵌視覺效果。 例如，字串、布林值和日期會具備值的計數，小數 (數值) 則會包含近似長條圖。 這可供快速了解資料的分佈。
類型分佈| 資料行中類型的內嵌值計數。 Null 為其自身的類型，因此此視覺效果在偵測奇數或遺漏值時相當實用。
類型|資料行的推斷類型。 可能的值包括：字串、布林值、日期和小數。
最小值| 資料行的最小值。 如果特徵的類型沒有固有排序 (例如布林值)，則會顯示空白項目。
最大值| 資料行的最小值。 
Count| 資料行中遺漏和未遺漏項目的總數。
未遺漏計數| 資料行中未遺漏的項目數。 空字串和錯誤都會視為值，因此不會計入「未遺漏計數」中。
分位數| 每個分位數的近似值可供了解資料其分佈。
平均值| 資料行的算術平均數或平均。
標準差| 此資料行資料的離散或變異量量值。
Variance| 此資料行資料從其平均值分散程度的量值。 
偏度| 此資料行資料與常態分佈相異程度的量值。
峰度| 相較於常態分佈，此資料行資料集中於尾端程度的量值。

<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>進階特徵化選項

自動化機器學習會自動提供前置處理和資料護欄，以協助識別和管理資料的潛在問題，例如[過度學習和不平衡的資料](concept-manage-ml-pitfalls.md#prevent-over-fitting)。 

### <a name="preprocessing"></a>前置處理

> [!NOTE]
> 如果計劃將自動 ML 建立的模型匯出到 [ONNX 模型](concept-onnx.md)，則 ONNX 格式僅支援具備 * 標記的特徵化選項。 深入了解[將模型轉換為 ONNX](concept-automated-ml.md#use-with-onnx)。 

|前置處理步驟&nbsp;| 描述 |
| ------------- | ------------- |
|卸除高基數或無變異數特徵* |從訓練和驗證集卸除所有高基數或無變異數的特徵，包括所有遺漏值、在所有資料列上皆相同的值，或具備極高基數的值 (例如雜湊、識別碼或 GUID)。|
|插補遺漏值* |針對數值特徵，使用資料行中的值平均進行插補。<br/><br/>針對類別特徵，使用最頻繁的值進行插補。|
|產生其他特徵* |針對 DateTime 特徵：年、月、日、星期幾、幾月幾日、季、第幾週、小時、分鐘、秒。<br/><br/>針對文字特徵：以單字母組、雙字母組和三字母組為基礎的字詞頻率。|
|轉換和編碼*|具有較少唯一值的數值特徵會轉換成類別特徵。<br/><br/>針對低基數的類別執行 one-hot 編碼；針對高基數的特徵執行 one-hot-hash 編碼。|
|字組內嵌|文字 Featurizer 會使用預先定型的模型，將文字權杖的向量轉換成句子向量。 文件中每個文字的內嵌向量都會彙總在一起，以產生文件特徵向量。|
|目標編碼|針對類別特徵，針對迴歸問題將每個類別與平均目標值進行對應，以及針對分類問題將每個類別對應到每個類別的類別機率。 其會套用以頻率為基礎的加權和 K 折交叉驗證，以減少對應的過度學習和疏鬆資料類別所造成雜訊。|
|文字目標編碼|針對文字輸入，會使用具備文字袋 (bag-of-words) 的堆疊線性模型來產生每個類別其機率。|
|證據權數 (WoE)|將 WoE 作為類別資料行相互關聯與目標資料行相互關聯的量值計算。 其計算方式是類別內機率與類別外機率比例的對數。 此步驟會為每個類別輸出一個數值特徵資料行，而不需要明確插補遺漏值和極端值處理。|
|叢集距離|在所有數值資料行上定型 K-Means 叢集模型。  輸出 k 個新特徵，每個叢集一個新的數值特徵，其中包含每個樣本與每個叢集距心的距離。|

### <a name="data-guardrails"></a>資料護欄

啟用自動特徵化或將驗證設為自動時，即會套用資料護欄。資料護欄有助於識別資料的潛在問題 (例如遺漏值或類別不平衡)，並協助採取矯正動作來改善結果。 

使用者可在自動化 ML 執行 [資料護欄] 索引標籤內的工作室中檢閱資料護欄，或在使用 Python SDK 提交實驗時設定 ```show_output=True```。 

#### <a name="data-guardrail-states"></a>資料護欄狀態

資料護欄會顯示三種狀態的其中一種：**通過**、**完成**，或**警示**。

State| 描述
----|----
已通過| 未偵測到任何資料問題，因此不需要任何使用者動作。 
完成| 已成功將變更套用到資料。 我們鼓勵使用者檢閱自動化 ML 採取的矯正動作，以確保變更與預期的結果一致。 
警示| 偵測到無法救濟的資料問題。 我們鼓勵使用者修訂並修正問題。 

>[!NOTE]
> 先前版本的自動化 ML 實驗會顯示第四個狀態：**已修正**。 較新的實驗將不會顯示此狀態，且顯示**已修正**狀態的所有資料護欄現在會顯示**完成**。   

下表描述目前支援的資料護欄，以及使用者在提交實驗時可能會遇到的相關聯狀態。

護欄|狀態|觸發條件&nbsp;&nbsp;
---|---|---
遺漏特徵值插補 |**通過** <br><br><br> **完成**| 在訓練資料中沒有偵測到任何遺漏特徵值。 深入了解[遺漏值插補。](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> 在訓練資料中偵測到遺漏的特徵值，並已進行插補。
高基數特徵處理 |**通過** <br><br><br> **完成**| 輸入已進行分析，且沒有偵測到任何高基數特徵。 深入了解[高基數特徵偵測。](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> 在輸入中偵測到高基數特徵，並已進行處理。
驗證分割處理 |**完成**| 驗證設定已設為「自動」，且訓練資料包含**少於** 20,000 個資料列。 <br> 已透過交叉驗證驗證定型模型的每個反覆項目。 深入了解[驗證資料。](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> 驗證設定已設為「自動」，且訓練資料包含**超過** 20,000 個資料列。 <br> 輸入資料已分割成訓練資料集和驗證資料集，以用於模型驗證。
類別平衡偵測 |**通過** <br><br><br><br> **警示** | 已分析輸入，且訓練資料中所有類別都是平衡的。 如果每個類別在資料集中的代表程度良好 (以樣本數和比例測量)，即會將資料集視為平衡。 <br><br><br> 在輸入中偵測到不平衡的類別。 若要修正模型偏差，請修正平衡問題。 深入了解[不平衡的資料。](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
記憶體問題偵測 |**通過** <br><br><br><br> **完成** |<br> 已分析選取的 {範圍、延隔時間、移動視窗} 值，且沒有偵測到任何潛在的記憶體不足問題。 深入了解時間序列[預測設定。](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) <br><br><br>已分析選取的 {範圍、延隔時間、移動視窗} 值，且可能會導致實驗記憶體不足。 延隔時間或移動視窗設定已關閉。
頻率偵測 |**通過** <br><br><br><br> **完成** |<br> 已分析時間序列，且所有資料點都與偵測到的頻率相符。 <br> <br> 已分析時間序列，並偵測到有些資料點與偵測到的頻率不相符。 這些資料點已從資料集移除。 深入了解[時間序列預測的資料準備。](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="run-experiment-and-view-results"></a>執行實驗並檢視結果

選取 [完成] 以執行實驗。 實驗準備流程最多需要 10 分鐘。 訓練作業可能需要額外 2-3 分鐘不等，才能讓每個管線完成執行。

### <a name="view-experiment-details"></a>檢視實驗詳細資料

[執行詳細資料] 畫面會在 [詳細資料] 索引標籤中開啟。此畫面會顯示實驗執行的摘要，並會在頂端執行編號的旁邊包含狀態列。 

[模型] 索引標籤包含依計量分數所建立的模型清單。 依預設，根據所選計量評分最高的模型會出現在清單頂端。 如果訓練作業嘗試多個模型，系統會將所有結果新增到清單中。 使用此方式快速比較到目前為止所產生的各個模型計量。

[![執行詳細資料儀表板](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>檢視訓練執行的詳細資料

向下切入任何已完成的模型，以查看訓練執行詳細資料，例如 [模型詳細資料] 索引標籤上的執行計量，以及 [視覺效果] 索引標籤上的效能圖表。[進一步了解圖表](how-to-understand-automated-ml.md)。

[![反覆項目詳細資料](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>部署模型

當手邊具備最佳模型時，即可將其作為 Web 服務部署以預測新的資料。

自動化 ML 可協助部署模型，而無須撰寫程式碼：

1. 您有數個部署選項。 

    + 選項 1：若要部署最佳模型 (根據所定義的計量準則)，請選取位於 [詳細資料] 索引標籤上的 [部署最佳模型] 按鈕。

    + 選項 2：若要從此實驗部署特定模型反覆項目，請向下切入模型以開啟其 [模型詳細資料] 索引標籤，然後選取 [部署模型]。

1. 填入 [部署模型] 窗格。

    欄位| 值
    ----|----
    名稱| 輸入部署的唯一名稱。
    描述| 輸入描述以更清楚地識別此部署的用途。
    計算類型| 選取想要部署的端點類型：*Azure Kubernetes Service (AKS)* 或 *Azure 容器執行個體 (ACI)* 。
    計算名稱| 僅適用於 AKS：選取想要部署的目標 AKS 叢集名稱。
    啟用驗證 | 選取允許以權杖為基礎或以金鑰為基礎的驗證。
    使用自訂部署資產| 若想要上傳自己的評分指令碼和環境檔案，請啟用此功能。 [深入了解評分指令碼](how-to-deploy-and-where.md#script)。

    >[!Important]
    > 檔案名稱必須少於 32 個字元，且必須以英數字元開始及結束。 其中可包含虛線、底線、點和英數字元。 不允許空格。

    「進階」功能表提供預設部署功能，例如[資料收集](how-to-enable-app-insights.md)和資源使用率設定。 若想要覆寫這些預設，請在此功能表中進行。

1. 選取 [部署]。 部署需要約 20 分鐘才能完成。

現在您已擁有可運作的 Web 服務，可用來產生預測！ 您可從 [Power BI 內建的 Azure Machine Learning 支援](how-to-consume-web-service.md#consume-the-service-from-power-bi)以透過查詢服務來測試預測。

## <a name="next-steps"></a>後續步驟

* [了解如何取用 Web 服務](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)。
* [了解自動化機器學習結果](how-to-understand-automated-ml.md)。
* [深入了解自動化機器學習](concept-automated-ml.md)和 Azure Machine Learning。
