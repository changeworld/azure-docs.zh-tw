---
title: 使用 TensorBoard 將實驗視覺化
titleSuffix: Azure Machine Learning
description: 啟動 TensorBoard 以視覺化實驗執行歷程記錄，並確定超參數調優和再培訓的潛在區域。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195374"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>使用 TensorBoard 和 Azure 機器學習視覺化實驗運行和指標
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何使用主 Azure 機器學習 SDK 中的[`tensorboard`包](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py)在 TensorBoard 中查看實驗運行和指標。 檢查實驗運行後，可以更好地調整和重新訓練機器學習模型。

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview)是一套 Web 應用程式，用於檢查和理解您的實驗結構和性能。

使用 Azure 機器學習實驗啟動 TensorBoard 的方式取決於實驗的類型：
+ 如果實驗本機輸出了 TensorBoard 可消耗的日誌檔，如 PyTorch、鏈子和 TensorFlow 實驗，則可以直接從實驗的執行歷程記錄[中啟動 TensorBoard。](#direct) 

+ 對於未本機輸出 TensorBoard 易耗檔的實驗（如 Scikit 學習或 Azure 機器學習實驗），請使用[`export_to_tensorboard()`該方法](#export)將執行歷程記錄匯出為 TensorBoard 日誌，並從那裡啟動 TensorBoard。 

> [!TIP]
> 本文檔中的資訊主要面向想要監視模型培訓過程的資料科學家和開發人員。 如果您是對監視 Azure 機器學習中的資源使用方式和事件（如配額、已完成訓練運行或已完成的模型部署）感興趣的管理員，請參閱[監視 Azure 機器學習](monitor-azure-machine-learning.md)。

## <a name="prerequisites"></a>Prerequisites

* 要啟動 TensorBoard 並查看實驗執行歷程記錄，您的實驗需要以前啟用的日誌記錄來跟蹤其指標和性能。  

* 本文檔中的代碼可以在以下任一環境中運行： 

    * Azure 機器學習計算實例 - 無需下載或安裝

        * 完成[教程：設置環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以創建預載入 SDK 和示例存儲庫的專用筆記本伺服器。

        * 在筆記本伺服器上的示例資料夾中，通過導航到這些目錄來查找兩個已完成和展開的筆記本：
            * **使用方式-azureml >培訓-深度學習>出口運行歷史到 tens板>出口運行歷史到 tensorboard.**

            * **如何使用-azureml>跟蹤和監控實驗>張緊板。**

    * 您自己的 Juptyer 筆記本電腦伺服器
       * [安裝](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) `tensorboard` Azure 機器學習 SDK
        * [創建 Azure 機器學習工作區](how-to-manage-workspace.md)。  
        * [創建工作區設定檔](how-to-configure-environment.md#workspace)。
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>選項 1：直接查看 TensorBoard 中的執行歷程記錄

此選項適用于本機輸出 TensorBoard 可消耗的日誌檔的實驗，例如 PyTorch、鏈條和 TensorFlow 實驗。 如果情況並非如此，請使用[方法`export_to_tensorboard()`](#export)。

以下示例代碼在遠端計算目標 Azure 機器學習計算中使用 TensorFlow 存儲庫中的[MNIST 演示實驗](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py)。 接下來，我們使用 SDK 的自訂[TensorFlow 估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)訓練模型，然後針對此 TensorFlow 實驗啟動 TensorBoard，即本機輸出 TensorBoard 事件檔的實驗。

### <a name="set-experiment-name-and-create-project-folder"></a>設置實驗名稱並創建專案資料夾

在這裡，我們命名實驗並創建其資料夾。 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>下載 TensorFlow 演示實驗代碼

TensorFlow 的存儲庫具有具有大量 TensorBoard 儀器的 MNIST 演示。 我們不需要也不需要更改本演示的任何代碼，以便它與 Azure 機器學習配合使用。 在以下代碼中，我們下載 MNIST 代碼並將其保存在新創建的實驗資料夾中。

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
在整個 MNIST 代碼檔中，mnist_with_summaries.py，請注意有呼叫`tf.summary.scalar()`的`tf.summary.histogram()`行，`tf.summary.FileWriter()`等。這些方法將實驗的關鍵指標分組、記錄和標記到執行歷程記錄中。 特別`tf.summary.FileWriter()`重要，因為它序列化了來自您記錄的實驗指標中的資料，這允許 TensorBoard 從它們中生成視覺化效果。

 ### <a name="configure-experiment"></a>設定實驗

在下面，我們配置實驗並設置日誌和資料的目錄。 這些日誌將上載到工件服務，TensorBoard 稍後將訪問該服務。

>[!Note]
> 對於此 TensorFlow 示例，您需要在本地電腦上安裝 TensorFlow。 此外，TensorBoard 模組（即 TensorFlow 中包含的模組）必須可供此筆記本的內核訪問，因為本地電腦是運行 TensorBoard 的模組。

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

### <a name="create-a-cluster-for-your-experiment"></a>為實驗創建群集
我們為此實驗創建 AmlCompute 群集，但是您的實驗可以在任何環境中創建，您仍然能夠針對實驗執行歷程記錄啟動 TensorBoard。 

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

### <a name="submit-run-with-tensorflow-estimator"></a>使用 TensorFlow 估計器提交運行

TensorFlow 估計器提供了一種在計算目標上啟動 TensorFlow 訓練作業的簡單方法。 它通過泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類實現，可用於支援任何框架。 有關使用通用估計器的訓練模型的詳細資訊，請參閱[使用估計器使用 Azure 機器學習訓練模型](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>啟動張力板

您可以在運行期間或運行完成後啟動 TensorBoard。 在下面，我們創建一個 TensorBoard 物件`tb`實例 ，該實例將獲取載入在`run`中的實驗執行歷程記錄，然後使用 方法`start()`啟動 TensorBoard。 
  
[TensorBoard 建構函式](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py)採用一個運行陣列，因此請確保並將其作為單元素陣列傳遞。

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 雖然此示例使用 TensorFlow，但 TensorBoard 可以像 PyTorch 或鏈子型號一樣容易使用。 TensorFlow 必須在運行 TensorBoard 的電腦上可用，但在執行 PyTorch 或鏈式計算的電腦上不需要 TensorFlow。 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>選項 2：將歷史記錄匯出為日誌，以在 TensorBoard 中查看

以下代碼設置示例實驗，使用 Azure 機器學習執行歷程記錄 API 開始日誌記錄過程，並將實驗執行歷程記錄匯出到 TensorBoard 可使用的日誌中進行視覺化。 

### <a name="set-up-experiment"></a>設置實驗

以下代碼設置一個新的實驗，並命名運行目錄`root_run`。 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

在這裡，我們載入糖尿病資料集 -一個內置的小資料集，附帶 scikit 學習，並將其拆分為測試和培訓集。

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

### <a name="run-experiment-and-log-metrics"></a>運行實驗和日誌指標

對於此代碼，我們在執行歷程記錄中訓練線性回歸模型並記錄關鍵指標、Alpha`alpha`係數和均方誤差。 `mse`

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>匯出運行到 TensorBoard

使用 SDK 的[export_to_tensorboard（）](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py)方法，我們可以將 Azure 機器學習實驗的執行歷程記錄匯出到 TensorBoard 日誌中，以便通過 TensorBoard 查看它們。  

在以下代碼中，我們在當前工作目錄中`logdir`創建資料夾。 此資料夾是我們將匯出實驗執行歷程記錄和日誌的位置`root_run`，然後將其標記為已完成。 

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
 還可以通過指定運行的名稱將特定運行匯出到 TensorBoard`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>啟動和停止 TensorBoard
匯出此實驗的執行歷程記錄後，我們可以使用[start（）](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-)方法啟動 TensorBoard。 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

完成後，請確保調用 TensorBoard 物件的[stop（）](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--)方法。 否則，TensorBoard 將繼續運行，直到您關閉筆記本內核。 

```python
tb.stop()
```

## <a name="next-steps"></a>後續步驟

在此，創建兩個實驗，並學習如何啟動 TensorBoard 針對其執行歷程記錄，以確定潛在的調優和再培訓區域。 

* 如果您對模型感到滿意，請前往我們的["如何部署模型](how-to-deploy-and-where.md)"文章。 
* 瞭解有關[超參數調優](how-to-tune-hyperparameters.md)的更多。 
