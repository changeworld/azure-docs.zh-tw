---
title: 'Azure Machine Learning (預覽中的模型可解譯性) '
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning SDK 來說明模型為何會進行預測。 您可以在定型和推斷期間使用它，以瞭解您的模型如何進行預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.openlocfilehash: f98e18abb8ba06ea632ee9c63c1a726879e825d2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311521"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Azure Machine Learning (預覽中的模型可解譯性) 


## <a name="overview-of-model-interpretability"></a>模型可解譯性總覽

對於資料科學家、審計員和企業決策者而言，可解譯性是非常重要的，可確保符合公司原則、產業標準和政府法規：

+ 資料科學家需要能夠向主管和專案關係人說明其模型，讓他們可以瞭解其結果的價值和精確度。 它們也需要可解譯性來對其模型進行偵測，並做出有關如何改進的明智決策。 

+ 法律審核者需要工具來驗證有關法規合規性的模型，並監視模型的決策對人類的影響。 

+ 企業決策者必須能夠為終端使用者提供透明度，因此需要安心。 這可讓他們獲得和維護信任。


在模型開發的兩個主要階段中，啟用說明機器學習模型的功能很重要：
+ 在定型階段期間，模型設計師和評估工具可以使用模型的可解譯性輸出來驗證假設，並與專案關係人建立信任關係。 它們也會使用模型的深入解析來進行偵測、驗證模型行為符合其目標，以及檢查模型不公平性或無意義的功能。

+ 在推斷階段期間，在部署模型周圍具有透明度，可讓主管瞭解「部署時」模型的運作方式，以及其決定如何處理和影響現實生活中的人員。 

## <a name="interpretability-with-azure-machine-learning"></a>Azure Machine Learning 的可解譯性

可解譯性類別可透過下列 SDK 套件取得： (瞭解如何 [安裝適用于 Azure Machine Learning 的 SDK 套件](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)) 

* `azureml.interpret`，包含 Microsoft 所支援的功能。

用於 `pip install azureml-interpret` 一般用途。

## <a name="how-to-interpret-your-model"></a>如何解讀模型

使用 SDK 中的類別和方法，您可以：
+ 藉由產生整個模型及/或個別資料點的特徵重要性值來說明模型預測。 
+ 在定型和推斷期間，大規模達成真實世界資料集的模型可解譯性。
+ 使用互動式視覺效果儀表板來探索資料中的模式和定型時間的說明


在機器學習中， **功能** 是用來預測目標資料點的資料欄位。 例如，若要預測信用風險，可能會使用年齡、帳戶大小和帳戶存留期的資料欄位。 在此情況下，年齡、帳戶大小和帳戶存留期都是 **功能** 。 功能重要性會告訴您每個資料欄位如何影響模型的預測。 例如，年齡可能會在預測中大量使用，而帳戶大小和年齡不會大幅影響預測值。 此程式可讓資料科學家說明產生的預測，讓專案關係人可以看到模型中最重要的功能。

在這裡瞭解支援的可解譯性技術、支援的機器學習模型，以及支援的執行環境。


## <a name="supported-interpretability-techniques"></a>支援的可解譯性技術

 `azureml-interpret` 使用在 [解讀](https://github.com/interpretml/interpret-community/)可解譯性中開發的技術，這是一個開放原始碼 python 套件，用於定型可解譯模型，並協助說明黑箱 AI 系統。 [解讀-社區](https://github.com/interpretml/interpret-community/) 可作為此 SDK 支援 explainers 的主機，目前支援下列可解譯性技術：

|可解譯性技術|描述|類型|
|--|--|--------------------|
|SHAP 樹狀結構說明| [SHAP](https://github.com/slundberg/shap)的樹狀說明，其焦點在於多項式時間快速 SHAP 的值估計演算法，適用于樹狀結構 **和整體的** 樹狀結構。|模型特定|
|SHAP 深層說明| 深度說明是以 SHAP 的說明為基礎，這是在深度學習模型中 SHAP 值的高速近似值演算法，而這些模型是以 [SHAP NIPS 紙](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)中所述之 DeepLIFT 的連接為基礎。 支援使用 TensorFlow 後端 **TensorFlow** 模型和 **Keras** 模型 (也有 PyTorch) 」的初步支援。|模型特定|
|SHAP 線性說明| SHAP 的線性說明會計算 **線性模型** 的 SHAP 值，並選擇性地針對功能間的相互關聯進行計算。|模型特定|
|SHAP 核心說明| SHAP 的核心說明會使用特殊加權的本機線性回歸來估計 **任何模型** 的 SHAP 值。|模型中立|
|模仿說明 (全域代理) | 模擬說明是以定型 [全域代理模型](https://christophm.github.io/interpretable-ml-book/global.html) 來模擬黑箱模型的概念為基礎。 全域代理模型是一種本質上的可解譯模型，經過定型以盡可能精確地估計 **任何黑色箱模型** 的預測。 資料科學家可以解讀代理模型，以繪製有關黑色箱模型的結論。 您可以使用下列其中一個可解譯模型做為您的代理模型： LightGBM (LGBMExplainableModel) 、線性回歸 (LinearExplainableModel) 、隨機梯度下降解釋模型 (SGDExplainableModel) ，以及決策樹 (DecisionTreeExplainableModel) 。|模型中立|
|排列功能重要性說明 (PFI) | 排列功能重要性是一種技術，用來說明 [Breiman 的隨機](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) 樹系紙張所啟發的分類和回歸模型 (請參閱第10節) 。 概括而言，它的運作方式是針對整個資料集一次隨機跳過一項功能，並計算所需的效能度量有多大。 變更愈大，該特性愈重要。 PFI 可以解釋 **任何基礎模型** 的整體行為，但不會說明個別預測。 |模型中立|




除了上述的可解譯性技術，我們還支援另一個以 SHAP 為基礎的說明，稱為 `TabularExplainer` 。 視模型而定，會 `TabularExplainer` 使用其中一個支援的 SHAP explainers：

* 所有以樹狀結構為基礎的模型的 TreeExplainer
* 適用于 DNN 模型的 DeepExplainer
* 線性模型的 LinearExplainer
* 適用于所有其他模型的 KernelExplainer

`TabularExplainer` 也透過直接 SHAP Explainers 進行了大幅的功能和效能增強功能：

* **初始化資料集的摘要** 。 在說明速度最重要的情況下，我們摘要了初始設定資料集，並產生一組小型的代表性範例，以加速產生整體和個別的特徵重要性值。
* **取樣評估資料集** 。 如果使用者傳入大量的評估範例，但實際上並不需要進行評估，則可以將取樣參數設為 true，以加速整體模型說明的計算。

下圖顯示目前支援的 explainers 結構。

[![Machine Learning 可解譯性架構](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>支援的機器學習模型

`azureml.interpret`SDK 套件支援使用下列資料集格式定型的模型：
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

說明函式接受模型和管線做為輸入。 如果有提供模型，模型必須執行預測函數， `predict` 或 `predict_proba` 符合 scikit-learn 慣例。 如果您的模型不支援這種情況，您可以將模型包裝在產生和 Scikit-learn 相同結果的函式中， `predict` `predict_proba` 並搭配選取的說明使用該包裝函式函數。 如果提供了管線，說明函式會假設執行中的管線腳本會傳回預測。 使用這個包裝技術， `azureml.interpret` 可支援透過 PyTorch、TensorFlow 和 Keras 深度學習架構，以及傳統機器學習模型來定型的模型。

## <a name="local-and-remote-compute-target"></a>本機和遠端計算目標

`azureml.interpret`封裝是設計來搭配本機和遠端計算目標使用。 如果在本機執行，SDK 函式將不會聯繫任何 Azure 服務。 

您可以從遠端執行 Azure Machine Learning 計算的說明，並將說明資訊記錄到 Azure Machine Learning 的執行歷程記錄服務中。 記錄這項資訊之後，就可以在 Azure Machine Learning studio 進行使用者分析，立即取得說明中的報表和視覺效果。


## <a name="next-steps"></a>後續步驟

- 請參閱 [如何](how-to-machine-learning-interpretability-aml.md) 在本機和 Azure Machine Learning 遠端計算資源上啟用模型定型的可解譯性。 
- 如需其他案例，請參閱 [範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) 。 
- 如果您對文字案例的可解譯性有興趣，請參閱 [解讀文字](https://github.com/interpretml/interpret-text)（相關的開放原始碼存放 [庫），以瞭解](https://github.com/interpretml/interpret-community/)NLP 的可解譯性技術。 `azureml.interpret` 套件目前不支援這些技術，但您可以從 [文字分類上的範例筆記本](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)開始著手。