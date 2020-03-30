---
title: 創建 Python 模型：模組引用
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"創建 Python 模型"模組創建自訂建模或資料處理模組。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 929938bba9c9512ecfd663a540cf4a7ebbf68e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371812"
---
# <a name="create-python-model-module"></a>創建 Python 模型模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

瞭解如何使用創建 Python 模型模組從 Python 腳本創建未經訓練的模型。 可以將模型基於 Azure 機器學習設計器環境中 Python 包中包含的任何學員。 

創建模型後，可以使用[訓練模型](train-model.md)在資料集上訓練模型，就像 Azure 機器學習中的任何其他學員一樣。 訓練模型可以傳遞給[分數模型](score-model.md)進行預測。 然後，您可以保存已訓練的模型，並將評分工作流發佈為 Web 服務。

> [!WARNING]
> 目前，無法將 Python 模型的評分結果傳遞給[評估模型](evaluate-model.md)。 如果需要評估模型，可以使用[執行 Python 腳本](execute-python-script.md)模組編寫自訂 Python 腳本並運行它。  


## <a name="configure-the-module"></a>配置模組

使用此模組需要 Python 的中間或專業知識。 該模組支援使用 Azure 機器學習中已安裝的 Python 包中包含的任何學員。 請參閱[執行 Python 腳本](execute-python-script.md)中預先安裝的 Python 包清單。
  

本文演示如何將**創建 Python 模型**與簡單的管道一起使用。 下面是管道圖：

![創建 Python 模型圖](./media/module/create-python-model.png)

1. 選擇 **"創建 Python 模型**"，然後編輯腳本以實現建模或資料管理過程。 可以將模型基於 Azure 機器學習環境中 Python 包中包含的任何學員。

   以下雙類 Naive Bayes 分類器的示例代碼使用流行的*sklearn*包：

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

1. 連接剛剛創建的用於**訓練模型**和**分數模型**的**創建 Python 模型**模組。

1. 如果需要評估模型，請添加執行 Python[腳本](execute-python-script.md)模組並編輯 Python 腳本。

   以下腳本是示例評估代碼：

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function can contain up to two input arguments:
   #   Param<dataframe1>: a pandas.DataFrame
   #   Param<dataframe2>: a pandas.DataFrame
   def azureml_main(dataframe1 = None, dataframe2 = None):
    
       from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
       import pandas as pd
       import numpy as np
    
       scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
       ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
       ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
       probabilities = scores["probabilities"]
    
       accuracy, precision, recall, auc = \
       accuracy_score(ytrue, ypred),\
       precision_score(ytrue, ypred),\
       recall_score(ytrue, ypred),\
       roc_auc_score(ytrue, probabilities)
    
       metrics = pd.DataFrame();
       metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
       metrics["Value"] = [accuracy, precision, recall, auc]
    
       return metrics,

   ```

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 