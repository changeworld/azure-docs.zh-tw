---
title: 實驗追蹤和部署模型
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解如何使用 Azure Machine Learning 和/或 MLFlow，從 DSVM 追蹤並記錄實驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 943e8bd9f272f3dc8cefbfbccd326cf520497bb2
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146890"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>在 Azure Machine Learning 中追蹤實驗和部署模型

追蹤您的實驗並監視執行計量，以強化模型建立程序。 在本文中，您將瞭解如何使用 [MLflow](https://mlflow.org/) API 將記錄程式碼新增至訓練腳本，並在 Azure Machine Learning 中追蹤實驗。

下圖說明使用 MLflow 追蹤時，您可以追蹤實驗的執行計量，並將模型成品儲存在 Azure Machine Learning 工作區中。

![追蹤實驗](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>必要條件

* 您將需要布建 [Azure Machine Learning 工作區](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace)

## <a name="create-a-new-notebook"></a>建立新的 Notebook

Azure Machine Learning 和 MLFlow SDK 會預先安裝在資料科學 VM 上，並可在**azureml_py36_ \* ** conda 環境中存取。 在 Jupyterlab 中，按一下啟動器，然後選取下列核心：

![核心選取](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>設定工作區

移至 [Azure 入口網站](https://portal.azure.com) ，然後選取您在必要條件中布建的工作區。 您會看到 __下載 config.js的__ (請參閱下文) 下載設定，並確保其儲存在 DSVM 的工作目錄中。

![取得設定檔](./media/how-to-track-experiments/experiment-tracking-2.png)

此設定包含工作區名稱、訂用帳戶等的資訊，這表示您不需要硬編碼這些參數。

## <a name="track-dsvm-runs"></a>追蹤 DSVM 執行

將下列程式碼新增至您的筆記本 (或編寫) 的腳本，以設定 AzureML 工作區物件。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
追蹤 URI 的有效時間上限為一小時或更短。 如果您在某段閒置時間之後重新啟動指令碼，請使用 get_mlflow_tracking_uri API 來取得新的 URI。

### <a name="load-the-data"></a>載入資料

此範例會使用糖尿病資料集，這是 scikit-learn 隨附的知名小型資料集。 此儲存格會載入資料集，並將其分割成隨機定型和測試集。

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>新增追蹤

使用 Azure Machine Learning SDK 新增實驗定型，並將保存的模型上傳至實驗執行記錄中。 下列程式碼會新增記錄，並將模型檔案上傳至實驗執行。 模型也會在 Azure Machine Learning 模型登錄中註冊。

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>在 Azure Machine Learning 中執行 View

您可以在 [Azure Machine Learning Studio](https://ml.azure.com)中查看實驗執行。 按一下左側功能表中的 [ __實驗__ ]，然後選取 [experiment_with_mlflow] (或者，如果您決定在上述程式碼片段中以不同的方式命名您的實驗，請按一下 [使用的名稱) ：

![選取實驗](./media/how-to-track-experiments/mlflow-experiments.png)

您應該會看到 (MSE) 記錄的 Mean 平方錯誤：

![Mse](./media/how-to-track-experiments/mlflow-experiments-2.png)

如果您按一下 [執行]，將會在__輸出 + 記錄__檔中看到其他詳細資料和序列化模型

## <a name="deploy-model-in-azure-machine-learning"></a>在 Azure Machine Learning 中部署模型

在本節中，我們將概述如何將 DSVM 上定型的模型部署至 Azure Machine Learning。

### <a name="step-1-create-inference-compute"></a>步驟1：建立推斷計算

在 [AzureML Studio](https://ml.azure.com) 的左側功能表上，依序按一下 [ __計算__ ] 和 [ __推斷__ 叢集] 索引標籤。接著，按一下 [ __+ 新增__ ]，如下所示：

![建立推斷計算](./media/how-to-track-experiments/mlflow-experiments-6.png)

在 [ __新增推斷__ 叢集] 窗格中，填入詳細資料：

* 計算名稱
* Kubernetes 服務-選取 [建立新的]
* 選取區域
* 基於本教學課程的目的，請選取 VM 大小 (，Standard_D3_v2 的預設值就已足夠) 
* 叢集目的-選取 __開發/測試__
* 節點數目應等於 __1__
* 網路設定-基本

接著，按一下 [ __建立__]。

![計算詳細資料](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>步驟2：部署無程式碼推斷服務

當我們在程式碼中使用我們將 `register_model` 架構指定為 sklearn 時，註冊模型。 Azure Machine Learning 不支援下列架構的程式碼部署：

* scikit-learn
* Tensorflow SaveModel 格式
* ONNX 模型格式

無程式碼部署表示您可以直接從模型成品進行部署，而不需要指定任何特定的評分腳本。

若要部署糖尿病模型，請移至 [Azure Machine Learning Studio](https://ml.azure.com) 中的左側功能表，然後選取 [ __模型__]。 接著，按一下 [已註冊的 diabetes_model：

![選取模型](./media/how-to-track-experiments/mlflow-experiments-3.png)

接著，按一下 [模型詳細資料] 窗格中的 [ __部署__ ] 按鈕：

![部署](./media/how-to-track-experiments/mlflow-experiments-4.png)

我們會將模型部署至推斷叢集 (Azure Kubernetes Service 我們在步驟1中建立的) 。 藉由提供服務的名稱，以及在步驟 1) 中建立 (AKS 計算叢集的名稱，填寫下列詳細資料。 此外，我們也建議您將 __CPU 保留容量__ 增加為 1 (從 0.1) ，並將 __記憶體保留容量__ 增加至 0.5) 的 1 (-您可以按一下 [ __Advanced__ ] 並填入詳細資料，來完成這項作業。 然後按一下 [ __部署__]。

![部署詳細資料](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>步驟3：使用

成功部署模型時，您應該會看到下列 (若要前往此頁面，請按一下左側功能表中的端點 > 然後按一下已部署服務的名稱) ：

![取用模型](./media/how-to-track-experiments/mlflow-experiments-8.png)

您應該會注意到，部署狀態從 __轉換__ 到 __狀況良好__。 此外，此詳細資料區段提供 REST 端點和 Swagger Url，可讓應用程式開發人員用來將您的 ML 模型整合至其應用程式。

您可以使用 [Postman](https://www.postman.com/)來測試端點，也可以使用 AzureML SDK：

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>步驟4：清除

您應該刪除您在步驟1中建立的推斷計算，如此就不會產生進行中的計算費用。 在 Azure Machine Learning Studio 的左側功能表上，按一下 [計算 > 推斷叢集]，> 選取 [計算] > [刪除]。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [在 AzureML 中部署模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)
