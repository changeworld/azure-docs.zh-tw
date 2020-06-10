---
title: 映像分類教學課程：將模型定型
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 搭配 scikit-learn 在 Python Jupyter Notebook 中將影像分類模型定型。 本教學課程兩個部分中的第一部分。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 3cdd39ea9581384e7dd37ed67c641849f2696e1c
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558326"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>教學課程：使用 MNIST 資料和 scikit-learn 將影像分類模型定型 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本教學課程中，您會以遠端計算資源訓練機器學習模型。 您將使用 Python Jupyter Notebook 中的 Azure Machine Learning 定型和部署工作流程。  然後，您可以使用 Notebook 作為範本，以自己的資料將您自己的機器學習服務模型定型。 本教學課程是**兩部分教學課程系列的第一部分**。  

本教學課程可讓您使用 [MNIST](http://yann.lecun.com/exdb/mnist/) 資料集，並搭配 [scikit-learn](https://scikit-learn.org) 與 Azure Machine Learning 定型簡單的羅吉斯迴歸。 MNIST 是熱門的資料集，由 70,000 個灰階影像所組成。 每個影像都是 28 x 28 像素的手寫數字，代表 0 到 9 的數字。 目標是要建立多類別分類器，來識別特定影像代表的數字。

了解如何執行下列動作：

> [!div class="checklist"]
> * 設定您的開發環境。
> * 存取及檢查資料。
> * 在遠端叢集上定型簡單的羅吉斯迴歸模型。
> * 檢閱定型結果並註冊最佳模型。

您會在[本教學課程的第二部分](tutorial-deploy-models-with-aml.md)中，了解如何選取模型及部署模型。

如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

>[!NOTE]
> 此文章中的程式碼已經過 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 1.0.83 版的測試。

## <a name="prerequisites"></a>必要條件

* 完成[教學課程：開始建立您的第一個 Azure ML 實驗](tutorial-1st-experiment-sdk-setup.md)，以便：
    * 建立工作區
    * 將教學課程 Notebook 複製到工作區中的資料夾。
    * 建立雲端式計算執行個體。

* 在複製的 tutorials/image-classification-mnist-data 資料夾中，開啟 img-classification-part1-training.ipynb 筆記本。 


如果您想要在自己的[本機環境](how-to-configure-environment.md#local)中使用此教學課程，也可以在 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 上取得此教學課程和隨附的 **utils.py** 檔案。 執行 `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` 以安裝此教學課程的相依性。

> [!Important]
> 本文的其餘部分包含與您在 Notebook 中所見相同的內容。  
>
> 如果您想要在執行程式碼時進行閱讀，請立即切換到 Jupyter Notebook。 
> 若要在 Notebook 中執行單一程式碼資料格，請按一下程式碼資料格，然後按 **Shift+Enter**。 或者，從頂端工具列中選擇 [全部執行]，以執行整個 Notebook。

## <a name="set-up-your-development-environment"></a><a name="start"></a>設定您的開發環境

針對您開發工作的所有設定都可以在 Python Notebook 中完成。 設定包含下列動作：

* 匯入 Python 套件。
* 連線到工作區，以便讓本機電腦能夠與遠端資源進行通訊。
* 建立實驗來追蹤所有執行。
* 建立要用於定型的遠端計算目標。

### <a name="import-packages"></a>匯入套件

匯入在本工作階段所需的 Python 套件。 此外，也顯示 Azure Machine Learning SDK 版本：

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>連線到工作區

從現有的工作區建立工作區物件。 `Workspace.from_config()` 會讀取 **config.json** 檔案，並將詳細資料載入到名為 `ws` 的物件：

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>建立實驗

建立實驗，以追蹤您工作區中的執行。 一個工作區可以有多個實驗：

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>建立或連結現有的計算目標

資料科學家可藉由使用 Azure Machine Learning Compute 這項受控服務，在 Azure 虛擬機器的叢集上訓練機器學習模型。 範例包括具有 GPU 支援的 VM。 在本教學課程中，您會建立 Azure Machine Learning Compute 作為訓練環境。 您會提交 Python 程式碼，以便稍後於本教學課程中在此 VM 上執行。 

如果您的工作區中還沒有計算叢集，下列程式碼將會為您建立計算叢集。 會將叢集設定為當未在使用中時縮小為 0，並且可以擴大到最多 4 個節點。 

 **建立計算目標需要大約 5 分鐘的時間。** 如果工作區中已有計算資源，則程式碼會加以使用而略過建立程序。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

您現在具有必要的套件和計算資源，可以在雲端將模型定型。 

## <a name="explore-data"></a>探索資料

在您將模型定型之前，必須先了解用來將模型定型的資料。 在本節中，您將了解如何：

* 下載 MNIST 資料集。
* 顯示一些範例影像。

### <a name="download-the-mnist-dataset"></a>下載 MNIST 資料集

使用 Azure 開放資料集來取得原始 MNIST 資料檔案。 [Azure 開放資料集](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets)是策劃的公用資料集，您可以使用這些公用資料集，將案例專有的功能新增至機器學習解決方案，以獲得更準確的模型。 每個資料集都有對應的類別 (在此案例中為 `MNIST`)，來以不同的方式擷取資料。

此程式碼以 `FileDataset` 物件的形式擷取資料，該物件是 `Dataset` 的子類別。 `FileDataset` 會參考資料存放區或公用 URL 中的單一或多個任意格式檔案。 透過建立資料來源位置的參考，類別可讓您將檔案下載或裝載至您的計算。 此外，您可以在工作區中註冊資料集，以便在定型期間進行輕鬆的擷取。

依照[操作說明](how-to-create-register-datasets.md)以深入了解資料集與其在 SDK 中的使用方式。

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>顯示一些範例影像

將壓縮的檔案載入到 `numpy` 陣列。 然後使用 `matplotlib` 來繪製 30 個來自資料集的隨機影像，並且在其上加上標籤。 此步驟需要 `util.py` 檔案中所包含的 `load_data` 函式。 這個檔案包含在範例資料夾。 請確定將它放在與此 Notebook 相同的資料夾中。 `load_data` 函式會直接將壓縮檔案剖析為 numpy 陣列。

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

影像的隨機範例會顯示：

![隨機的影像範例](./media/tutorial-train-models-with-aml/digits.png)

現在您已了解這些影像外觀與預期的預測結果。

## <a name="train-on-a-remote-cluster"></a>在遠端叢集上定型

針對這項工作，提交工作，已在您稍早設定的遠端定型叢集上執行。  若要提交工作，您要：
* 建立目錄
* 建立定型指令碼
* 建立 estimator 物件
* 提交工作

### <a name="create-a-directory"></a>建立目錄

建立目錄，以從您的電腦傳遞必要程式碼到遠端資源。

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>建立定型指令碼

若要將工作提交到叢集，請先建立定型指令碼。 執行下列程式碼，在您剛建立的目錄中建立定型指令碼，稱為 `train.py`。

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

請注意指令碼取得資料並儲存模型的方式：

+ 定型指令碼會讀取引數以尋找包含資料的目錄。 當您稍後提交工作時，您會指向這個引數的資料存放區：```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ 定型指令碼會將模型儲存到名為 **outputs** 的目錄中。 寫入此目錄之任何項目會自動上傳到您的工作區。 稍後在本教學課程中，您會從這個目錄存取您的模型。 `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ 定型指令碼需要 `utils.py` 檔案才能正確載入資料集。 下列程式碼會將 `utils.py` 複製到 `script_folder` 中，使該檔案可與遠端資源上的定型指令碼一起存取。

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>建立估計工具

估計工具物件用來提交執行。 Azure Machine Learning 已針對常見的機器學習架構來預先設定估算器，並也設定了一般估算器。 藉由指定下列項目來建立估算器


* 估算器物件的名稱 (`est`)。
* 包含指令碼的目錄。 在此目錄中的所有檔案都會上傳到叢集節點以便執行。
* 計算目標。 在此案例中，您會使用您所建立的 Azure Machine Learning 計算叢集。
* 定型指令碼名稱 (**train.py**)。
* 包含執行指令碼所需程式庫的環境。
* 來自定型指令碼的必要參數。

在本教學課程中，這個目標會是 AmlCompute。 指令碼資料夾中的所有檔案都會上傳到叢集節點以便執行。 **data_folder** 會設定為使用資料集。 「首先，建立包含下列項目的環境：scikit-learn 程式庫、存取資料集所需的 azureml-dataprep，以及 azureml-defaults (其中包含用於記錄計量的相依性)。 azureml-defaults 也包含之後將模型部署為 Web 服務 (本教學課程第 2 部分) 所需的相依性。

定義環境之後，請向工作區註冊此環境，以在本教學課程的第 2 部分重複使用。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

然後使用下列程式碼建立估算器。

```python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': mnist_file_dataset.as_named_input('mnist_opendataset').as_mount(),
    '--regularization': 0.5
}

est = Estimator(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>將工作提交至叢集

提交估算器物件以執行實驗：

```python
run = exp.submit(config=est)
run
```

由於呼叫是非同步的，因此只要工作一啟動，它就會傳回 **Preparing** 或 **Running** 狀態。

## <a name="monitor-a-remote-run"></a>監視遠端執行

第一次執行總計會花費**大約 10 分鐘的時間**。 但針對後續的執行，只要指令碼相依性不變，便會重複使用相同的影像。 因此容器啟動時間會快許多。

在您等候時，會發生什麼事：

- **影像建立**：系統會建立一個符合估算器所指定 Python 環境的 Docker 映像。 影像會上傳到工作區。 映像的建立和上傳會花費**大約 5 分鐘的時間**。

  這個階段會針對每個 Python 環境發生一次，因為系統會快取容器以供後續執行使用。 在影像建立期間，會將記錄串流至執行歷程記錄中。 您可以使用這些記錄來監視映像建立程序。

- **調整**：如果遠端叢集需要比目前可用節點數更多的節點來進行執行，將會自動新增額外的節點。 調整通常會花費**大約 5 分鐘的時間**。

- **執行中**：在這個階段，會將必要的指令碼與檔案傳送到計算目標。 然後會掛接或複製資料存放區。 接著會執行 **entry_script**。 當作業執行時，**stdout** 和 **./logs** 目錄會串流至執行歷程記錄。 您可以使用這些記錄來監視執行的進度。

- **後處理**：執行的 **./outputs** 目錄會複製到您工作區中的執行歷程記錄，以便您存取這些結果。

您可以透過數種方式檢查執行中作業的進度。 本教學課程會使用 Jupyter 小工具和 `wait_for_completion` 方法。

### <a name="jupyter-widget"></a>Jupyter 小工具

使用 [Jupyter 小工具](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)觀看執行的進度。 與提交執行相同，小工具會以非同步方式作業，並且會每隔 10 到 15 秒提供即時更新，直到作業完成為止：

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

在訓練結束時，小工具看起來會如下所示：

![Notebook 小工具](./media/tutorial-train-models-with-aml/widget.png)

如需取消執行，您可以依照[這些指示](https://aka.ms/aml-docs-cancel-run)操作。

### <a name="get-log-results-upon-completion"></a>在完成時取得記錄檔結果

模型定型和監視會在背景中發生。 請等到模型完成定型之後，再執行更多程式碼。 使用 `wait_for_completion` 可顯示模型定型何時完成：

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>顯示執行結果

您現在在遠端叢集上已有定型的模型。 擷取模型的精確度：

```python
print(run.get_metrics())
```

輸出顯示遠端模型有 0.9204 的精確度：

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

在下一個教學課程中，您將更詳細地探索這個模型。

## <a name="register-model"></a>註冊模型

定型指令碼的最後一個步驟會在工作執行所在叢集之 VM 中名為 `outputs` 的目錄內，寫入 `outputs/sklearn_mnist_model.pkl` 檔案。 `outputs` 目錄的特殊之處，在於此目錄中的所有內容都會自動上傳到您的工作區。 此內容會出現在您工作區下，實驗的執行記錄中。 因此，模型檔案現在也在您的工作區中可供使用。

您可以看到與該執行相關聯的檔案：

```python
print(run.get_file_names())
```

請在工作區中註冊模型，以便您或其他共同作業者稍後可以查詢、檢查和部署此模型：

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

您也可以只刪除 Azure Machine Learning Compute 叢集。 不過，自動調整已開啟，且叢集最小值為零。 因此，這個特定資源在不處於使用中狀態時，並不會產生額外的計算費用：

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>後續步驟

在這個 Azure Machine Learning 教學課程中，您已使用 Python 來進行下列工作：

> [!div class="checklist"]
> * 設定您的開發環境。
> * 存取及檢查資料。
> * 使用熱門的 scikit-learn 機器學習程式庫對遠端叢集訓練多個模型
> * 檢閱定型詳細資料並註冊最佳的模型。

您已準備好使用教學課程系列下一個部分中的指示，來部署這個已註冊的模型：

> [!div class="nextstepaction"]
> [教學課程 2 - 部署模型](tutorial-deploy-models-with-aml.md)
