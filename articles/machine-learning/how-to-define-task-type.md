---
title: 為自動機器學習執行定義機器學習工作
titleSuffix: Azure Machine Learning
description: 瞭解如何為自動機器學習執行定義機器學習任務
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: fef3f0347d963b8a7d5fe2f1ca771293e9c3d2a9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475493"
---
# <a name="how-to-define-a-machine-learning-task"></a>如何定義機器學習工作 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中,您將瞭解支援的機器學習任務以及如何為自動機器學習 (自動 ML) 實驗運行定義它們。


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>什麼是機器學習任務?

機器學習任務表示通過創建預測模型正在解決的問題類型。 自動 ML 支援三種不同類型的任務,包括分類、回歸和時間序列預測。

工作類型| 描述| 範例
----|----|----
分類 | 用於預測資料集中特定行的類別的任務。 | 信用卡上的欺詐檢測。 目標列將是「**欺詐」** 類別為 *「真*」或 *「假*」。。 在這種情況下,我們將數據中的每一行分類為真或假。
迴歸 | 用於預測連續數量輸出的任務。 | 汽車成本根據其特點,目標列將是**價格**。
預測 |在確定未來趨勢方向時作出知情估計的任務。| 預測未來48小時的能源需求。 目標列為**需求**,預測值將用於顯示能源需求模式。

在自動化和調整程序期間，自動化 ML 支援下列演算法。 身為使用者，您不需要指定演算法。

分類 | 迴歸 | 時間序列預測
-- |-- |--
[邏輯回歸](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [彈性網路](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[漸層停駐提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[決策樹](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)|[決策樹](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 最近鄰演算法](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[線性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C 支援向量分類 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[隨機樹系](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[極度隨機樹狀結構](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 類別器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 回歸器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 回歸器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 線性類別器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[線性迴歸器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[線性迴歸器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[貝氏機率分類](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[隨機梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>設定工作型態
您可以使用 SDK 或 Azure 機器學習工作室為自動 ML 實驗設置任務類型。

使用建構`task`函數`AutoMLConfig`中的 參數指定實驗類型。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

您可以將任務設置為 Azure 機器學習工作室中自動 ML 實驗運行創建的一部分。 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![工作類型選擇](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>後續步驟

+ 詳細瞭解 Azure 機器學習中的[自動 ml。](concept-automated-ml.md)
+ 瞭解有關在 Azure 機器學習中[自動訓練時間序列預測模型](how-to-auto-train-forecast.md)的更多
+ 嘗試[自動機器學習分類](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)教程。
+ 嘗試[自動機器學習回歸](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)示例筆記本。

