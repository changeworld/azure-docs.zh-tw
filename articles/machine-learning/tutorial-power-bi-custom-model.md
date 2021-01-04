---
title: 教學課程：使用 Notebook 建立預測模型 (第 1 部，共 2 部)
titleSuffix: Azure Machine Learning
description: 了解如何在 Jupyter Notebook 中使用程式碼建立和部署機器學習模型，以使用該模型來預測 Microsoft Power BI 的結果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370631"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>教學課程：Power BI 整合 - 使用 Notebook 建立預測模型 (第 1 部，共 2 部)

在本教學課程的第一部中，您會使用 Jupyter Notebook 中的程式碼來訓練和部署預測性機器學習模型。 在第 2 部中，您將使用模型來預測 Microsoft Power BI 中的結果。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立 Jupyter Notebook
> * 建立 Azure Machine Learning 計算執行個體
> * 使用 scikit-learn 訓練迴歸模型
> * 將模型部署到即時評分端點

有三種不同的方式可用來建立及部署您將在 Power BI 中使用的模型。  本文將說明選項 A：使用 Notebook 來訓練和部署模型。  此選項將說明程式碼優先撰寫體驗 (使用 Azure Machine Learning 工作室中裝載的 Jupyter Notebook)。 

您也可以改為使用：

* [選項 B：使用設計工具來訓練和部署模型](tutorial-power-bi-designer-model.md) - 使用設計工具的低程式碼撰寫體驗 (拖放使用者介面)。
* [選項 C：使用自動化 ML 訓練和部署模型](tutorial-power-bi-automated-model.md) - 無程式碼撰寫體驗，可將資料準備和模型訓練完全自動化。


## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 ([可使用免費試用版](https://aka.ms/AMLFree))。 
- Azure Machine Learning 工作區。 如果您還沒有工作區，請遵循[如何建立 Azure Machine Learning 工作區](./how-to-manage-workspace.md#create-a-workspace)。
- Python 語言和機器學習工作流程的簡介知識。

## <a name="create-a-notebook-and-compute"></a>建立筆記本和計算

在 [Azure Machine Learning 工作室](https://ml.azure.com)首頁中，依序選取 [新建] 及 [筆記本]：

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="顯示如何建立筆記本的螢幕擷取畫面":::
 
您會看到 [建立新檔案] 的對話方塊，請輸入：

1. 您筆記本的檔案名稱 (例如 `my_model_notebook`)
1. 將 [檔案類型] 變更為 [筆記本]

選取 [建立]。 接下來，您必須建立一些計算，並將其附加到您的筆記本，以執行程式碼資料格。 若要執行此動作，請選取筆記本頂端的加號圖示：

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="顯示如何建立計算執行個體的螢幕擷取畫面":::

接下來，在 [建立計算執行個體] 頁面上：

1. 選擇 CPU 虛擬機器大小 - 基於本教學課程的目的，**Standard_D11_v2** (兩個核心，14 GB 的 RAM) 是適當的選擇。
1. 選取 [下一步]。 
1. 在 [設定] 頁面上，提供有效的 **計算名稱** (有效的字元為大寫和小寫字母、數字和 - 字元)。
1. 選取 [建立]。

在筆記本上，您可能會注意到 [計算] 旁的圓形已變成青色，這表示正在建立計算執行個體：

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="顯示計算正在建立的螢幕擷取畫面":::

> [!NOTE]
> 佈建計算可能需要大約 2-4 分鐘的時間。

佈建計算之後，您就可以使用筆記本來執行程式碼資料格。 例如，在資料格中輸入：

```python
import numpy as np

np.sin(3)
```

接著按 **Shift-Enter** (或 **Control-Enter**，或選取資料格旁邊的 [播放] 按鈕)。 您應該會看見下列輸出：

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="顯示資料格執行的螢幕擷取畫面":::

您現在已準備好建置 Machine Learning 模型了！

## <a name="build-a-model-using-scikit-learn"></a>使用 scikit-learn 建置模型

在本教學課程中，您會使用可在 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)中取得的[糖尿病資料集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)。 


### <a name="import-data"></a>匯入資料

若要匯入您的資料，請將下列程式碼複製並貼到您筆記本中的新 **程式碼資料格**：

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df` Pandas 資料框架包含 10 個基準輸入變數 (例如年齡、性別、身體質量指數、平均血壓和六種血清測量值)。 `y_df` Pandas 資料框架是目標變數，其包含基準日一年後的疾病進展量化測量。 總共有 442 筆記錄。

### <a name="train-model"></a>定型模型

在您的筆記本中建立新的 **程式碼資料格** 並複製並貼上以下的程式碼片段，其會使用 Python 的 pickle 格式來建構脊迴歸模型，並將模型序列化：

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>註冊模型

除了模型檔案本身的內容之外，您的已註冊模型也會儲存模型中繼資料 (模型描述、標記和架構資訊)，這些資料有助於您管理及部署工作區中的模型。 例如，您可以使用標記將模型分類，並在列出工作區中的模型時套用篩選。 此外，使用 scikit-learn 架構來標記此模型會簡化將其部署為 Web 服務的過程，如我們稍後所見。

將下列程式碼複製並貼到您筆記本中的新 **程式碼資料格**：

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

在 Azure Machine Learning 工作室中，您也可以從左側功能表瀏覽至 [端點]，以檢視模型：

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="顯示模型的螢幕擷取畫面":::

### <a name="define-the-scoring-script"></a>定義評分指令碼

當部署要整合到 Microsoft Power BI 的模型時，您必須定義 Python「評分指令碼」和自訂環境。 評分指令碼包含兩個函式：

- `init()` - 此函式會在服務啟動後執行。 此函式會載入模型 (請注意，模型會從模型登錄中自動下載) 並將其還原序列化。
- `run(data)` - 向服務進行呼叫時，此函式便會執行，其中包含一些需要評分的輸入資料。 

>[!NOTE]
> 我們會使用 Python 裝飾項目來定義輸入和輸出資料的結構描述，這對於 Microsoft Power BI 整合來說很重要。

將下列程式碼複製並貼到您筆記本中的新 **程式碼資料格**。 下面程式碼片段具有資料格魔術 (cell magic)，會將程式碼寫入名為 score.py 的檔案。

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>定義自訂環境

接下來，我們需要定義要對模型進行評分的環境 - 我們必須在此環境中定義上面定義的評分指令碼 (score.py) 所需的 Python 套件，例如 Pandas、scikit-learn 等等。

若要定義環境，請將下列程式碼複製並貼到您筆記本中的新 **程式碼資料格**：

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>部署模型

若要部署模型，請將下列程式碼複製並貼到您筆記本中的新 **程式碼資料格**：

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> 部署服務需要約 2-4 分鐘。

您應該會看到服務成功部署後的下列輸出：

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

在 Azure Machine Learning 工作室中，您也可以從左側功能表瀏覽至 [端點]，以檢視服務：

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="顯示端點的螢幕擷取畫面":::

建議您測試 webservice 以確保其如預期般運作。 選取 Azure Machine Learning 工作室左側功能表中的 [筆記本]，往回瀏覽到您的筆記本。 將下列程式碼複製並貼到您筆記本中的新 **程式碼資料格**，以測試服務：

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

輸出應該會類似下列 json 結構：`{'predict': [[205.59], [68.84]]}`。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建置和部署模型，使其可供 Microsoft Power BI 使用。 在下一部中，您將了解如何從 Power BI 報表使用此模型。

> [!div class="nextstepaction"]
> [教學課程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
