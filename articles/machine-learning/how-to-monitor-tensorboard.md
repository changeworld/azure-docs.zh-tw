---
title: 使用 TensorBoard 將實驗視覺化
titleSuffix: Azure Machine Learning
description: 啟動 TensorBoard 以視覺化實驗執行歷程記錄，並找出超參數微調和重新定型的可能區域。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78195374"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>使用 TensorBoard 和 Azure Machine Learning 將實驗執行和計量視覺化
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何使用主要 Azure Machine Learning SDK 中[的`tensorboard`套件](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py)，在 TensorBoard 中查看您的實驗執行和計量。 檢查實驗執行之後，您就可以更進一步地微調和重新定型您的機器學習模型。

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview)是一套 web 應用程式，可檢查並瞭解您的實驗結構和效能。

如何使用 Azure Machine Learning 實驗啟動 TensorBoard，取決於實驗的類型：
+ 如果您的實驗原本就輸出 TensorBoard 所能使用的記錄檔，例如 PyTorch、Chainer 和 TensorFlow 實驗，則您可以直接從實驗的執行歷程記錄[啟動 TensorBoard](#direct) 。 

+ 對於原本不會輸出 TensorBoard 可耗用檔案的實驗（例如 Scikit-learn-學習或 Azure Machine Learning 實驗），請使用[ `export_to_tensorboard()`方法](#export)，將執行歷程記錄匯出為 TensorBoard 記錄，並從該處啟動 TensorBoard。 

> [!TIP]
> 本檔中的資訊主要適用于想要監視模型定型流程的資料科學家和開發人員。 如果您是系統管理員，想要監視 Azure Machine learning 中的資源使用方式和事件（例如配額、已完成的定型回合或完成的模型部署），請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md)。

## <a name="prerequisites"></a>先決條件

* 若要啟動 TensorBoard 並查看您的實驗執行歷程記錄，您的實驗必須先前已啟用記錄，以追蹤其計量和效能。  

* 本檔中的程式碼可在下列其中一個環境中執行： 

    * Azure Machine Learning 計算實例-不需要下載或安裝

        * 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。

        * 在筆記本伺服器的 samples 資料夾中，流覽至下列目錄來尋找兩個已完成和已展開的筆記本：
            * **使用方式 > 訓練-包含深度學習 > 匯出-執行歷程記錄到 tensorboard > 匯出-執行-歷程記錄到 tensorboard。 ipynb**

            * **使用說明 > 的追蹤和監視 > tensorboard 的實驗。 ipynb**

    * 您自己的 Juptyer 筆記本伺服器
       * 使用`tensorboard`額外的[安裝 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
        * [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。  
        * [建立工作區設定檔](how-to-configure-environment.md#workspace)。
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>選項1：在 TensorBoard 中直接查看執行歷程記錄

此選項適用于原生輸出 TensorBoard 所使用之記錄檔的實驗，例如 PyTorch、Chainer 和 TensorFlow 實驗。 如果這不是您實驗的情況，請改用[ `export_to_tensorboard()`方法](#export)。

下列範例程式碼會從遠端計算目標中的 TensorFlow 存放庫使用[MNIST 示範實驗](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py)，Azure Machine Learning 計算。 接下來，我們會使用 SDK 的自訂[TensorFlow 估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)來定型模型，然後開始針對此 TensorFlow 實驗進行 TensorBoard，也就是原生輸出 TensorBoard 事件檔案的實驗。

### <a name="set-experiment-name-and-create-project-folder"></a>設定實驗名稱並建立專案資料夾

在這裡，我們將實驗命名為，並建立其資料夾。 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>下載 TensorFlow 示範實驗程式碼

TensorFlow 的存放庫有一個 MNIST 示範，其中包含廣泛的 TensorBoard 檢測。 我們不需要改變此示範的任何程式碼，就能與 Azure Machine Learning 搭配使用。 在下列程式碼中，我們會下載 MNIST 程式碼，並將它儲存在新建立的實驗資料夾中。

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
在 MNIST 程式碼檔案中，mnist_with_summaries .py，請注意，有幾行會`tf.summary.scalar()`呼叫`tf.summary.histogram()`、 `tf.summary.FileWriter()`等等。這些方法會將您實驗的重要計量分組、記錄及標記為執行歷程記錄。 在`tf.summary.FileWriter()`從記錄的實驗計量中序列化資料時，特別重要，這可讓 TensorBoard 從他們產生視覺效果。

 ### <a name="configure-experiment"></a>設定實驗

在下列程式中，我們會設定我們的實驗，並設定記錄和資料的目錄。 這些記錄將會上傳至成品服務，稍後會 TensorBoard 存取。

>[!Note]
> 在此 TensorFlow 範例中，您必須在本機電腦上安裝 TensorFlow。 此外，TensorBoard 模組（也就是包含在 TensorFlow 中的模組）必須可供此筆記本的核心存取，因為本機電腦是執行 TensorBoard 的地方。

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

### <a name="create-a-cluster-for-your-experiment"></a>為您的實驗建立叢集
我們會為此實驗建立 AmlCompute 叢集，不過您可以在任何環境中建立您的實驗，而且仍然可以針對實驗執行歷程記錄啟動 TensorBoard。 

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

### <a name="submit-run-with-tensorflow-estimator"></a>以 TensorFlow 估計工具提交執行

TensorFlow 估計工具提供一種簡單的方式，在計算目標上啟動 TensorFlow 訓練作業。 它是透過泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別來執行，它可以用來支援任何架構。 如需使用泛型估計工具定型模型的詳細資訊，請參閱[使用估計工具以 Azure Machine Learning 定型模型](how-to-train-ml-models.md)

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

您可以在執行期間或完成後啟動 TensorBoard。 在下列程式中，我們會建立 TensorBoard 物件實例`tb`，其會將實驗執行歷程記錄載入中`run`，然後使用`start()`方法啟動 TensorBoard。 
  
[TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py)的函式會接受執行的陣列，因此請務必將它當做單一元素陣列傳入。

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 雖然此範例使用 TensorFlow，但 TensorBoard 可輕鬆地搭配 PyTorch 或 Chainer 模型使用。 執行 TensorBoard 的電腦上必須有 TensorFlow，但在進行 PyTorch 或 Chainer 計算的電腦上則不需要。 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>選項2：在 TensorBoard 中將歷程記錄匯出為記錄

下列程式碼會設定範例實驗、使用 Azure Machine Learning 執行歷程記錄 Api 開始記錄程式，並將實驗執行歷程記錄匯出到 TensorBoard 所取用的記錄中以供視覺效果。 

### <a name="set-up-experiment"></a>設定實驗

下列程式碼會設定新的實驗，並將執行目錄`root_run`命名為。 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

在這裡，我們會載入糖尿病資料集，這是 scikit-learn 所隨附的內建小型資料集，並將它分割成測試和定型集。

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

在此程式碼中，我們會在執行歷程記錄中訓練線性回歸模型和記錄`alpha`索引鍵計量、Alpha 係數`mse`和 mean 平方誤差。

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

### <a name="export-runs-to-tensorboard"></a>將執行匯出至 TensorBoard

透過 SDK 的[export_to_tensorboard （）](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py)方法，我們可以將 Azure machine learning 實驗的執行歷程記錄匯出至 tensorboard 記錄，以便我們可以透過 tensorboard 來加以查看。  

在下列程式碼中，我們會在`logdir`目前的工作目錄中建立資料夾。 此資料夾是我們將在其中匯出實驗執行歷程記錄和記錄`root_run`檔的位置，然後將該執行標示為已完成。 

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
 您也可以藉由指定執行的名稱，將特定執行匯出至 TensorBoard`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>啟動和停止 TensorBoard
一旦匯出此實驗的執行歷程記錄，我們就可以使用[start （）](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-)方法來啟動 TensorBoard。 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

當您完成時，請務必呼叫 TensorBoard 物件的[stop （）](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--)方法。 否則，TensorBoard 會繼續執行，直到您關閉筆記本核心為止。 

```python
tb.stop()
```

## <a name="next-steps"></a>後續步驟

在此操作說明中，建立兩個實驗，並瞭解如何針對其執行歷程記錄啟動 TensorBoard，以找出可能的微調和重新定型的區域。 

* 如果您對模型感到滿意，請前往我們的[如何部署模型一](how-to-deploy-and-where.md)文。 
* 深入瞭解[超參數微調](how-to-tune-hyperparameters.md)。 
