---
title: Azure 機器學習中的模型可解釋性
titleSuffix: Azure Machine Learning
description: 瞭解如何解釋模型為何使用 Azure 機器學習 SDK 進行預測。 它可以在訓練和推理期間使用,以瞭解模型如何進行預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631405"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Azure 機器學習中的模型可解釋性
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>模型可解釋性概述

可解釋性對於數據科學家、審核員和業務決策者來說都至關重要,以確保符合公司政策、行業標準和政府法規:

+ 數據科學家需要能夠向高管和利益相關者解釋他們的模型,以便他們能夠理解其發現的價值和準確性。 它們還需要可解釋性來調試其模型,並就如何改進模型做出明智的決策。 

+ 法律審計師需要工具來驗證與法規遵從性有關的模式,並監控模型的決策如何影響人類。 

+ 業務決策者需要通過有能力為最終使用者提供透明度來平和。 這使他們能夠贏得並保持信任。


在模型開發兩個主要階段,啟用解釋機器學習模型的能力非常重要:
+ 在培訓階段,作為模型設計人員和評估者,可以使用模型的解釋性輸出來驗證假設並與利益相關者建立信任。 他們還使用對模型的見解進行調試,驗證模型行為與其目標相匹配,並檢查模型不公平或功能不重要。

+ 在推斷階段,由於在部署的模型周圍具有透明度,使管理人員能夠瞭解"部署時"模型的工作原理,以及其決策在現實生活中如何對待和影響員工。 

## <a name="interpretability-with-azure-machine-learning"></a>Azure 機器學習的可解釋性

可解釋性類可透過多個 SDK 套件提供:(瞭解如何為[Azure 機器學習安裝 SDK 包](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py))

* `azureml.interpret`,包含微軟支援的功能。

* `azureml.contrib.interpret`,預覽和實驗功能,你可以嘗試。

* `azureml.train.automl.automlexplainer`用於解釋自動機器學習模型的軟體包。

用於`pip install azureml-interpret`和`pip install azureml-interpret-contrib`用於一般用途,`pip install azureml-interpret-contrib`以及用於自動ML獲取可解釋性包。


> [!IMPORTANT]
> 命名空間中`contrib`的內容不完全受支援。 隨著實驗功能的成熟,它們將逐步轉移到主命名空間。
.



## <a name="how-to-interpret-your-model"></a>如何解釋模型

使用 SDK 的類別和方法,可以:
+ 通過為整個模型和/或單個數據點生成要素重要性值來解釋模型預測。 
+ 在培訓和推理期間,在大規模實現實際數據集的模型可解釋性。
+ 使用互動式可視化儀表板在培訓時發現數據模式和說明


在機器學習中,**功能**是用於預測目標數據點的數據欄位。 例如,為了預測信用風險,可以使用年齡、帳戶大小和帳戶年齡的數據欄位。 在這種情況下,年齡、帳戶大小和帳戶年齡是**功能**。 要素重要性告訴您每個數據欄位如何影響模型的預測。 例如,在預測中可能大量使用年齡,而帳戶大小和年齡不會對預測值產生重大影響。 此過程允許數據科學家解釋結果預測,以便利益幹系人能夠瞭解模型中最重要的要素。

在此處瞭解受支援的可解釋性技術、支援的機器學習模型和支援的運行環境。


## <a name="supported-interpretability-techniques"></a>支援的可解釋性技術

 `azureml-interpret`使用[解釋社區](https://github.com/interpretml/interpret-community/)開發的解釋性技術,這是一個開源的python包,用於訓練可解釋的模型,並幫助解釋黑盒AI系統。 [解釋社區](https://github.com/interpretml/interpret-community/)充當此 SDK 支援的解釋器的主機,目前支援以下可解釋性技術:

|可解釋性技術|描述|類型|
|--|--|--------------------|
|1. SHAP 樹解釋器| [SHAP](https://github.com/slundberg/shap)的樹示程式,它側重於多聚時間快速SHAP值估計演演算法特定於**樹木和樹的合奏**。|特定於型號|
|2. SHAP 深度解說器| 根據[SHAP](https://github.com/slundberg/shap)的解釋,深度解釋器「是深度學習模型中SHAP值的高速近似演演演算法,它建立在[SHAPNIPS 論文](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)中描述的深度LIFT的連接之上。 支援使用 TensorFlow 後端的**TensorFlow**模型和**Keras**模型(也有對 PyTorch 的初步支援)」。|特定於型號|
|3. SHAP 線性直譯器| [SHAP](https://github.com/slundberg/shap)的線性解釋器計算**線性模型**的 SHAP 值,可以選擇考慮特徵間相關性。|特定於型號|
|4. SHAP 內核解譯器| [SHAP](https://github.com/slundberg/shap)的內核解釋器使用特殊加權的局部線性回歸來估計**任何模型**的 SHAP 值。|模型無關|
|5. 模擬直譯器(全球代理)| 模擬解釋器基於訓練[全域代理模型](https://christophm.github.io/interpretable-ml-book/global.html)來類比黑水模型的想法。 全域代理模型是一種內在可解釋的模型,經過訓練,可以儘可能準確地近似**任何黑盒模型**的預測。 數據科學家可以解釋代理模型,以得出有關黑盒模型的結論。 您可以將以下可解釋模型之一用作代理模型:LightGBM(LGBM 可解釋模型)、線性回歸(線性可解釋模型)、隨機梯度下降可解釋模型(SGD 可解釋模型)和決策樹(決策樹可解釋模型)。|模型無關|
|6. 排列特徵重要性直譯器 (PFI)| 排列特徵重要性是一種技術,用於解釋分類和回歸模型,靈感來自[佈雷曼的隨機森林論文](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf)(參見第10節)。 在高級別上,其工作方式是隨機對整個數據集一次隨機洗牌一個要素,並計算興趣的性能指標變化程度。 變更愈大，該特性愈重要。 PFI 可以解釋**任何基礎模型**的總體行為,但不解釋單個預測。 |模型無關|




除了上述可解釋性技術外,我們支援另一種[基於SHAP的解釋器](https://github.com/slundberg/shap)`TabularExplainer`,稱為 。 根據模型,`TabularExplainer`使用支援的 SHAP 直譯器之一:

* 所有基於樹模型的樹直譯器
* DNN 型號的深層直譯器
* 線性模型的線性直譯器
* 所有其他型號的內核直譯器

`TabularExplainer`還對直接 SHAP 解釋器進行了顯著的功能和性能增強:

* **初始化資料集的彙總**。 在解釋速度最重要的情況下,我們將初始化數據集彙總並生成一小組具有代表性的樣本,從而加快生成整體和單個要素重要性值。
* **取樣評估資料集**。 如果用戶傳遞了一組大型評估樣本,但實際上並不需要對其進行所有評估,則可以將採樣參數設置為 true 以加快計算總體模型解釋。

下圖顯示了受支持的解釋器的當前結構。

[![機器學習可解釋性體系結構](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>支援的機器學習模型

SDK`azureml.interpret`套件支援使用以下資料集格式訓練的模型:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

解釋函數接受模型和管道作為輸入。 如果提供了模型,則模型必須實現預測函數`predict``predict_proba`或 符合 Scikit 約定。 如果模型不支援此功能,則可以將模型包裝在生成與`predict`Scikit 相同結果的函數`predict_proba`中, 並將該包裝函數與選定的解釋器一起使用。 如果提供了管道,則解釋函數假定正在運行的管道腳本返回預測。 使用此包裝技術,`azureml.interpret`可以支援透過 PyTorch、TensorFlow 和 Keras 深度學習架構以及傳統機器學習模型訓練的模型。

## <a name="local-and-remote-compute-target"></a>本地端與遠端計算目標

該`azureml.interpret`包旨在同時處理本地和遠端計算目標。 如果在本地運行,SDK 函數將不會與任何 Azure 服務聯繫。 

您可以在 Azure 機器學習計算上遠端運行說明,並將說明資訊記錄到 Azure 機器學習執行歷史記錄服務中。 記錄此資訊后,在 Azure 機器學習工作室隨時提供解釋中的報表和可視化效果,以便進行使用者分析。


## <a name="next-steps"></a>後續步驟

請參閱如何[為](how-to-machine-learning-interpretability-aml.md)本地和 Azure 機器學習遠端計算資源定型的模型啟用可解釋性。 有關其他方案[,請參閱示例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
