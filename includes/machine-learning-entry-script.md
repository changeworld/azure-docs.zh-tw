---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325301"
---
輸入指令碼會接收提交給已部署 Web 服務的資料，並將其傳遞給模型。 然後，輸入指令碼會採用模型傳回的回應，並將該回應傳回至用戶端。 *腳本是您的模型特有的* 。 它必須瞭解模型預期並傳回的資料。

您需要在輸入腳本中完成的兩個事項如下：

1. 使用稱為) 的函數載入您的模型 (`init()`
1. 使用稱為) 的函式在輸入資料上執行模型 (`run()`

讓我們詳細解說這些步驟。

### <a name="writing-init"></a>寫入 init ( # A1 

#### <a name="loading-a-registered-model"></a>載入已註冊的模型

您已註冊的模型會儲存在名為的環境變數所指向的路徑 `AZUREML_MODEL_DIR` 。 如需確切目錄結構的詳細資訊，請參閱[在您的輸入腳本中找出模型](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)檔案

#### <a name="loading-a-local-model"></a>載入本機模型

如果您選擇不註冊您的模型，並將模型當作來原始目錄的一部分來進行讀取，您可以像在本機一樣讀取它，方法是將相對於您評分腳本的路徑傳遞給模型。 例如，如果您的目錄結構如下：

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

您可以使用下列 Python 程式碼來載入模型：

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>撰寫執行 ( # A1

`run()` 每次您的模型收到評分要求時都會執行，而且預期要求的主體必須是具有下列結構的 JSON 檔：

```json
{
    "data": <model-specific-data-structure>
}

```

的輸入 `run()` 是 Python 字串，其中包含 "data" 索引鍵後面的任何內容。

下列範例示範如何載入已註冊的 scikit-learn 學習模型，並使用 numpy 資料對它評分：

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

如需更高階的範例，包括自動 Swagger 架構產生和二進位 (即影像) 資料，請閱讀 [有關撰寫輸入腳本的文章](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)