---
title: Azure Machine Learning 中的模型 interpretability
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning SDK 來說明您的模型為何會進行預測。 它可以在定型和推斷期間用來瞭解模型如何進行預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 06/30/2020
ms.openlocfilehash: 97401b2bdbcc2dc1379505f8dade443a4f1eb318
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85601678"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Azure Machine Learning 中的模型 interpretability
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>模型 interpretability 總覽

對於資料科學家、審計員和商務決策者而言，Interpretability 非常重要，可確保符合公司政策、業界標準和政府法規：

+ 資料科學家需要能夠向主管和專案關係人說明其模型，讓他們能夠瞭解其發現的價值和精確度。 它們也需要 interpretability 來對其模型進行偵錯工具，並做出明智的決策以進行改善。 

+ 法律審核者需要工具來驗證有關法規合規性的模型，並監視模型的決策對人類的影響。 

+ 商務決策者必須能夠為使用者提供透明度，而需要安心的印象。 這可讓他們獲得並維護信任。


在模型開發的兩個主要階段中，啟用說明機器學習模型的功能非常重要：
+ 在定型階段期間，模型設計師和評估人員可以使用模型的 interpretability 輸出來驗證假設，並與專案關係人建立信任。 它們也會使用模型中的深入解析來進行偵錯工具、驗證模型行為是否符合其目標，以及檢查模型 unfairness 或不重要的功能。

+ 在推斷階段中，由於已部署模型的透明度，讓主管能夠瞭解模型的運作方式，以及其決定如何在真實生活中進行處理和影響人員。 

## <a name="interpretability-with-azure-machine-learning"></a>具有 Azure Machine Learning 的 Interpretability

Interpretability 類別可透過多個 SDK 套件取得：（瞭解如何[安裝適用于 Azure Machine Learning 的 sdk 套件](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)）

* `azureml.interpret`，主要封裝包含 Microsoft 支援的功能。

* `azureml.contrib.interpret`，您可以嘗試的、預覽和實驗性功能。

使用 `pip install azureml-interpret` 和 `pip install azureml-interpret-contrib` 進行一般使用，並 `pip install azureml-contrib-interpret` 讓 AutoML 用來取得 interpretability 套件。


> [!IMPORTANT]
> `contrib`未完全支援命名空間中的內容。 當實驗性功能變得成熟時，它們會逐漸移至主要命名空間。
.



## <a name="how-to-interpret-your-model"></a>如何解讀您的模型

使用 SDK 中的類別和方法，您可以：
+ 藉由產生整個模型和/或個別資料點的特徵重要性值來說明模型預測。 
+ 在定型和推斷期間，大規模地針對真實世界的資料集達成模型 interpretability。
+ 使用互動式視覺效果儀表板，探索資料中的模式和定型時間的說明


在機器學習中，**功能**是用來預測目標資料點的資料欄位。 例如，若要預測信用風險，可能會使用年齡、帳戶大小和帳戶存留期的資料欄位。 在此情況下，年齡、帳戶大小和帳戶存留期都是**功能**。 功能重要性告訴您每個資料欄位如何影響模型的預測。 例如，當帳戶大小和年齡不會大幅影響預測值時，在預測中可能會耗用大量時間。 此程式可讓資料科學家說明產生的預測，讓專案關係人能夠看到模型中最重要的功能。

在這裡瞭解支援的 interpretability 技術、支援的機器學習服務模型，以及支援的執行環境。


## <a name="supported-interpretability-techniques"></a>支援的 interpretability 技術

 `azureml-interpret`使用在[解讀-社區](https://github.com/interpretml/interpret-community/)中開發的 interpretability 技術，這是一個開放原始碼 python 套件，用於定型可解譯模型，並協助說明黑箱 AI 系統。 [解讀-社區](https://github.com/interpretml/interpret-community/)作為此 SDK 支援 explainers 的主機，目前支援下列 interpretability 技術：

|Interpretability 技術|描述|類型|
|--|--|--------------------|
|SHAP 樹狀說明| [SHAP](https://github.com/slundberg/shap)的樹狀說明，著重于多項式時間快速 SHAP 價值估計演算法，適用于**樹狀結構和整體的**樹系。|模型特定|
|SHAP 深層說明| 根據 SHAP 的說明，深度說明」是一種高速的近似值演算法，可在深度學習模型中，以[SHAP NIPS](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)檔中所述的 DeepLIFT 連接為基礎來 SHAP 值。 支援使用 TensorFlow 後端的**TensorFlow**模型和**Keras**模型（也有 PyTorch 的初步支援）」。|模型特定|
|SHAP 線性說明| SHAP 的線性說明會計算**線性模型**的 SHAP 值，並選擇性地會計入功能間的相互關聯。|模型特定|
|SHAP 核心說明| SHAP 的核心說明會使用特殊加權的區域線性回歸來估計**任何模型**的 SHAP 值。|與模型無關|
|模擬說明（全域代理）| 模擬說明是以定型[全域代理模型](https://christophm.github.io/interpretable-ml-book/global.html)來模擬黑箱模型的概念為基礎。 全域代理模型是一種內部可解譯模型，經過訓練以盡可能精確地大致估計**任何黑色方塊模型**的預測。 資料科學家可以解讀代理模型，以繪製有關黑色箱模型的結論。 您可以使用下列其中一個可解譯模型做為您的代理模型： LightGBM （LGBMExplainableModel）、線性回歸（LinearExplainableModel）、隨機梯度下降 explainable 模型（SGDExplainableModel）和決策樹（DecisionTreeExplainableModel）。|與模型無關|
|排列功能重要性說明（PFI）| 排列功能重要性是一種技術，用來說明[Breiman 的隨機](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf)樹系紙張所能啟發的分類和回歸模型（請參閱第10節）。 概括而言，它的運作方式是針對整個資料集，一次隨機亂數據一項功能，並計算感利率的效能計量有多大的變化。 變更愈大，該特性愈重要。 PFI 可以說明**任何基礎模型**的整體行為，但不會說明個別的預測。 |與模型無關|




除了上述的 interpretability 技術之外，我們還支援另一個以 SHAP 為基礎的說明，稱為 `TabularExplainer` 。 視模型而定，會 `TabularExplainer` 使用其中一個支援的 SHAP explainers：

* 所有以樹狀結構為基礎的模型 TreeExplainer
* DNN 模型的 DeepExplainer
* 線性模型的 LinearExplainer
* 所有其他模型的 KernelExplainer

`TabularExplainer`也透過直接 SHAP Explainers 進行了重大的功能和效能增強：

* **初始化資料集的摘要**。 在說明速度最重要的情況下，我們會摘要初始設定資料集，並產生一小組代表性的範例，以加速產生整體和個別功能的重要性值。
* **取樣評估資料集**。 如果使用者傳入大量的評估範例，但實際上並不需要進行評估，則可以將取樣參數設定為 true，以加速整體模型說明的計算。

下圖顯示目前支援的 explainers 結構。

[![Machine Learning Interpretability 架構](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>支援的機器學習模型

`azureml.interpret`SDK 的封裝支援使用下列資料集格式定型的模型：
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

說明函式接受模型和管線做為輸入。 如果提供模型，則模型必須實作用 `predict` 或 `predict_proba` 符合 scikit-learn 慣例的預測函數。 如果您的模型不支援這項功能，您可以將模型包裝在函式中，此函式會在 Scikit-learn 中產生與或相同的結果， `predict` `predict_proba` 並使用該包裝函式來搭配選取的說明。 如果提供管線，說明函式會假設執行中的管線腳本傳回預測。 使用這種包裝技術， `azureml.interpret` 可以支援透過 PyTorch、TensorFlow 和 Keras 深度學習架構，以及傳統機器學習模型定型的模型。

## <a name="local-and-remote-compute-target"></a>本機和遠端計算目標

`azureml.interpret`封裝是設計來搭配本機和遠端計算目標使用。 如果在本機執行，SDK 函式將不會與任何 Azure 服務連線。 

您可以在 Azure Machine Learning 計算中從遠端執行說明，並將說明資訊記錄到 Azure Machine Learning 執行歷程記錄服務中。 記錄這項資訊之後，Azure Machine Learning studio 就可以立即取得說明中的報告和視覺效果，以供使用者分析。


## <a name="next-steps"></a>後續步驟

- 請參閱[如何](how-to-machine-learning-interpretability-aml.md)在本機和 Azure Machine Learning 遠端計算資源上啟用模型定型的 interpretability。 
- 如需其他案例，請參閱[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。 
- 如果您對 interpretability 文字案例感興趣，請參閱 NLP 的 interpretability 技術的[解讀文字](https://github.com/interpretml/interpret-text)（一個相關的開放原始碼存放庫來[解讀](https://github.com/interpretml/interpret-community/)）。 `azureml.interpret`套件目前不支援這些技術，但您可以開始使用[文字分類的範例筆記本](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)。
