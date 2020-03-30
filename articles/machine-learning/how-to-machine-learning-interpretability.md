---
title: Azure 機器學習中的模型可解釋性
titleSuffix: Azure Machine Learning
description: 瞭解如何解釋模型為何使用 Azure 機器學習 SDK 進行預測。 它可以在訓練和推理期間使用，以瞭解模型如何進行預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063991"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Azure 機器學習中的模型可解釋性
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>模型可解釋性概述

可解釋性對於資料科學家和業務決策者來說都至關重要，以確保符合公司政策、行業標準和政府法規：
+ 資料科學家需要能夠向高管和利益相關者解釋他們的模型，以便他們能夠瞭解其發現的價值和準確性 
+ 業務決策者需要高枕無憂的能力，為最終使用者提供透明度，以獲得和維護其信任

在模型開發兩個主要階段，啟用解釋機器學習模型的能力非常重要：
+ 在機器學習模型開發週期的培訓階段。 模型設計人員和評估者可以使用模型的解釋性輸出來驗證假設並與利益相關者建立信任。 他們還使用對模型的見解進行調試，驗證模型行為與其目標相匹配，並檢查偏差或無足輕重的功能。
+ 在推斷階段，由於在部署的模型周圍具有透明度，使管理人員能夠瞭解"部署時"模型的工作原理，以及其決策在現實生活中如何對待和影響員工。 

## <a name="interpretability-with-azure-machine-learning"></a>Azure 機器學習的可解釋性

在本文中，您將瞭解如何在 SDK 中實現模型可解釋性概念。

使用 SDK 中的類和方法，您可以獲得：
+ 原始和工程要素的功能重要性值
+ 在訓練和推理期間，對實際資料集進行大規模解釋。
+ 互動式視覺化，説明您在培訓時發現資料模式和說明


在機器學習中，**功能**是用於預測目標資料點的資料欄位。 例如，為了預測信用風險，可以使用年齡、帳戶大小和帳戶年齡的資料欄位。 在這種情況下，年齡、帳戶大小和帳戶年齡是**功能**。 要素重要性告訴您每個資料欄位如何影響模型的預測。 例如，在預測中可能大量使用年齡，而帳戶大小和年齡不會對預測準確性產生顯著影響。 此過程允許資料科學家解釋結果預測，以便專案關係人能夠瞭解模型中最重要的資料點。

使用這些工具，您可以**解釋全球所有資料上的**機器學習模型，或者使用最先進的技術**在特定的資料點上**以便於使用和可擴展的方式在本地解釋機器學習模型。

可解釋性類可通過多個 SDK 包提供。 瞭解如何為[Azure 機器學習安裝 SDK 包](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* `azureml.interpret`，包含微軟支援的功能。

* `azureml.contrib.interpret`，預覽和實驗功能，你可以嘗試。

* `azureml.train.automl.automlexplainer`用於解釋自動機器學習模型的套裝軟體。

> [!IMPORTANT]
> 命名空間中`contrib`的內容不完全受支援。 隨著實驗功能的成熟，它們將逐步轉移到主命名空間。

## <a name="how-to-interpret-your-model"></a>如何解釋模型

您可以應用可解釋性類和方法來瞭解模型的全域行為或特定預測。 前者稱為全域解釋，後者稱為局部解釋。

這些方法也可以根據該方法是模型無關或特定于模型進行分類的。 某些方法針對某些類型的模型。 例如，SHAP 的樹解譯器僅適用于基於樹的模型。 某些方法將模型視為黑盒，例如類比解譯器或 SHAP 的內核解譯器。 該`interpret`包根據資料集、模型類型和用例利用這些不同的方法。

輸出是一組有關給定模型如何進行預測的資訊，例如：
* 全域/本地相對特徵重要性
* 全域/局部特徵和預測關係

### <a name="explainers"></a>解說員

此套裝軟體使用[解釋社區](https://github.com/interpretml/interpret-community/)開發的解釋性技術，這是一個開源 python 包，用於訓練可解釋模型並説明解釋黑盒 AI 系統。 [解釋社區](https://github.com/interpretml/interpret-community/)充當此 SDK 支援的解譯器的主機，目前支援以下可解釋性技術：

* **SHAP樹解譯器**[：SHAP](https://github.com/slundberg/shap)的樹解譯器，它側重于多聚時間快速SHAP值估計演算法，特定于樹和樹的合奏。
* **SHAP深度解譯器**：基於[SHAP](https://github.com/slundberg/shap)的解釋，深度解譯器"是深度學習模型中SHAP值的高速近似演算法，它建立在[SHAPNIPS論文](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)中描述的深度LIFT的連接之上。 支援使用 TensorFlow 後端的 TensorFlow 模型和 Keras 模型（也有對 PyTorch 的初步支援）"。
* **SHAP 線性解譯器**[：SHAP](https://github.com/slundberg/shap)的線性解譯器計算線性模型的 SHAP 值，可以選擇考慮特徵間相關性。

* **SHAP 內核解譯器**： [SHAP](https://github.com/slundberg/shap)的內核解譯器使用特殊加權的局部線性回歸來估計任何模型的 SHAP 值。
* **模仿解譯器**：模仿解譯器是基於訓練[全球代理模型](https://christophm.github.io/interpretable-ml-book/global.html)來類比黑盒模型的想法。 全域代理模型是一種內在可解釋的模型，經過訓練，可以盡可能準確地近似黑盒模型的預測。 資料科學家可以解釋代理模型，得出關於黑盒模型的結論。 您可以將以下可解釋模型之一用作代理模型：LightGBM （LGBM 可解釋模型）、線性回歸（線性可解釋模型）、隨機梯度下降可解釋模型 （SGD 可解釋模型）和決策樹 （決策樹可解釋模型）。


* **排列特徵重要性解譯器**：排列特徵重要性是一種技術，用於解釋分類和回歸模型，靈感來自[佈雷曼的隨機林論文](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf)（見第10節）。 在高級別上，其工作方式是隨機對整個資料集一次隨機洗牌一個要素，並計算興趣的性能指標變化程度。 變更愈大，該特性愈重要。

* **LIME解譯器**`contrib`（）： 基於[LIME，](https://github.com/marcotcr/lime)LIME 解譯器使用最先進的本地可解釋模型無關解釋 （LIME） 演算法來創建本地代理模型. 與全域代理模型不同，LIME 側重于訓練本地代理模型來解釋單個預測。
* **HAN 文本解譯器**（`contrib`）： HAN 文本解譯器使用分層注意網路從給定黑盒文本模型的文本資料獲取模型說明。 它在給定黑盒模型的預測輸出上訓練 HAN 代理模型。 在整個文本語料庫進行全球培訓後，它會為特定文檔添加微調步驟，以提高解釋的準確性。 HAN 使用具有兩個注意層的雙向 RNN，用於句子和單詞關注。 一旦對 DNN 進行了黑盒模型的培訓，並針對特定文檔進行了微調，使用者就可以從注意層中提取單詞重要性。 對於文本資料，HAN 比 LIME 或 SHAP 更準確，但在培訓時間方面也更昂貴。 已進行了改進，使使用者可以選擇使用 GloVe 字嵌入初始化網路，以減少培訓時間。 通過在遠端 Azure GPU VM 上運行 HAN 可以顯著提高培訓時間。 HAN 的實現在["文檔分類的分層關注網路（Yang 等人，2016 年）"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)中進行了描述。


* **表格解譯器**：`TabularExplainer`使用以下邏輯調用直接[SHAP](https://github.com/slundberg/shap)解譯器：

    1. 如果是基於樹的模型，請應用 SHAP `TreeExplainer`，否則
    2. 如果是 DNN 模型，請應用 SHAP `DeepExplainer`，否則
    3. 如果是線性模型，請應用 SHAP `LinearExplainer`，否則
    3. 將其視為黑盒模型，並應用 SHAP`KernelExplainer`


`TabularExplainer`還對直接 SHAP 解譯器進行了顯著的功能和性能增強：

* **初始化資料集的匯總**。 在解釋速度最重要的情況下，我們將初始化資料集匯總並生成一小組具有代表性的樣本，從而加快全域和本地解釋。
* **採樣評估資料集**。 如果使用者傳遞了一組大量評估樣本，但實際上並不需要對其進行所有評估，則可以將採樣參數設置為 true 以加快全域解釋。

下圖顯示了直接解譯器和元解譯器的當前結構。

[![機器學習可解釋性體系結構](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>支援模型

任何`numpy.array`在 Python 、、`pandas.DataFrame`或`iml.datatypes.DenseData``scipy.sparse.csr_matrix`格式中訓練資料集的模型都受 SDK 的解釋`explain`包的支援。

解釋函數接受模型和管道作為輸入。 如果提供了模型，則模型必須實現預測函數`predict`或`predict_proba`符合 Scikit 約定。 如果提供了管道（管道腳本的名稱），則解釋函數假定正在運行的管道腳本返回預測。 我們支援通過 PyTorch、TensorFlow 和 Keras 深度學習框架培訓的模型。

### <a name="local-and-remote-compute-target"></a>本地和遠端計算目標

該`explain`包旨在同時處理本地和遠端計算目標。 如果在本地運行，SDK 函數將不會與任何 Azure 服務聯繫。 您可以在 Azure 機器學習計算上遠端運行說明，並將說明資訊記錄到 Azure 機器學習執行歷程記錄服務中。 記錄此資訊後，在 Azure 機器學習工作區上隨時提供解釋中的報表和視覺化效果，以便進行使用者分析。


## <a name="next-steps"></a>後續步驟

請參閱如何[為](how-to-machine-learning-interpretability-aml.md)本地和 Azure 機器學習遠端計算資源定型的模型啟用可解釋性。 有關其他方案[，請參閱示例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
