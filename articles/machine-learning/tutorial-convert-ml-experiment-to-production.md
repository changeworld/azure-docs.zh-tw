---
title: 將機器學習實驗程式碼轉換為實際執行程式碼
titleSuffix: Azure Machine Learning
description: 了解如何使用 MLOpsPython 程式碼範本，將機器學習實驗性程式碼轉換為實際執行程式碼。
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: b929dd8979ecf587872092af2fa44e5ae03626d1
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472480"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>教學課程：將 ML 實驗性程式碼轉換為實際執行程式碼

機器學習專案需要實驗，其中的假設會透過敏捷式工具 (例如 Jupyter Notebook) 使用實際資料集進行測試。 模型準備好用於生產後，應該將模型程式碼放入實際執行程式碼存放庫。 在某些情況下，必須將模型程式碼轉換成 Python 指令碼，才能放入實際執行程式碼存放庫。 本教學課程涵蓋如何將實驗程式碼匯出至 Python 指令碼的建議方法。  

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 清除非必要的程式碼
> * 將 Jupyter Notebook 程式碼重構為函式
> * 建立相關工作的 Python 指令碼
> * 建立單元測試

## <a name="prerequisites"></a>Prerequisites

- 產生 [MLOpsPython 範本](https://github.com/microsoft/MLOpsPython/generate)並使用 `experimentation/Diabetes Ridge Regression Training.ipynb` 和 `experimentation/Diabetes Ridge Regression Scoring.ipynb` Notebook。 這些 Notebook 會作為從實驗轉換到生產的範例。
- 安裝 nbconvert。 僅依照[安裝](https://nbconvert.readthedocs.io/en/latest/install.html)頁面上__安裝 nbconvert__ 一節中的安裝指示操作。

## <a name="remove-all-nonessential-code"></a>移除所有非必要的程式碼

在實驗期間撰寫的某些程式碼僅供探索之用。 因此，將實驗性程式碼轉換成實際執行程式碼的第一個步驟，就是移除此非必要的程式碼。 移除非必要的程式碼也會讓程式碼更容易維護。 在本節中，您將從 `experimentation/Diabetes Ridge Regression Training.ipynb` Notebook 中移除程式碼。 用於列印 `X` 和 `y` 圖形的陳述式，以及用於呼叫 `features.describe` 的資料格，只是用於資料探索並可加以移除。 移除非必要的程式碼之後，`experimentation/Diabetes Ridge Regression Training.ipynb` 應如下列程式碼所示 (不含 Markdown)：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
 
X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>將程式碼重構為函式

第二，Jupyter 程式碼必須重構為函式。 將程式碼重構為函式可簡化單元測試，並使程式碼更容易維護。 在本節中，您將會重構：
- 糖尿病脊迴歸訓練 Notebook (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- 糖尿病脊迴歸評分 Notebook (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>將糖尿病脊迴歸訓練 Notebook 重構為函式
在 `experimentation/Diabetes Ridge Regression Training.ipynb` 中，完成下列步驟：

1. 建立名為 `train_model` 的函式，此函式會採用 `data` 和 `alpha` 參數並傳回模型。 
1. 將「在訓練集上定型模型」和「在驗證集上驗證模型」標題下的程式碼複製到 `train_model` 函式中。

`train_model` 函式應如下列程式碼所示：

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

建立 `train_model` 函式之後，請使用下列陳述式取代「在訓練集上定型模型」和「在驗證集上驗證模型」標題下的程式碼：

```python
reg = train_model(data, alpha)
```

前一個陳述式會呼叫用於傳遞 `data` 和 `alpha` 參數的 `train_model` 函式，並傳回模型。

在 `experimentation/Diabetes Ridge Regression Training.ipynb` 中，完成下列步驟：

1. 建立稱為 `main` 的新函式，此函式不會採用任何參數，也不會傳回任何內容。
1. 將「載入資料」、「將資料分割成訓練集和驗證集」和「儲存模型」標題下的程式碼複製到 `main` 函式中。
1. 將新建立的 `train_model` 呼叫複製到 `main` 函式中。

`main` 函式應如下列程式碼所示：

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

建立 `main` 函式之後，請使用下列陳述式取代「載入資料」、「將資料分割成訓練集和驗證集」和「儲存模型」標題下的所有程式碼，以及新建立的 `train_model` 呼叫：

```python
main()
```

重構之後，`experimentation/Diabetes Ridge Regression Training.ipynb` 應如下列程式碼所示 (不含 Markdown)：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

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
在命令提示字元中執行下列陳述式，以將 Notebook 轉換成可執行的指令碼，其會使用 nbconvert 套件和 `experimentation/Diabetes Ridge Regression Training.ipynb` 的路徑：

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

在 Notebook 轉換成 `train.py` 之後，移除所有註解。 您的 `train.py` 檔案應如下列程式碼所示：

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

在 MLOpsPython 存放庫的 `diabetes_regression/training` 目錄中找到的 `train.py` 檔案可支援命令列引數 (也就是 `build_id`、`model_name` 和 `alpha`)。 您可將命令列引數的支援新增至 `train.py` 檔案，以支援動態模型名稱和 `alpha` 值，但程式碼不一定要執行成功。

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>建立糖尿病脊迴歸評分 Notebook 的 Python 檔案
在命令提示字元中執行下列陳述式，以將 Notebook 轉換成可執行的指令碼，其會使用 nbconvert 套件和 `experimentation/Diabetes Ridge Regression Scoring.ipynb` 的路徑：

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

在 Notebook 轉換成 `score.py` 之後，移除所有註解。 您的 `score.py` 檔案應如下列程式碼所示：

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

`train_model` 函式需要修改，才能具現化全域變數模型，使其可在整個指令碼中顯示。 在 `init` 函式的開頭新增下列陳述式：

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
第四，必須為每個 Python 檔案建立單元測試，讓程式碼更強固且更容易維護。 在本節中，您將為 `train.py` 中的其中一個函式建立單元測試。

`train.py` 包含兩個函式：`train_model` 和 `main`。 每個函式都需要單元測試，但在本教學課程中，我們只會使用 Pytest 架構來為 `train_model` 函式建立單一單元測試。  Pytest 不是唯一的 Python 單元測試架構，但是最常使用的架構之一。 如需詳細資訊，請瀏覽 [Pytest](https://pytest.org)。

單元測試通常包含三個主要動作：
- 排列物件 - 建立和設定必要的物件
- 對物件採取動作
- 判斷提示預期的結果

`train_model` 的常見條件就是傳遞 `data` 和 `alpha` 值時。 預期的結果是應該呼叫 `Ridge.train` 和 `Ridge.predict` 函式。 由於機器學習訓練方法通常不會快速執行，因此會模擬 `Ridge.train` 呼叫。 因為 `Ridge.train` 的傳回值是模擬物件，所以我們也會模擬 `Ridge.predict`。 可供 `train_model` 以使用模擬所呼叫 `Ridge.train` 和 `Ridge.predict` 函式的預期結果，測試 `data` 傳遞和 `alpha` 值的單元測試，而 Pytest 架構應如下列程式碼所示：

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch(Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>使用您自己的模型搭配 MLOpsPython 程式碼範本
如果您已遵循本指南中的步驟操作，您會有一組指令碼，其與 MLOpsPython 存放庫中可用的定型/評分/測試指令碼相互關聯。  根據以上所述的結構，下列步驟將逐步解說針對您自己的機器學習專案使用這些檔案所需的動作：  

1.  遵循使用者入門指南
2.  取代訓練程式碼
3.  取代評分程式碼
4.  更新評估程式碼

### <a name="follow-the-getting-started-guide"></a>遵循使用者入門指南
必須遵循快速入門手冊，支援的基礎結構和管線才能執行 MLOpsPython。  我們建議在放入自己的程式碼之前，先依現狀部署 MLOpsPython 程式碼，以確保結構和管線運作正常。  讓自己熟悉存放庫的程式碼結構也很有用。

### <a name="replace-training-code"></a>取代訓練程式碼
必須取代用來定型模型的程式碼，並移除或取代對應的單元測試，讓解決方案能搭配您自己的程式碼運作。  請特別遵循下列步驟：

1. 取代 `diabetes_regression\training\train.py`。 此指令碼會在本機或 Azure ML 計算上定型您的模型。
1. 移除或取代在 `tests/unit/code_test.py` 中找到的訓練單元測試

### <a name="replace-score-code"></a>取代評分程式碼
為了讓模型提供即時推斷功能，必須取代評分代碼。 MLOpsPython 範本會使用評分程式碼來部署模型，以對 ACI、AKS 或 Web 應用程式進行即時評分。  如果您想要保留評分，請取代 `diabetes_regression/scoring/score.py`。

### <a name="update-evaluation-code"></a>更新評估程式碼
MLOpsPython 範本會使用 evaluate_model 指令碼，以根據均方差來比較新定型模型和目前生產模型的效能。 如果新定型模型的效能優於目前的生產模型，則管線會繼續進行。 否則，管線就會停止。 若要繼續評估，請以您想要的計量取代 `diabetes_regression/evaluate/evaluate_model.py` 中的所有 `mse` 執行個體。 

若要清除評估，請將 `.pipelines\diabetes_regression-variables` 中的 DevOps 管線變數 `RUN_EVALUATION` 設定為 `false`。

## <a name="next-steps"></a>後續步驟

既然您已了解如何從實驗轉換成實際執行程式碼，請使用下列連結來了解如何監視已部署為 Web 服務的實驗執行和模型：

> [!div class="nextstepaction"]
> [監視 Azure ML 實驗執行和計量](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [從 ML Web 服務端點監視和收集資料](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
