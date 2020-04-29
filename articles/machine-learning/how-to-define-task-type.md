---
title: 定義自動化機器學習執行的機器學習工作
titleSuffix: Azure Machine Learning
description: 瞭解如何定義自動化機器學習執行的機器學習工作
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: fef3f0347d963b8a7d5fe2f1ca771293e9c3d2a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475493"
---
# <a name="how-to-define-a-machine-learning-task"></a>如何定義機器學習工作 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解支援的機器學習服務工作，以及如何定義它們以進行自動化機器學習（自動化 ML）實驗執行。


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>什麼是機器學習服務工作？

機器學習工作表示藉由建立預測性模型所解決的問題類型。 自動化 ML 支援三種不同類型的工作，包括分類、回歸和時間序列預測。

工作類型| 描述| 範例
----|----|----
分類 | 用來預測資料集中特定資料列之類別目錄的工作。 | 信用卡上的詐騙偵測。 目標資料行會偵測**到**類別為*True*或*False*的詐騙。 在此情況下，我們會將資料中的每一列分類為 true 或 false。
迴歸 | 預測連續數量輸出的工作。 | 汽車成本根據其功能而定，目標資料行會是**價格**。
預測 |此工作可讓您在判斷未來趨勢的方向時做出明智的評估。| 預測接下來48小時的能源需求。 目標資料行會是**需求**，而預測值會用來顯示能源需求中的模式。

在自動化和調整程序期間，自動化 ML 支援下列演算法。 身為使用者，您不需要指定演算法。

分類 | 迴歸 | 時間序列預測
-- |-- |--
[羅吉斯回歸](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[決策樹](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)|[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[線性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C 支援向量分類 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 分類器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 回歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 回歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 線性分類器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[線性回歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[線性回歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[貝氏機率分類](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>設定工作類型
您可以使用 SDK 或 Azure Machine Learning studio 來設定自動化 ML 實驗的工作類型。

請在`task`函式中`AutoMLConfig`使用參數來指定您的實驗類型。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

在 Azure Machine Learning studio 中，您可以將工作設定為自動化 ML 實驗執行建立的一部分。 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![工作類型選取範圍](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>後續步驟

+ 深入瞭解 Azure Machine Learning 中的[自動化 ml](concept-automated-ml.md) 。
+ 深入瞭解如何在 Azure Machine Learning 中[自動定型時間序列預測模型](how-to-auto-train-forecast.md)
+ 試用[自動化的 Machine Learning 分類](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)教學課程。
+ 試用[自動化的 Machine Learning 回歸](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)範例筆記本。

