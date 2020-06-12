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
ms.openlocfilehash: cb2fbcda7f30db24b876a66df22071f14df74814
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653242"
---
# <a name="how-to-define-a-machine-learning-task"></a>如何定義機器學習工作 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將了解支援的機器學習工作，以及如何定義工作以進行自動化機器學習 (自動化 ML) 的實驗執行。


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a> 什麼是機器學習工作？

機器學習工作代表藉由建立預測性模型所解決的問題類型。 自動化 ML 支援三種不同類型的工作，包括分類、迴歸、時間序列預測。

工作類型| 描述| 範例
----|----|----
分類 | 這類工作用來預測資料集中特定資料列的類別。 | 信用卡的詐騙偵測。 目標資料行會是**偵測到詐騙**的分類為 True 或 False。 在此情況下，我們會將資料中的每一列分類為 true 或 false。
迴歸 | 這類工作用來預測連續的數量輸出。 | 汽車成本根據其功能而定，目標資料行為**價格**。
預測 |這類工作可在判斷未來趨勢的方向時做出明智的評估。| 預測接下來 48 小時的能源需求。 目標資料行會是**需求**，且預測值會用來顯示能源需求的模式。

在自動化和調整程序期間，自動化 ML 支援下列演算法。 身為使用者，您不需要指定演算法。

分類 | 迴歸 | 時間序列預測
-- |-- |--
[羅吉斯迴歸](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[決策樹](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)|[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[線性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C 支援向量分類 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 分類器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 迴歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 迴歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 線性分類器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[線性迴歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[線性迴歸輸入變數](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[貝氏機率分類](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>設定工作類型
您可以使用 SDK 或 Azure Machine Learning Studio 來設定自動化 ML 實驗的工作類型。

在 `AutoMLConfig` 建構函式中使用 `task` 參數指定您的實驗類型。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

在 Azure Machine Learning Studio 中，您可以將工作設定為自動化 ML 實驗執行建立的一部分。 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![工作類型選取](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>後續步驟

+ 深入瞭解 Azure Machine Learning 中的[自動化機器學習](concept-automated-ml.md)。
+ 深入瞭解 Azure Machine Learning 中的[自動定型時間序列預測模型](how-to-auto-train-forecast.md)。
+ 嘗試[自動化 Machine Learning 分類](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)教學課程。
+ 嘗試[自動化 Machine Learning 迴歸](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb) 範例筆記本。

