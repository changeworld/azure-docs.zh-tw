---
title: 建立 Python 模型:模組參照
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"創建 Python 模型「模組創建自定義建模或數據處理模組。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c8be0882452dc120f538394a5481769e26e3fa15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682812"
---
# <a name="create-python-model-module"></a>建立 Python 模型模組

本文介紹 Azure 機器學習設計器(預覽)中的模組。

瞭解如何使用創建 Python 模型模組從 Python 文稿建立未經訓練的模型。 可以將模型基於 Azure 機器學習設計器環境中 Python 包中包含的任何學員。 

創建模型后,可以使用[訓練模型](train-model.md)在數據集上訓練模型,就像 Azure 機器學習中的任何其他學員一樣。 訓練模型可以傳遞給[分數模型](score-model.md)進行預測。 然後,您可以保存已訓練的模型,並將評分工作流發佈為 Web 服務。

> [!WARNING]
> 目前,無法將 Python 模型的評分結果傳遞給[評估模型](evaluate-model.md)。 如果需要評估模型,可以使用[執行 Python 文本](execute-python-script.md)模組編寫自訂 Python 文稿並運行它。  


## <a name="configure-the-module"></a>設定模組

使用此模組需要 Python 的中間或專業知識。 該模組支援使用 Azure 機器學習中已安裝的 Python 包中包含的任何學員。 請參考[Python 文稿](execute-python-script.md)中預先安裝的 Python 套件清單。

> [!NOTE]
> 編寫文本時請非常小心,並確保沒有語法錯誤,例如使用未聲明的物件或未導入的模組。

> [!NOTE]
還要特別注意[執行 Python 文稿](execute-python-script.md)中預安裝的模組清單。 僅導入預安裝的模組。 請不要在此文本中安裝額外的套件,如"pip 安裝 xgboost",否則在閱讀下游模組中的模型時將引發錯誤。
  
本文演示如何將**創建 Python 模型**與簡單的管道一起使用。 下面是導管圖:

![建立 Python 模型圖](./media/module/create-python-model.png)

1. 選擇 **「創建 Python 模型**」,然後編輯文稿以實現建模或資料管理過程。 可以將模型基於 Azure 機器學習環境中 Python 包中包含的任何學員。

> [!NOTE]
> 請特別注意腳本示例代碼中的註釋,並確保腳本嚴格遵守要求,包括類名稱、方法和方法簽名。 衝突將導致異常。 

   以下雙類 Naive Bayes 分類器的範例代碼使用流行的*sklearn*包:

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

1. 連接剛剛建立的用於**訓練模型**和**分數模型**的**創建 Python 模型**模組。

1. 如果需要評估模型,請添加執行 Python[文稿](execute-python-script.md)模組並編輯 Python 文稿。

   以下文稿是範例評估代碼:

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

請參考 Azure 機器學習[可用的模組集](module-reference.md)。 