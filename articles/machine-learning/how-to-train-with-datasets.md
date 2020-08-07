---
title: 使用 azureml 進行定型-資料集
titleSuffix: Azure Machine Learning
description: 瞭解如何在定型中使用資料集
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: acd030d8108ef3983be29fe85de6d7b3caf620af
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849330"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>使用 Azure Machine Learning 中的資料集進行定型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何在訓練實驗中使用[Azure Machine Learning 資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)。  您可以使用本機或遠端計算目標中的資料集，而不需擔心連接字串或資料路徑。

Azure Machine Learning 資料集提供與 Azure Machine Learning 訓練產品（例如[ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py)、[估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)、 [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py)和[Azure Machine Learning 管線](how-to-create-your-first-pipeline.md)）的完美整合。

## <a name="prerequisites"></a>必要條件

若要使用資料集來建立和定型，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括 azureml 資料集封裝。

> [!Note]
> 某些資料集類別具有[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)套件的相依性。 針對 Linux 使用者，只有下列散發版本才支援這些類別： Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="access-and-explore-input-datasets"></a>存取和探索輸入資料集

您可以從工作區上實驗的定型腳本存取現有的 TabularDataset，並將該資料集載入 pandas 資料框架，以進一步探索您的本機環境。

下列程式碼會使用 [`get_context()`]() 類別中的方法， [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) 來存取 `titanic` 定型腳本中現有的輸入 TabularDataset。 接著會使用 [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 方法，將該資料集載入至 pandas 資料框架，以便在定型之前進一步進行資料探索和準備。

> [!Note]
> 如果您的原始資料來源包含 NaN、空字串或空白值，則當您使用 to_pandas_dataframe ( # A1 時，這些值會被取代為*Null*值。 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

如果您需要從 [記憶體 pandas 資料框架] 將準備的資料載入至新的資料集，請將資料寫入本機檔案（例如 parquet），然後從該檔案建立新的資料集。 您也可以從本機檔案或資料存放區中的路徑來建立資料集。 深入瞭解[如何建立資料集](how-to-create-register-datasets.md)。

## <a name="use-datasets-directly-in-training-scripts"></a>直接在定型腳本中使用資料集

如果您的結構化資料尚未註冊為資料集，請建立 TabularDataset，並直接在您的本機或遠端實驗訓練腳本中使用它。

在此範例中，您會建立未註冊的[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) ，並使用它做為物件的直接輸入來 `estimator` 進行定型。 如果您想要將此 TabularDataset 與工作區中的其他實驗重複使用，請參閱[如何將資料集註冊到您的工作區](how-to-create-register-datasets.md#register-datasets)。

### <a name="create-a-tabulardataset"></a>建立 TabularDataset

下列程式碼會從 web url 建立未註冊的 TabularDataset。  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset 物件可讓您將 TabularDataset 中的資料載入 pandas 或 Spark 資料框架，讓您可以使用熟悉的資料準備和定型程式庫，而不需要離開您的筆記本。 若要利用這項功能，請參閱[存取和探索輸入資料集](#access-and-explore-input-datasets)。

### <a name="configure-the-estimator"></a>設定估計工具

[估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)物件是用來提交實驗執行。 Azure Machine Learning 已針對常見的機器學習架構和一般估計工具，預先設定估算器。

此程式碼會建立泛型估計工具物件， `est` 它會指定

* 腳本的腳本目錄。 在此目錄中的所有檔案都會上傳到叢集節點以便執行。
* 定型腳本， *train_titanic .py*。
* 用於定型的輸入資料集 `titanic_ds` 。 `as_named_input()`是必要的，以便在定型腳本中，由指派的名稱來參考輸入資料集 `titanic` 。 
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

## <a name="mount-files-to-remote-compute-targets"></a>將檔案掛接到遠端計算目標

如果您有非結構化資料，請建立[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) ，並掛接或下載您的資料檔案，讓它們可供您的遠端計算目標用於定型。 瞭解何時使用[掛接和下載](#mount-vs-download)來進行遠端訓練實驗。 

下列範例會建立 FileDataset，並將資料集裝載至計算目標，方法是將它當做估計工具中的引數傳遞以進行定型。 

### <a name="create-a-filedataset"></a>建立 FileDataset

下列範例會從 web url 建立未註冊的 FileDataset。 深入瞭解如何從其他來源[建立資料集](https://aka.ms/azureml/howto/createdatasets)。

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

### <a name="configure-the-estimator"></a>設定估計工具

建議您在裝載時，將資料集當做引數傳遞。 除了透過估計工具中的參數傳遞資料集之外 `inputs` ，您也可以透過引數，將資料集傳遞至， `script_params` 並在定型腳本中取得資料路徑 (掛接點) 。 如此一來，您就可以在任何雲端平臺上使用相同的訓練腳本進行本機的偵錯工具和遠端訓練。

[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)估計工具物件是用來提交 scikit-learn-學習實驗的執行。 提交執行之後，資料集所參考的資料檔案 `mnist` 將會掛接至計算目標。 深入瞭解如何使用[SKlearn 估計工具](how-to-train-scikit-learn.md)進行訓練。

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

### <a name="retrieve-the-data-in-your-training-script"></a>取得定型腳本中的資料

下列程式碼示範如何在您的腳本中取出資料。

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

## <a name="mount-vs-download"></a>掛接與下載

針對從 Azure Blob 儲存體、Azure 檔案儲存體、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database 和適用於 PostgreSQL 的 Azure 資料庫所建立的資料集，支援裝載或下載任何格式的檔案。 

當您**掛接**資料集時，您會將資料集所參考的檔案附加至目錄 (掛接點) 並使其可在計算目標上使用。 針對以 Linux 為基礎的計算支援掛接，包括 Azure Machine Learning 計算、虛擬機器和 HDInsight。 

當您**下載**資料集時，資料集所參考的所有檔案都會下載到計算目標。 所有計算類型都支援下載。 

如果您的腳本會處理資料集所參考的所有檔案，且您的計算磁片可以符合您的完整資料集，則建議下載，以避免從儲存體服務串流資料的額外負荷。 如果您的資料大小超過計算磁片大小，則無法下載。 針對此案例，我們建議您掛接，因為在處理時只會載入腳本所使用的資料檔案。

下列程式碼會裝載 `dataset` 至的臨時目錄`mounted_path`

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

## <a name="access-datasets-in-your-script"></a>存取腳本中的資料集

已註冊的資料集會在計算叢集（例如 Azure Machine Learning 計算）上，從本機和遠端存取。 若要跨實驗存取已註冊的資料集，請使用下列程式碼依名稱存取您的工作區和已註冊的資料集。 根據預設， [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) 類別上的方法 `Dataset` 會傳回已向工作區註冊之資料集的最新版本。

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>在定型期間存取原始程式碼

Azure Blob 儲存體的輸送量速度高於 Azure 檔案共用，且會調整為以平行方式啟動的大量作業。 因此，建議您將執行設定為使用 Blob 儲存體來傳輸原始程式碼檔案。

下列程式碼範例會在執行設定中指定要用於原始程式碼傳輸的 Blob 資料存放區。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>筆記本範例

[資料集筆記本](https://aka.ms/dataset-tutorial)會在本文中的概念上進行示範和擴充。

## <a name="next-steps"></a>後續步驟

* 使用 TabularDatasets[自動訓練機器學習模型](how-to-auto-train-remote.md)。

* 使用 FileDatasets 將[影像分類模型定型](https://aka.ms/filedataset-samplenotebook)。

* [使用管線將資料集定型](how-to-create-your-first-pipeline.md)。
