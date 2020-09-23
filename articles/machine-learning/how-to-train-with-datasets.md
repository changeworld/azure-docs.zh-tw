---
title: 使用 azureml （資料集）定型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用定型中的資料集
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
ms.openlocfilehash: 7a3f839a676723942af2e669839457ed3246aabd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885881"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>使用 Azure Machine Learning 中的資料集定型


在本文中，您將瞭解如何在訓練實驗中使用 [Azure Machine Learning 資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) 。  您可以使用本機或遠端計算目標中的資料集，而不需要擔心連接字串或資料路徑。

Azure Machine Learning 資料集提供與 Azure Machine Learning 訓練產品（例如 [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py&preserve-view=true)、 [估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py&preserve-view=true)、 [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py&preserve-view=true) 和 [Azure Machine Learning 管線](how-to-create-your-first-pipeline.md)）的緊密整合。

## <a name="prerequisites"></a>必要條件

若要建立和訓練資料集，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)，其中包含 azureml 資料集套件。

> [!Note]
> 某些資料集類別具有 [azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py&preserve-view=true) 封裝的相依性。 針對 Linux 使用者，只有下列發行版本才支援這些類別： Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="access-and-explore-input-datasets"></a>存取及探索輸入資料集

您可以從工作區上實驗的定型腳本來存取現有的 TabularDataset，然後將該資料集載入 pandas 資料框架，以便進一步探索您的本機環境。

下列程式碼會使用 [`get_context()`]() 類別中的方法， [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true) 存取 `titanic` 定型腳本中的現有輸入 TabularDataset。 然後使用 [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 方法將該資料集載入至 pandas 資料框架，以便在定型之前進一步進行資料探索和準備。

> [!Note]
> 如果您的原始資料來源包含 NaN、空字串或空白值，則當您使用 to_pandas_dataframe ( # A1 時，這些值會被取代為 *Null* 值。 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

如果您需要將備妥的資料從記憶體 pandas 資料框架載入至新的資料集，請將資料寫入本機檔案（例如 parquet），然後從該檔案建立新的資料集。 您也可以在資料存放區的本機檔案或路徑中建立資料集。 深入瞭解 [如何建立資料集](how-to-create-register-datasets.md)。

## <a name="use-datasets-directly-in-training-scripts"></a>直接在定型腳本中使用資料集

如果您的結構化資料尚未註冊為資料集，請建立 TabularDataset，並直接在您的本機或遠端實驗的定型腳本中使用。

在此範例中，您會建立未註冊的 [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) ，並使用它做為您的物件的直接輸入以 `estimator` 進行定型。 如果您想要在工作區中重複使用此 TabularDataset 與其他實驗，請參閱 [如何將資料集註冊到您的工作區](how-to-create-register-datasets.md#register-datasets)。

### <a name="create-a-tabulardataset"></a>建立 TabularDataset

下列程式碼會從 web url 建立未註冊的 TabularDataset。  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset 物件可讓您將 TabularDataset 中的資料載入 pandas 或 Spark 資料框架，讓您可以使用熟悉的資料準備和定型程式庫，而不需要離開您的筆記本。 若要利用這項功能，請參閱 [存取和探索輸入資料集](#access-and-explore-input-datasets)。

### <a name="configure-the-estimator"></a>設定估算器

[估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true)物件用來提交實驗執行。 Azure Machine Learning 已針對常見的機器學習架構和一般估算器預先設定估算器。

此程式碼會建立泛型估算器物件， `est` 以指定

* 腳本的腳本目錄。 在此目錄中的所有檔案都會上傳到叢集節點以便執行。
* 定型腳本 *train_titanic .py*。
* 用於定型的輸入資料集 `titanic_ds` 。 `as_named_input()` 這是必要的，以便 `titanic` 您的定型腳本中指派的名稱可以參考輸入資料集。 
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

## <a name="mount-files-to-remote-compute-targets"></a>將檔案掛接至遠端計算目標

如果您有非結構化資料，請建立 [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true) ，並裝載或下載您的資料檔案，使其可供您的遠端計算目標用於定型。 深入瞭解您的遠端訓練實驗使用 [掛接與下載](#mount-vs-download) 的時機。 

下列範例會建立 FileDataset，並將資料集以估算器中的引數傳遞至計算目標，以進行定型。 

> [!Note]
> 如果您使用自訂的 Docker 基底映射，則必須透過作為相依性來安裝保險絲， `apt-get install -y fuse` 才能讓資料集裝載運作。 瞭解如何 [建立自訂群組建映射](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)。

### <a name="create-a-filedataset"></a>建立 FileDataset

下列範例會從 web url 建立未註冊的 FileDataset。 深入瞭解如何從其他來源 [建立資料集](how-to-create-register-datasets.md) 。

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

### <a name="configure-the-estimator"></a>設定估算器

建議您在裝載時，將資料集當作引數傳遞。 除了透過估算器中的參數傳遞資料集外 `inputs` ，您也可以透過引數，透過傳遞資料集， `script_params` 並透過引數取得定型腳本中 (掛接點) 的資料路徑。 如此一來，您就可以在任何雲端平臺上，使用相同的定型腳本進行本機的調試和遠端訓練。

[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py&preserve-view=true)估算器物件是用來提交 scikit-learn 學習實驗的執行。 提交執行之後，資料集所參考的資料檔 `mnist` 將會掛接至計算目標。 深入瞭解如何使用 [SKlearn 估算器](how-to-train-scikit-learn.md)進行定型。

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

### <a name="retrieve-the-data-in-your-training-script"></a>取出定型腳本中的資料

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

## <a name="mount-vs-download"></a>掛接 vs 下載

從 Azure Blob 儲存體、Azure 檔案儲存體、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database 和適用於 PostgreSQL 的 Azure 資料庫所建立的資料集，都支援裝載或下載任何格式的檔案。 

當您 **掛接** 資料集時，您會將資料集所參考的檔案附加至目錄， (掛接點) 並讓它可在計算目標上使用。 以 Linux 為基礎的計算支援裝載，包括 Azure Machine Learning 計算、虛擬機器和 HDInsight。 

當您 **下載** 資料集時，資料集所參考的所有檔案都會下載到計算目標。 所有計算類型都支援下載。 

如果您的腳本會處理資料集所參考的所有檔案，而您的計算磁片可以容納您的完整資料集，則建議下載以避免從儲存體服務串流資料的額外負荷。 如果您的資料大小超過計算磁片大小，就不可能進行下載。 在此案例中，我們建議您掛接，因為只會在處理時載入腳本所使用的資料檔案。

下列程式碼會掛接 `dataset` 至位於下列位置的臨時目錄： `mounted_path`

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

註冊的資料集可在本機和遠端的計算叢集上存取，例如 Azure Machine Learning 計算。 若要跨實驗存取您已註冊的資料集，請使用下列程式碼依名稱存取您的工作區和已註冊的資料集。 根據預設， [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) 類別上的方法 `Dataset` 會傳回已向工作區註冊之資料集的最新版本。

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

[資料集筆記本](https://aka.ms/dataset-tutorial)會根據本文的概念進行示範和擴充。

## <a name="next-steps"></a>下一步

* 使用 TabularDatasets[自動將機器學習模型定型](how-to-auto-train-remote.md)。

* 使用 FileDatasets 將[影像分類模型定型](https://aka.ms/filedataset-samplenotebook)。

* [使用管線以資料集進行定型](how-to-create-your-first-pipeline.md)。
