---
title: 將筆記本程式碼轉換成 Python 指令碼
titleSuffix: Azure Machine Learning
description: 使用 MLOpsPython 程式碼範本，將您的機器學習實驗筆記本轉換成生產環境可用的程式碼。 然後，您可以測試、部署和自動化該程式碼。
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3a6ce5860704e6fd16b79fc253650dd45ec743e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852611"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>教學課程：將 ML 實驗轉換為生產環境的 Python 程式碼

在本教學課程中，您將了解如何將 Juptyer 筆記本轉換成 Python 指令碼，以使用 MLOpsPython 程式碼範本和 Azure Machine Learning 讓其便於進行測試和自動化。 一般來說，此程序會用來從 Juptyer 筆記本取得實驗/訓練程式碼，並將其轉換成 Python 指令碼。 然後這些指令碼就可以用來在您的生產環境中進行測試和 CI/CD 自動化。 

機器學習專案需要實驗，其中的假設會透過敏捷式工具 (例如 Jupyter Notebook) 使用實際資料集進行測試。 模型準備好用於生產後，應該將模型程式碼放入實際執行程式碼存放庫。 在某些情況下，必須將模型程式碼轉換成 Python 指令碼，才能放入實際執行程式碼存放庫。 本教學課程涵蓋如何將實驗程式碼匯出至 Python 指令碼的建議方法。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 清除非必要的程式碼
> * 將 Jupyter Notebook 程式碼重構為函式
> * 建立相關工作的 Python 指令碼
> * 建立單元測試

## <a name="prerequisites"></a>Prerequisites

- 產生 [MLOpsPython 範本](https://github.com/microsoft/MLOpsPython/generate)並使用 `experimentation/Diabetes Ridge Regression Training.ipynb` 和 `experimentation/Diabetes Ridge Regression Scoring.ipynb` Notebook。 這些 Notebook 會作為從實驗轉換到生產的範例。 您可以在 [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) 找到這些 Notebook。
- 安裝 `nbconvert`。 僅依照[安裝](https://nbconvert.readthedocs.io/en/latest/install.html)頁面上__安裝 nbconvert__ 一節中的安裝指示操作。

## <a name="remove-all-nonessential-code"></a>移除所有非必要的程式碼

在實驗期間撰寫的某些程式碼僅供探索之用。 因此，將實驗性程式碼轉換成實際執行程式碼的第一個步驟，就是移除此非必要的程式碼。 移除非必要的程式碼也會讓程式碼更容易維護。 在本節中，您將從 `experimentation/Diabetes Ridge Regression Training.ipynb` Notebook 中移除程式碼。 用於列印 `X` 和 `y` 圖形的陳述式，以及用於呼叫 `features.describe` 的資料格，只是用於資料探索並可加以移除。 移除非必要的程式碼之後，`experimentation/Diabetes Ridge Regression Training.ipynb` 應如下列程式碼所示 (不含 Markdown)：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>將程式碼重構為函式

第二，Jupyter 程式碼必須重構為函式。 將程式碼重構為函式可簡化單元測試，並使程式碼更容易維護。 在本節中，您將會重構：

- 糖尿病脊迴歸訓練 Notebook (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- 糖尿病脊迴歸評分 Notebook (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>將糖尿病脊迴歸訓練 Notebook 重構為函式

在 `experimentation/Diabetes Ridge Regression Training.ipynb` 中，完成下列步驟：

1. 建立名為 `split_data` 的函式，將資料框架分割成測試和定型資料。 函式應該採用資料框架 `df` 作做為參數，並傳回包含索引鍵 `train` 和 `test`的字典。

    將*將資料分割成訓練和驗證集*標題下的程式碼移到 `split_data` 函式並加以修改，以傳回 `data` 物件。

1. 建立名為 `train_model` 的函式，此函式會採用 `data` 和 `args` 參數並傳回定型的模型。

    將標題*訓練集中的訓練模型*下的程式碼移到 `train_model` 函式並加以修改，以傳回 `reg_model` 物件。 移除 `args` 字典，這些值將來自 `args` 參數。

1. 建立名為 `get_model_metrics` 的函式，此函式會採用 `reg_model` 和 `data` 參數，然後評估模型，並傳回已定型模型的計量字典。

    將*訓練集中的驗證模型*標題下的程式碼移到 `get_model_metrics` 函式並加以修改，以傳回 `metrics` 物件。

這三個函式應該如下所示：

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

在 `experimentation/Diabetes Ridge Regression Training.ipynb` 中，完成下列步驟：

1. 建立稱為 `main` 的新函式，此函式不會採用任何參數，也不會傳回任何內容。
1. 將「載入資料」標題下的程式碼移到 `main` 函式中。
1. 將新寫入函式的叫用新增至 `main` 函式：
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. 將「儲存模型」標題下的程式碼移到 `main` 函式中。

`main` 函式應如下列程式碼所示：

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

在這個階段中，除了第一個資料格中的 import 陳述式以外，不在函式中的筆記本應該不會留下任何程式碼。

新增會呼叫 `main` 函式的陳述式。

```python
main()
```

重構之後，`experimentation/Diabetes Ridge Regression Training.ipynb` 應如下列程式碼所示 (不含 Markdown)：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>將糖尿病脊迴歸評分 Notebook 重構為函式

在 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 中，完成下列步驟：

1. 建立稱為 `init` 的新函式，此函式不會採用任何參數，也不會傳回任何內容。
1. 將「載入模型」標題下的程式碼複製到 `init` 函式中。

`init` 函式應如下列程式碼所示：

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

建立 `init` 函式之後，將「載入模型」標題下的所有程式碼取代為對 `init` 的單一呼叫，如下所示：

```python
init()
```

在 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 中，完成下列步驟：

1. 建立稱為 `run` 的新函式，此函式會採用 `raw_data` 和 `request_headers` 作為參數，並傳回結果的字典，如下所示：

    ```python
    {"result": result.tolist()}
    ```

1. 將「準備資料」和「評分資料」標題下的程式碼複製到 `run` 函式中。

    `run` 函式應如下列程式碼所示 (請記得移除可設定 `raw_data` 和 `request_headers` 變數的陳述式，其稍後會在呼叫 `run` 函式時使用)：

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

建立 `run` 函式之後，請使用下列程式碼取代「準備資料」和「評分資料」標題下的所有程式碼：

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

先前的程式碼會設定 `raw_data` 和 `request_header`變數、呼叫具有 `raw_data` 和 `request_header` 的 `run` 函式，然後列印預測。

重構之後，`experimentation/Diabetes Ridge Regression Scoring.ipynb` 應如下列程式碼所示 (不含 Markdown)：

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>合併 Python 檔案中的相關函式

第三，必須將相關函式合併到 Python 檔案中，以便更妥善地協助重複使用程式碼。 在本節中，您將會為下列 Notebook 建立 Python 檔案：

- 糖尿病脊迴歸訓練 Notebook (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- 糖尿病脊迴歸評分 Notebook (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>建立糖尿病脊迴歸訓練 Notebook 的 Python 檔案

在命令提示字元中執行下列陳述式，以將 Notebook 轉換成可執行的指令碼，其會使用 `nbconvert` 套件和 `experimentation/Diabetes Ridge Regression Training.ipynb` 的路徑：

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

在筆記本轉換成 `train.py` 之後，移除所有不需要的註解。 使用類似下列程式碼的條件叫，取代檔案結尾處 `main()` 的呼叫：

```python
if __name__ == '__main__':
    main()
```

您的 `train.py` 檔案應如下列程式碼所示：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

您現在可以執行 `python train.py`，從終端機叫用 `train.py`。
來自 `train.py` 的函式也可以從其他檔案呼叫。

在 MLOpsPython 存放庫的 `diabetes_regression/training` 目錄中找到的 `train_aml.py` 檔案，會呼叫 Azure Machine Learning 實驗執行內容的 `train.py` 中定義的函式。 函式也可以在單元測試中呼叫，本指南稍後會加以討論。

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>建立糖尿病脊迴歸評分 Notebook 的 Python 檔案

在命令提示字元中執行下列陳述式，以將 Notebook 轉換成可執行的指令碼，其會使用 `nbconvert` 套件和 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 的路徑：

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

在筆記本轉換成 `score.py` 之後，移除所有不需要的註解。 您的 `score.py` 檔案應如下列程式碼所示：

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

`model` 變數必須是全域的，才能在整個指令碼中顯示。 在 `init` 函式的開頭新增下列陳述式：

```python
global model
```

新增前一個陳述式之後，`init` 函式應如下列程式碼所示：

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>建立每個 Python 檔案的單元測試

第四，為您的 Python 函式建立單元測試。 單元測試會保護程式碼免於功能迴歸，以便輕鬆維護。 在本節中，您將為 `train.py` 中的函式建立單元測試。

`train.py` 包含多個函式，但在本教學課程中，我們只會使用 Pytest 架構來為 `train_model` 函式建立單一單元測試。 Pytest 不是唯一的 Python 單元測試架構，但是最常使用的架構之一。 如需詳細資訊，請瀏覽 [Pytest](https://pytest.org)。

單元測試通常包含三個主要動作：

- 排列物件 - 建立和設定必要的物件
- 對物件採取動作
- 判斷提示預期的結果

單元測試將使用一些硬式編碼的資料和引數來呼叫 `train_model`，並使用產生的定型模型進行預測，同時將該預測與預期的值進行比較，以驗證 `train_model` 是否如預期般運作。

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>後續步驟

既然您已了解如何從實驗轉換成實際執行的程式碼，請參閱下列連結以取得詳細資訊和後續步驟：

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md):建置 CI/CD 管線，使用 Azure Pipelines 和 Azure Machine Learning 來定型、評估和部署您自己的模型
+ [監視 Azure ML 實驗的執行和計量](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [從 ML Web 服務端點監視及收集資料](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
