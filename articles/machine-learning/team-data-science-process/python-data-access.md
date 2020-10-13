---
title: 使用 Python 用戶端程式庫存取資料集 - Team Data Science Process
description: 安裝並使用 Python 用戶端程式庫，從本機 Python 環境存取和管理 Azure Machine Learning 資料。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6c8e6fee2b9f01b8d7ab48990760aa4c4d6e11b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361495"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>使用 Azure Machine Learning Python 用戶端程式庫利用 Python 存取資料集
Microsoft Azure Machine Learning Python 用戶端程式庫的預覽能夠從本機 Python 環境安全存取您的 Azure Machine Learning 資料集，並且可在工作區中建立和管理資料集。

本主題提供如何執行以下作業的指示：

* 安裝 Machine Learning Python 用戶端程式庫
* 存取和上傳資料集，包括如何從本機 Python 環境取得授權以存取 Azure Machine Learning 資料集
* 存取實驗中的中繼資料集
* 使用 Python 用戶端程式庫來列舉資料集、存取中繼資料、讀取資料集的內容、建立新的資料集，以及更新現有的資料集

## <a name="prerequisites"></a><a name="prerequisites"></a>必要條件
Python 用戶端程式庫已在下列環境下經過測試：

* Windows、Mac 和 Linux
* Python 2.7、3.3 及 3.4

對下列套件具相依性：

* requests
* python-dateutil
* pandas

建議您使用 Python、IPython 隨附的 [Anaconda](https://www.anaconda.com/) 或 [Canopy](https://store.enthought.com/downloads/) 等 Python 發佈，並安裝上面列出的三個套件。 雖然不一定需要 IPython，但它是以互動方式操作和虛擬化資料的絕佳環境。

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>如何安裝 Azure Machine Learning Python 用戶端程式庫
安裝 Azure Machine Learning Python 用戶端程式庫，以完成本主題中所述的工作。 您可以從 [Python 套件索引](https://pypi.python.org/pypi/azureml)取得此程式庫。 若要在 Python 環境中安裝它，請從本機 Python 環境執行下列命令：

```console
pip install azureml
```

或者，您也可以從 [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python)上的來源下載並安裝。

```console
python setup.py install
```

如果必須在您的電腦上安裝 git，您可以使用 PIP 從 git 儲存機制直接安裝：

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>使用程式碼片段來存取資料集
Python 用戶端程式庫讓您以程式設計方式存取執行實驗所得的現有資料集。

您可以從 Azure Machine Learning Studio (傳統) web 介面，產生包含所有必要資訊的程式碼片段，以下載資料集，並將其還原序列化為本機電腦上的 pandas 資料框架物件。

### <a name="security-for-data-access"></a><a name="security"></a>資料存取安全性
Azure Machine Learning Studio 提供的程式碼片段 (傳統) 與 Python 用戶端程式庫搭配使用，包括您的工作區識別碼與授權權杖。 這些項目可提供工作區的完整存取權，且務必加以保護，像是密碼。

基於安全性理由，程式碼片段功能只提供給其角色設定為工作區「擁有者」 **** 的使用者。 您的角色會顯示在 [ **使用者** ] 頁面的 [ **設定**] Azure Machine Learning Studio (傳統) 。

![螢幕擷取畫面顯示 Azure Machine Learning Studio [使用者] 頁面中的設定。][security]

如果您的角色未設定為 [擁有者] ****，您可以要求重新受邀為擁有者，或要求該工作區的擁有者將程式碼片段提供給您。

若要取得授權權杖，您可以選擇下列其中一個選項：

* 向擁有者要求權杖。 擁有者可以從 Azure Machine Learning Studio (傳統) 中工作區的 [設定] 頁面存取其授權權杖。 選取左窗格中的 [設定]****，然後按一下 [授權權杖]****，即可看到主要與次要權杖。 雖然主要或次要授權權杖都能用於程式碼片段，但建議擁有者只共用次要授權權杖。

   ![授權權杖](./media/python-data-access/ml-python-access-settings-tokens.png)

* 要求提升為擁有者角色：工作區的目前擁有者必須先將您從工作區中移除，然後以擁有者身分重新邀請給您。

一旦開發人員取得工作區識別碼與授權權杖之後，就可以使用程式碼片段來存取工作區，而不論其角色為何。

授權權杖可以在 [設定]**** 下的 [授權權杖]**** 頁面上管理。 您可以重新產生權杖，但這個程序會撤銷上一個權杖的存取權。

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>從本機 Python 應用程式存取資料集
1. 在 Machine Learning Studio (傳統) 中，按一下左側導覽列中的 [ **資料集** ]。
2. 選取您想要存取的資料集。 您可以從 [範例]**** 清單的 [我的資料集]**** 清單中，選擇任何資料集。
3. 按一下底部工具列上的 [產生資料存取程式碼] ****。 如果資料格式與 Python 用戶端程式的不相容，就會停用這個按鈕。
   
    ![螢幕擷取畫面顯示具有產生資料存取碼的資料集。][datasets]
4. 從出現的視窗中選取程式碼片段，然後複製到剪貼簿。
   
    ![產生資料存取碼按鈕][dataset-access-code]
5. 將程式碼貼入本機 Python 應用程式的筆記本。
   
    ![將程式碼貼入筆記本][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>存取機器學習服務實驗中的中繼資料
在 Machine Learning Studio (傳統) 中執行實驗之後，就可以從模組的輸出節點存取中繼資料集。 中繼資料集是指當模型工具執行時為中繼步驟建立和使用的資料。

只要其資料格式能與 Python 用戶端程式庫相容，就能夠存取中繼資料集。

以下是支援這些格式 (常數的格式 `azureml.DataTypeIds` ：) 類別中：

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

您可以將滑鼠停留在模組輸出節點上方來判斷其格式。 其會與節點名稱一同顯示在工具提示中。

有些模組 (例如[分割][split]模組) 輸出的格式稱為 `Dataset`，但 Python 用戶端程式碼不支援這種格式。

![資料集格式][dataset-format]

您必須使用轉換模組 (例如，[轉換成 CSV][convert-to-csv])，才能將輸出變成支援的格式。

![GenericCSV 格式][csv-format]

下列步驟示範說明建立實驗、加以執行，然後群組中繼資料集。

1. 建立新實驗。
2. 插入 [成人收入普查二進位分類資料集] **** 模組。
3. 插入[分割][split]模組，然後將其輸入連接至資料集模組輸出。
4. 插入[轉換成 CSV][convert-to-csv] 模組，然後將其輸入連接至其中一個[分割][split]模組輸出。
5. 儲存並執行實驗，並等候作業完成。
6. 按一下 [轉換成 CSV 模組][convert-to-csv] 上的輸出節點。
7. 在隨即出現內容功能表，選取 [產生資料存取程式碼]****。
   
    ![操作功能表][experiment]
8. 選取程式碼片段，然後從出現的視窗中將它複製到剪貼簿。
   
    ![從內容功能表產生存取程式碼][intermediate-dataset-access-code]
9. 將程式碼貼入筆記本。
   
    ![將程式碼貼入筆記本][ipython-intermediate-dataset]
10. 您可以使用 matplotlib 將資料視覺化。 這樣會以長條圖顯示年齡欄：
    
    ![長條圖][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>使用 Machine Learning Python 用戶端程式碼來存取、讀取、建立及管理資料集
### <a name="workspace"></a>工作區
工作區是 Python 用戶端程式碼的進入點。 `Workspace`使用您的工作區識別碼和授權權杖來提供類別，以建立實例：

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>列舉資料集
若要列舉指定工作區中的所有資料集：

```python
for ds in ws.datasets:
    print(ds.name)
```

若只要列舉使用者建立的資料集：

```python
for ds in ws.user_datasets:
    print(ds.name)
```

若只要列舉範例資料集：

```python
for ds in ws.example_datasets:
    print(ds.name)
```

您可以依名稱 (區分大小寫) 存取資料集：

```python
ds = ws.datasets['my dataset name']
```

或您可以依索引來存取：

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>中繼資料
除了內容，資料集還有中繼資料。 (中繼資料集是這個規則的例外，而且沒有任何中繼資料)。

有些中繼資料值是由使用者在建立時指派：

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

其他則是由 Azure ML 指派的值：

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

如需可用中繼資料的詳細資訊，請參閱 `SourceDataset` 類別。

### <a name="read-contents"></a>讀取內容
Machine Learning Studio (傳統) 提供的程式碼片段會自動下載資料集，並將其還原序列化為 pandas 資料框架物件。 此動作可用 `to_dataframe` 方法來完成：

```python
frame = ds.to_dataframe()
```

您也可以選擇下載原始資料，並自己執行還原序列化。 對於 Python 用戶端程式庫無法還原序列化的格式 (例如 'ARFF')，目前這是唯一選擇。

若要以文字格式讀取內容：

```python
text_data = ds.read_as_text()
```

若要以二進位格式讀取內容：

```python
binary_data = ds.read_as_binary()
```

您也可以開啟內容的資料流：

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>建立新的資料集
Python 用戶端程式碼可讓您上傳 Python 程式中的資料集。 這些資料集接著可在您的工作區中使用。

如果您有資料在 pandas DataFrame 中，可以使用下列程式碼：

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

如果您的資料已經序列化，則可以使用：

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Python 用戶端程式庫能夠將 pandas DataFrame 序列化為下列格式 (這些都是 `azureml.DataTypeIds` 類別的常數)：

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>更新現有資料集
如果您嘗試以與現有資料集相符的名稱上傳新的資料集，應會發生衝突錯誤。

若要更新現有資料集，您必須先取得現有資料集的參照：

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

然後使用 `update_from_dataframe` 以序列化並取代 Azure 上資料集的內容：

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

如果您想要將資料序列化為不同的格式，可為選擇性 `data_type_id` 參數指定一個值。

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

您可以選擇性地為 `description` 參數指定一個值，以設定新的描述。

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

您可以選擇性地為 `name` 參數指定一個值，以設定新的名稱。 從現在起，您只會擷取使用新名稱的資料集。 下列程式碼會更新資料、名稱和描述。

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    name='existing dataset v2',
    description='data up to feb 2015',
)

print(dataset.data_type_id)                    # 'GenericCSV'
print(dataset.name)                            # 'existing dataset v2'
print(dataset.description)                     # 'data up to feb 2015'

print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
print(ws.datasets['existing dataset'].name)    # IndexError
```

`data_type_id`、`name` 及 `description` 都是選擇性參數，並以先前的值為預設值。 `dataframe` 一向是必要參數。

如果您的資料已經序列化，請使用 `update_from_raw_data`，而不是 `update_from_dataframe`： 如果您只傳入 `raw_data`，而不是 `dataframe`，其會以類似方式運作。

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

