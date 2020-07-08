---
title: 使用 TensorBoard 將實驗視覺化
titleSuffix: Azure Machine Learning
description: 啟動 TensorBoard 以將實驗執行歷程記錄視覺化，並找出超參數微調和重新定型的可能區域。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: 317c771daca146660c62eb0cba14facc701b1b2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84430163"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>使用 TensorBoard 和 Azure Machine Learning 將實驗執行與計量視覺化
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您會了解如何使用主要 Azure Machine Learning SDK 中的 [`tensorboard` 套件](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py)，在 TensorBoard 中檢視您的實驗執行和計量。 檢查過實驗執行之後，您就可以更進一步地微調機器學習模型並加以重新定型。

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) 是一套 Web 應用程式，可用於檢查和了解您的實驗結構和效能。

使用 Azure Machine Learning 實驗來啟動 TensorBoard 的方法取決於實驗的類型：
+ 如果您的實驗原本就會輸出 TensorBoard 可取用的記錄檔，例如 PyTorch、Chainer 和 TensorFlow 實驗，您就可以從實驗的執行歷程記錄[直接啟動 TensorBoard](#direct)。 

+ 對於原本不會輸出 TensorBoard 可取用檔案的實驗 (例如 Scikit-learn 或 Azure Machine Learning 實驗)，則請使用 [`export_to_tensorboard()` 方法](#export)將執行歷程記錄匯出為 TensorBoard 記錄，再從該處啟動 TensorBoard。 

> [!TIP]
> 本文件中的資訊主要適用於想要監視模型定型程序的資料科學家和開發人員。 如果您是系統管理員，且想要從 Azure Machine Learning 監視資源使用量和事件 (例如配額、已完成的定型回合或已完成的模型部署)，則請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md)。

## <a name="prerequisites"></a>Prerequisites

* 若要啟動 TensorBoard 並檢視您的實驗執行歷程記錄，則實驗先前必須已啟用記錄，才能追蹤其計量和效能。  

* 本文件中的程式碼可在下列任一環境中執行： 

    * Azure Machine Learning 計算執行個體 - 不需要下載或安裝

        * 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。

        * 在筆記本伺服器的範例資料夾中，瀏覽至下列目錄來尋找兩個已完成和已展開的筆記本：
            * **how-to-use-azureml > training-with-deep-learning > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**

            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard.ipynb**

    * 您自己的 Juptyer 筆記本伺服器
       * [安裝 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 與 `tensorboard` 額外項目
        * [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。  
        * [建立工作區設定檔](how-to-configure-environment.md#workspace)。
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>選項 1：在 TensorBoard 中直接檢視執行歷程記錄

此選項適用於原本就會輸出 TensorBoard 可取用記錄檔的實驗，例如 PyTorch、Chainer 和 TensorFlow 實驗。 如果您的實驗不是這種情況，請改用 [`export_to_tensorboard()` 方法](#export)。

下列範例程式碼會使用 [MNIST 示範實驗](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py)，此實驗來自 TensorFlow 在遠端計算目標 (Azure Machine Learning Compute) 中的存放庫。 接下來，我們會使用 SDK 的自訂 [TensorFlow 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)來將模型定型，然後針對此 TensorFlow 實驗 (也就是原本就會輸出 TensorBoard 事件檔案的實驗) 啟動 TensorBoard。

### <a name="set-experiment-name-and-create-project-folder"></a>設定實驗名稱並建立專案資料夾

在這裡，我們會為實驗命名，並建立其資料夾。 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>下載 TensorFlow 示範實驗程式碼

TensorFlow 的存放庫有一個 MNIST 示範，其中包含廣泛的 TensorBoard 檢測設備。 我們既不會也不需要改變此示範的任何程式碼，程式碼就能與 Azure Machine Learning 搭配運作。 在下列程式碼中，我們會下載 MNIST 程式碼，並將其儲存在新建立的實驗資料夾中。

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
請注意，MNIST 程式碼檔案 (mnist_with_summaries.py) 中有幾行會呼叫 `tf.summary.scalar()`、`tf.summary.histogram()`、`tf.summary.FileWriter()` 等等。這些方法會將您實驗的重要計量分組、記錄並標記到執行歷程記錄中。 `tf.summary.FileWriter()` 特別重要，因為其會將來自已記錄實驗計量中的資料序列化，而讓 TensorBoard 能夠從中產生視覺效果。

 ### <a name="configure-experiment"></a>設定實驗

下面，我們會設定實驗並設定用來存放記錄和資料的目錄。 這些記錄會上傳至成品服務，以供 TensorBoard 稍後存取。

>[!Note]
> 在此 TensorFlow 範例中，您必須在本機電腦上安裝 TensorFlow。 此外，TensorBoard 模組 (也就是包含在 TensorFlow 中的模組) 必須可供此筆記本的核心存取，因為本機電腦是會用來執行 TensorBoard 的地方。

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>為實驗建立叢集
我們會為此實驗建立 AmlCompute 叢集，不過您可以在任何環境中建立您的實驗，而且仍然可以針對實驗執行歷程記錄來啟動 TensorBoard。 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>使用 TensorFlow 估算器來提交執行

TensorFlow 估算器可讓您輕鬆地在計算目標上啟動 TensorFlow 定型作業。 其會透過泛型 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 類別 (可用來支援任何架構) 來實作。 如需如何使用泛型估算器來將模型定型的詳細資訊，請參閱[藉由估算器使用 Azure Machine Learning 將模型定型](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>啟動 TensorBoard

您可以在執行期間或執行完成後啟動 TensorBoard。 下面，我們會建立 TensorBoard 物件執行個體 `tb`，以在 `run` 中載入實驗執行歷程記錄，然後使用 `start()` 方法來啟動 TensorBoard。 
  
[TensorBoard 建構函式](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py)會接受執行陣列，因此請務必以單一元素陣列的形式將其傳入。

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 雖然此範例使用 TensorFlow，但 TensorBoard 一樣可以輕鬆地用在 PyTorch 或 Chainer 模型上。 執行 TensorBoard 的電腦上必須有 TensorFlow，但在進行 PyTorch 或 Chainer 計算的電腦上則不需要 TensorFlow。 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>選項 2：將歷程記錄匯出為記錄以在 TensorBoard 中檢視

下列程式碼會設定範例實驗、使用 Azure Machine Learning 執行歷程記錄 API 開始記錄程序，並將實驗執行歷程記錄匯出為可供 TensorBoard 取用的記錄以便建立視覺效果。 

### <a name="set-up-experiment"></a>設定實驗

下列程式碼會設定新的實驗，並將執行目錄命名為 `root_run`。 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

在這裡，我們會載入糖尿病資料集 (這是 scikit-learn 隨附的內建小型資料集)，並將其分割為測試集和定型集。

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>執行實驗和記錄計量

在此程式碼中，我們會在執行歷程記錄中將線性迴歸模型定型，並記錄重要計量、Alpha 係數 (`alpha`) 和均方差 (`mse`)。

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>將執行匯出至 TensorBoard

透過 SDK 的 [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) 方法，我們可以將 Azure 機器學習實驗的執行歷程記錄匯出為 TensorBoard 記錄，以便透過 TensorBoard 來檢視。  

在下列程式碼中，我們會在目前的工作目錄中建立 `logdir` 資料夾。 我們可以從這個資料夾的 `root_run` 中匯出實驗執行歷程記錄和記錄檔，然後將該執行標示為已完成。 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 您也可以藉由指定執行的名稱 (`export_to_tensorboard(run_name, logdir)`)，將特定執行匯出至 TensorBoard

### <a name="start-and-stop-tensorboard"></a>啟動和停止 TensorBoard
一旦匯出此實驗的執行歷程記錄，我們就可以使用 [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 方法來啟動 TensorBoard。 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

當您完成時，請務必呼叫 TensorBoard 物件的 [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) 方法。 否則，TensorBoard 會繼續執行，直到您關閉筆記本核心為止。 

```python
tb.stop()
```

## <a name="next-steps"></a>後續步驟

在此操作說明中，您建立了兩個實驗，並已了解如何針對其執行歷程記錄啟動 TensorBoard，以找出可能的微調和重新定型區域。 

* 如果您對模型感到滿意，請前往我們的[如何部署模型](how-to-deploy-and-where.md)一文。 
* 深入了解[超參數微調](how-to-tune-hyperparameters.md)。 
