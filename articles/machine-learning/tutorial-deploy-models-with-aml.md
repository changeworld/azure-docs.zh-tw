---
title: 映像分類教學課程：部署模型
titleSuffix: Azure Machine Learning
description: 本教學課程是兩部分系列的第二部分，將說明如何使用 Azure Machine Learning 搭配 scikit-learn 在 Python Jupyter Notebook 中部署影像分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: 680b6ec17b65cd9452dd3bd5c0c470e395688cb8
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "86025670"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>教學課程：在 Azure 容器執行個體中部署映像分類模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本教學課程是**兩部分教學課程系列的第二部分**。 在[先前的教學課程](tutorial-train-models-with-aml.md)中，您定型了機器學習模型，並在您的雲端工作區內註冊模型。  現在，您已準備好將模型部署為 Web 服務。 Web 服務是映像，在此案例中為 Docker 映像。 它封裝了評分邏輯和模型本身。 

在本教學課程部分中，您將使用 Azure Machine Learning 來進行下列工作：

> [!div class="checklist"]
> * 設定您的測試環境。
> * 從您的工作區擷取模型。
> * 將模型部署到容器執行個體。
> * 測試已部署的模型。

容器執行個體是很適合用來測試和了解工作流程的解決方案。 如需可調整的生產環境部署，請考慮使用 Azure Kubernetes Service。 如需詳細資訊，請參閱[部署方式和位置](how-to-deploy-and-where.md)。

>[!NOTE]
> 此文章中的程式碼已經過 Azure Machine Learning SDK 1.0.83 版的測試。

## <a name="prerequisites"></a>Prerequisites

若要執行筆記本，請先完成模型訓練，相關內容位於[教學課程 (第 1 部分)：將映像分類模型定型](tutorial-train-models-with-aml.md)。   然後在您複製的 tutorials/image-classification-mnist-data  資料夾中開啟 img-classification-part2-deploy.ipynb  筆記本。

如果您想要在自己的[本機環境](how-to-configure-environment.md#local)中使用此教學課程，也可以在 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 上取得。  請確定您已在環境中安裝 `matplotlib` 和 `scikit-learn`。 

> [!Important]
> 本文的其餘部分包含與您在 Notebook 中所見相同的內容。  
>
> 如果您想要在執行程式碼時進行閱讀，請立即切換到 Jupyter Notebook。
> 若要在 Notebook 中執行單一程式碼資料格，請按一下程式碼資料格，然後按 **Shift+Enter**。 或者，從頂端工具列中選擇 [全部執行]  ，以執行整個 Notebook。

## <a name="set-up-the-environment"></a><a name="start"></a>設定環境

著手開始設定測試環境。

### <a name="import-packages"></a>匯入套件

匯入本教學課程所需的 Python 套件。


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>作為 Web 服務部署

將模型部署為 ACI 中裝載的 Web 服務。 

若要建置正確的 ACI 環境，請提供下列項目：
* 示範如何使用模型的評分指令碼
* 用於建置 ACI 的設定檔
* 您之前定型的模型

### <a name="create-scoring-script"></a>建立評分指令碼

建立稱為 score.py 的評分指令碼。Web 服務呼叫會使用此指令碼示範如何使用模型。

您必須在評分指令碼中包含兩個必要函式：
* `init()` 函式，通常會將模型載入全域物件。 此函式只會在 Docker 容器啟動時執行一次。 

* `run(input_data)` 函式會使用模型依據輸入資料預測值。 run 的輸入和輸出通常會使用 JSON 進行序列化及還原序列化，但也支援其他格式。

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

### <a name="create-configuration-file"></a>建立組態檔

建立部署設定檔，並指定您 ACI 容器所需要的 CPU 數量及 RAM GB 數。 雖然這取決於您的模型，但預設的 1 核心及 1 GB RAM 通常對許多模型來說便已足夠。 若您稍後需要更多，您需要重新建立映像並重新部署服務。


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>在 ACI 中部署
預估完成時間：**2 到 5 分鐘**

設定映像並部署。 下列程式碼會執行這些步驟：

1. 使用在訓練期間儲存的環境 (`tutorial-env`)，建立包含模型所需相依性的環境物件。
1. 使用下列項目來建立將模型部署為 Web 服務時所需的推斷組態：
   * 評分檔案 (`score.py`)
   * 在上一個步驟中建立的環境物件
1. 將模型部署到 ACI 容器。
1. 取得 Web 服務 HTTP 端點。


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

取得評分 Web 服務的 HTTP 端點，該端點會接受 REST 用戶端呼叫。 此端點可和任何想要測試 Web 服務，或想要整合應用程式與服務的人共用。


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>測試模型


### <a name="download-test-data"></a>下載測試資料
將測試資料下載至 **./data/** 目錄


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>載入測試資料

從定型教學課程中建立的 **./data/** 目錄載入測試資料。


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>預測測試資料

饋送測試資料集給模型，以取得預測。


下列程式碼會執行這些步驟：
1. 將資料作為 JSON 陣列傳送至 ACI 中的託管 Web 服務。 

1. 使用 SDK 的 `run` API 叫用服務。 您也可以使用 HTTP 工具 (例如 curl) 進行原始呼叫。


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>檢查混淆矩陣

產生混淆矩陣，查看測試集中有多少範例的分類正確。 請注意不正確預測中的錯誤分類值。


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

輸出會顯示混淆矩陣：

```output
[[ 960    0    1    2    1    5    6    3    1    1]
 [   0 1112    3    1    0    1    5    1   12    0]
 [   9    8  920   20   10    4   10   11   37    3]
 [   4    0   17  921    2   21    4   12   20    9]
 [   1    2    5    3  915    0   10    2    6   38]
 [  10    2    0   41   10  770   17    7   28    7]
 [   9    3    7    2    6   20  907    1    3    0]
 [   2    7   22    5    8    1    1  950    5   27]
 [  10   15    5   21   15   27    7   11  851   12]
 [   7    8    2   13   32   13    0   24   12  898]]
Overall accuracy: 0.9204
```

使用 `matplotlib`，將混淆矩陣作為圖表顯示。 在此圖表中，X 軸代表實際值，Y 軸代表預測值。 每一格中的顏色代表錯誤率。 顏色越淡，表示錯誤率越高。 例如，很多 5 都錯誤分類為 3。 因此，您會在 (5,3) 看到明亮的方格。

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![顯示混淆矩陣的圖表](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>顯示預測

使用來自測試資料的 30 個隨機影像範例來測試已部署模型。  


1. 印出傳回的預測，並將它們與輸入影像繪製在一起。 紅色字型及反轉影像 (黑色上的白色) 用於醒目提示分類錯誤的樣本。 

 因為模型的準確度很高，您可能需要執行下列程式碼數次，才能看到分類錯誤的樣本。



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

您也可以傳送原始 HTTP 要求來測試 Web 服務。


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>清除資源

若要保留資源群組及工作區，以用於其他教學課程和探索，您可以使用此 API 呼叫，只刪除容器執行個體部署：

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>後續步驟

+ 了解所有的 [Azure Machine Learning 部署選項](how-to-deploy-and-where.md)。
+ 了解如何[建立 Web 服務的用戶端](how-to-consume-web-service.md)。
+  以非同步的方式[對大量資料進行預測](how-to-use-parallel-run-step.md)。
+ [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)。
+ 請試試[自動選取演算法](tutorial-auto-train-models.md)教學課程。 
