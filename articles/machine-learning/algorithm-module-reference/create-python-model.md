---
title: 建立 Python 模型：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的「建立 Python 模型」模組來建立自訂模型或資料處理模組。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/18/2020
ms.openlocfilehash: 03f80ebeb7b30d8c0fe14060335541d77b7523b0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898618"
---
# <a name="create-python-model-module"></a>建立 Python 模型模組

本文描述 Azure Machine Learning 設計工具中的模組。

瞭解如何使用「建立 Python 模型」模組，從 Python 腳本建立未定型的模型。 您可以在 Azure Machine Learning 設計工具環境中，以 Python 套件中包含的任何學習模組作為模型基礎。 

建立模型之後，您可以使用 [定型模型](train-model.md) ，將資料集上的模型定型，如同 Azure Machine Learning 中的任何其他學習模組。 定型的模型可傳遞至 [評分模型](score-model.md) 以進行預測。 然後，您可以儲存定型的模型，並將評分工作流程發佈為 web 服務。

> [!WARNING]
> 目前，無法連接此模組來 **微調模型超參數** 模組，或將 Python 模型的計分結果傳遞至 [評估模型](evaluate-model.md)。 如果您需要微調超參數或評估模型，您可以使用 [ [執行 Python 腳本](execute-python-script.md) ] 模組來撰寫自訂的 python 腳本。


## <a name="configure-the-module"></a>設定模組

使用此課程模組需要 Python 的中繼或專業知識。 此模組支援使用已安裝在 Azure Machine Learning 中的 Python 套件所包含的任何學習模組。 請參閱 [執行 Python 腳本](execute-python-script.md)中預先安裝的 python 套件清單。

> [!NOTE]
> 撰寫腳本時請務必小心，並確定沒有語法錯誤，例如使用未宣告的物件或未匯入的模組。

> [!NOTE]
> 此外，也需支付 [執行 Python 腳本](execute-python-script.md)中預先安裝模組清單的額外注意事項。 只匯入預先安裝的模組。 請勿在此腳本中安裝額外的封裝（例如 "pip install xgboost"），否則讀取下資料流程模組中的模型時，將會引發錯誤。
  
本文說明如何搭配使用 **Create Python 模型** 與簡單的管線。 以下是管線的圖表：

![建立 Python 模型的圖表](./media/module/create-python-model.png)

1. 選取 [ **建立 Python 模型**]，然後編輯腳本以執行模型化或資料管理程式。 您可以根據 Azure Machine Learning 環境中的 Python 套件所包含的任何學習模組來建立模型的基礎。

> [!NOTE]
> 請特別注意腳本的範例程式碼中的批註，並確定您的腳本嚴格遵循需求，包括類別名稱、方法和方法簽章。 違規會導致例外狀況。 

   下列兩個類別貝氏貝氏機率分類分類器的範例程式碼會使用熱門的 *sklearn* 套件：

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

2. 將您剛才建立的「 **建立 Python 模型** 」模組連接到「 **定型模型** 」和「 **評分模型**」。

3. 如果您需要評估模型，請新增 [ [執行 Python 腳本](execute-python-script.md) ] 模組，並編輯 Python 腳本。

   下列腳本是範例評估程式碼：

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function MUST have two input arguments:
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

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 