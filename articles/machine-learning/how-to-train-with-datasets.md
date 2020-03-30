---
title: 使用 azureml 資料集進行訓練
titleSuffix: Azure Machine Learning
description: 瞭解如何在培訓中使用資料集
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283494"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>在 Azure 機器學習中使用資料集進行訓練
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解在遠端實驗培訓運行中使用[Azure 機器學習資料集的](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)兩種方法，而無需擔心連接字串或資料路徑。

- 選項 1：如果您有結構化資料，請創建表格資料集，並直接在培訓腳本中使用。

- 選項 2：如果您有非結構化資料，請創建檔資料集並將檔裝載或下載到遠端計算以進行培訓。

Azure 機器學習資料集提供與 Azure 機器學習培訓產品（如[腳本運行](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py)、[估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)[、HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py)和[Azure 機器學習管道）的無縫集成](how-to-create-your-first-pipeline.md)。

## <a name="prerequisites"></a>Prerequisites

要使用資料集創建和訓練，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure 機器學習工作區](how-to-manage-workspace.md)。

* [安裝的 Python 的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括 azurem 資料集包。

> [!Note]
> 某些資料集類對[azureml 資料準備](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包具有依賴關係。 對於 Linux 使用者，這些類僅支援以下發行版：紅帽企業 Linux、Ubuntu、Fedora 和 CentOS。

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>選項 1：在訓練腳本中直接使用資料集

在此示例中，您將創建[一個表格資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)，並將其用作物件用於培訓的直接輸入`estimator`。 

### <a name="create-a-tabulardataset"></a>創建表格資料集

以下代碼從 Web URL 創建未註冊的 Tabular 資料集。 您還可以從資料存儲中的本地檔或路徑創建資料集。 詳細瞭解[如何創建資料集](https://aka.ms/azureml/howto/createdatasets)。

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>訪問訓練腳本中的輸入資料集

表格資料集物件提供將資料載入到熊貓或激發 DataFrame 的功能，以便您可以使用熟悉的資料準備和培訓庫。 若要利用此功能，可以將 TabularDataset 作為訓練配置中的輸入，然後在腳本中檢索它。

為此，請通過訓練腳本中[`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py)的物件訪問輸入資料集，並使用 方法。 [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>配置估計器

[估計物件](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)用於提交實驗運行。 Azure 機器學習具有用於常見機器學習框架的預配置評估器，以及通用估計器。

此代碼創建一個泛型估計器`est`物件，指定

* 腳本的腳本目錄。 在此目錄中的所有檔案都會上傳到叢集節點以便執行。
* 訓練腳本 *，train_titanic.py*。
* 用於訓練的輸入資料集。 `titanic` `as_named_input()`是必需的，以便輸入資料集可以由訓練腳本中的指定名稱引用。 
* 實驗的計算目標。
* 實驗的環境定義。

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>選項 2：將檔裝載到遠端計算目標

如果要使資料檔案在用於培訓的計算目標上可用，請使用[FileData](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)裝載或下載其引用的檔。

### <a name="mount-vs-download"></a>安裝與下載

對於從 Azure Blob 存儲、Azure 檔、Azure 資料存儲第 1 代、Azure 資料存儲第 2 代、Azure SQL 資料庫和用於 PostgreSQL 的 Azure 資料庫創建的資料集，都支援安裝或下載任何格式的檔。 

裝載資料集時，將資料集引用的檔附加到目錄（裝載點），使其在計算目標上可用。 支援基於 Linux 的計算安裝，包括 Azure 機器學習計算、虛擬機器和 HDInsight。 下載資料集時，資料集引用的所有檔都將下載到計算目標。 支援所有計算類型下載。 

如果腳本處理資料集引用的所有檔，並且計算磁片可以適合您的完整資料集，建議下載以避免存儲服務流資料開銷。 如果資料大小超過計算磁片大小，則無法下載。 對於此方案，我們建議安裝，因為在處理時僅載入腳本使用的資料檔案。

以下代碼裝載`dataset`到臨時目錄，`mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

### <a name="create-a-filedataset"></a>建立 FileDataset

下面的示例從 Web URL 創建未註冊的檔資料集。 詳細瞭解如何從其他源[創建資料集](https://aka.ms/azureml/howto/createdatasets)。

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>配置估計器

除了通過估計器中的`inputs`參數傳遞資料集外，您還可以通過資料集傳遞`script_params`資料，並通過參數獲取定型腳本中的資料路徑（安裝點）。 這樣，就可以使訓練腳本獨立于 azureml-sdk。 換句話說，您將能夠在任何雲平臺上使用相同的訓練腳本進行本地調試和遠端培訓。

[SK學習](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)估計器物件用於提交用於 scikit 學習實驗的運行。 瞭解有關使用[SK 學習估計器](how-to-train-scikit-learn.md)進行培訓的更多詳細資訊。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>檢索培訓腳本中的資料

提交運行後，`mnist`資料集引用的資料檔案將裝載到計算目標。 以下代碼演示如何檢索腳本中的資料。

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>筆記本範例

[資料集筆記本](https://aka.ms/dataset-tutorial)演示並擴展了本文中的概念。

## <a name="next-steps"></a>後續步驟

* [使用表格資料集自動訓練機器學習模型](how-to-auto-train-remote.md)

* [使用檔資料集訓練圖像分類模型](https://aka.ms/filedataset-samplenotebook)

* [使用管道使用資料集進行訓練](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
