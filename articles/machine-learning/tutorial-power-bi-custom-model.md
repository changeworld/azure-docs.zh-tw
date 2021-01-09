---
title: 教學課程：使用筆記本建立預測模型 (第 1 部分，共 2 部分)
titleSuffix: Azure Machine Learning
description: 了解如何在 Jupyter Notebook 中使用程式碼來建置和部署機器學習模型。 您可以使用模型來預測 Microsoft Power BI 中的結果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 1dfee56f90011d3c532767e136b383e4eb95c234
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814766"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-a-jupyter-notebook-part-1-of-2"></a>教學課程：Power BI 整合 - 使用 Jupyter Notebook 建立預測模型 (第 1 部分，共 2 部分)

在本教學課程的第 1 部分中，您會使用 Jupyter Notebook 中的程式碼來訓練和部署預測性機器學習模型。 在第 2 部分中，您將使用模型來預測 Microsoft Power BI 中的結果。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立 Jupyter Notebook。
> * 建立 Azure Machine Learning 計算執行個體。
> * 使用 scikit-learn 訓練迴歸模型。
> * 將模型部署到即時評分端點。

有三種方式可用來建立及部署您將在 Power BI 中使用的模型。  本文將說明「選項 A：使用筆記本來定型和部署模型。」  此選項需要程式碼撰寫經驗， 此選項會使用 Azure Machine Learning Studio 中裝載的 Jupyter Notebooks。 

但是，您可以改為使用其他選項：

* [選項 B：使用 Azure Machine Learning 設計工具定型和部署模型](tutorial-power-bi-designer-model.md)。 此選項使用拖放使用者介面，需要一些程式碼撰寫經驗。
* [選項 C：使用自動化機器學習定型和部署模型](tutorial-power-bi-automated-model.md)。 此選項無需程式碼撰寫經驗，可將資料準備和模型訓練完全自動化。


## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有訂用帳戶，可以使用[免費試用](https://aka.ms/AMLFree)。 
- Azure Machine Learning 工作區。 如果沒有工作區，請參閱[建立和管理 Azure Machine Learning 工作區](./how-to-manage-workspace.md#create-a-workspace)。
- Python 語言和機器學習工作流程的簡介知識。

## <a name="create-a-notebook-and-compute"></a>建立筆記本和計算

在 [**Azure Machine Learning Studio**](https://ml.azure.com) 首頁中，選取 [新建]  >  [筆記本]：

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="顯示如何建立筆記本的螢幕擷取畫面。":::
 
在 **建立新檔案** 頁面：

1. 為您的筆記本命名 (例如 my_model_notebook)。
1. 將 **檔案類型** 變更為 **筆記本**。
1. 選取 [建立]。 
 
接下來，若要執行程式碼資料格，請建立計算執行個體並附加至您的筆記本。 請選取筆記本頂端的加號圖示開始此作業：

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="顯示如何建立計算執行個體的螢幕擷取畫面。":::

在 **建立計算執行個體** 頁面上：

1. 選擇 CPU 虛擬機器大小。 在本教學課程中，您可以選擇具有 2 個核心和 14 GB RAM 的 **Standard_D11_v2**。
1. 選取 [下一步]  。 
1. 在 **設定** 頁面上，提供有效的 **計算名稱**。 有效的字元為大寫和小寫字母、數字和連字號 (-)。
1. 選取 [建立]。

您可能會在筆記本中注意到 **計算** 旁的圓形已變成青色。 顏色變更表示正在建立計算執行個體：

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="顯示計算正在建立的螢幕擷取畫面。":::

> [!NOTE]
> 計算執行個體可能需要 2 到 4 分鐘才能完成佈建。

佈建計算之後，您就可以使用筆記本來執行程式碼資料格。 例如，您可以在資料格中輸入下列程式碼：

```python
import numpy as np

np.sin(3)
```

接著選取 Shift + Enter (或選取 Control + Enter，或選取資料格旁邊的 [播放] 按鈕)。 您應該會看見下列輸出：

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="顯示資料格輸出的螢幕擷取畫面。":::

您現在已準備好建置 Machine Learning 模型了。

## <a name="build-a-model-by-using-scikit-learn"></a>使用 scikit-learn 建置模型

在本教學課程中，您會使用[糖尿病資料集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)。 此資料集可在 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)中取得。


### <a name="import-data"></a>匯入資料

若要匯入您的資料，請將下列程式碼複製並貼到您筆記本中的新「程式碼資料格」。

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df` Pandas 資料框架包含 10 個基準輸入變數。 這些變數包含年齡、性別、身體質量指數、平均血壓和六種血清測量值。 `y_df` Pandas 資料框架是目標變數， 並包含在基準後一年疾病進展的量化量值。 資料框架包含 442 筆記錄。

### <a name="train-the-model"></a>將模型定型

在您的筆記本中建立新的「程式碼資料格」 。 接著複製下列程式碼，並貼入資料格。 此程式碼片段會使用 Python Pickle 格式來建立一個脊迴歸模型，並將模型序列化。

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>註冊模型

除了模型檔案本身的內容之外，您的已註冊模型也會儲存中繼資料。 中繼資料包含模型描述、標籤和架構資訊。 

要在工作區中管理和部署模型時，中繼資料很有用。 例如，您可以使用標籤將模型分類，並在列出工作區中的模型時套用篩選。 此外，使用 scikit-learn 架構來標記此模型會簡化將其部署為 Web 服務的過程。

請將下列程式碼複製並貼到您筆記本中的新「程式碼資料格」。

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

您也可以在 Azure Machine Learning Studio 中檢視模型。 在左側功能表中，選取 [模型]：

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="顯示如何檢視模型的螢幕擷取畫面。":::

### <a name="define-the-scoring-script"></a>定義評分指令碼

部署要整合到 Power BI 的模型時，您必須定義 Python「評分指令碼」和自訂環境。 評分指令碼包含兩個函式：

- 當服務啟動時，就會執行 `init()` 函式。 此函式會載入模型 (模型會從模型登錄中自動下載) 並將其還原序列化。
- 當服務的呼叫包含需要計分的輸入資料時，就會執行 `run(data)` 函式。 

>[!NOTE]
> 本文使用 Python 裝飾項目來定義輸入和輸出資料的結構描述。 此設定對 Power BI 整合而言很重要。

請將下列程式碼複製並貼到您筆記本中的新「程式碼資料格」。 下列程式碼片段具有資料格魔術功能，會將程式碼寫入名為 score.py 的檔案。

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
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>定義自訂環境

接下來，定義要對模型評分的環境。 在環境中，定義計分指令碼 (score.py) 所需的 Python 套件 (例如 Pandas 和 scikit-learn)。

若要定義環境，請將下列程式碼複製並貼到您筆記本中的新「程式碼資料格」。

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

若要部署模型，請將下列程式碼複製並貼到您筆記本中的新「程式碼資料格」：

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> 部署服務可能需要 2 到 4 分鐘。

若服務部署成功，您應該會看到下列輸出：

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

您也可以在 Azure Machine Learning Studio 中檢視服務。 在左側功能表中，選取 [端點]：

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="顯示如何檢視服務的螢幕擷取畫面。":::

建議您測試 Web 服務以確保如預期般運作。 若要傳回筆記本，請在 Azure Machine Learning Studio 的左側功能表中，選取 [Notebooks]。 接著將下列程式碼複製並貼到您筆記本中的新「程式碼資料格」，以測試服務。

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

輸出應該會類似此 JSON 結構：`{'predict': [[205.59], [68.84]]}`。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建置和部署模型，使其可供 Power BI 使用。 在下一部分中，您將了解如何在 Power BI 報表使用此模型。

> [!div class="nextstepaction"]
> [教學課程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
