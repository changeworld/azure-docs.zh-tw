---
title: 使用 Azure 開放資料集建立資料集
titleSuffix: Azure Open Datasets
description: 瞭解如何從 Azure 開放資料集建立 Azure Machine Learning 資料集。
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: c90d11ba630dbb1e37054715855ae5547a8a034b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902721"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>從 Azure 開放資料集建立 Azure Machine Learning 資料集

在本文中，您將瞭解如何使用 [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) 資料集和 [Azure 開放資料集](https://docs.microsoft.com/azure/open-datasets/)，將策劃擴充資料帶入本機或遠端機器學習實驗。 

藉由建立 [Azure Machine Learning 資料集](../machine-learning/how-to-create-register-datasets.md)，您可以建立資料來源位置的參考，以及其中繼資料的複本。 因為資料集會進行延遲的評估，且資料會保留在現有的位置，所以您
* 不會產生額外的儲存體成本。
* 不會有意外變更原始資料來源的風險。 
* 改善 ML 工作流程的效能速度。

若要瞭解資料集符合 Azure Machine Learning 的整體資料存取工作流程，請參閱 [安全存取資料](../machine-learning/concept-data.md#data-workflow) 檔。

Azure 開放資料集是策劃的公用資料集，可讓您用來新增案例專屬的功能，以擴充您的預測解決方案並改善其精確度。 請參閱 [開放資料集目錄](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) 中的公用網域資料，以協助您訓練機器學習模型，例如：

* [天氣](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [政府](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [假期](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [公共安全](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

開放資料集位於雲端上的 Microsoft Azure，同時包含在 [Azure Machine Learning PYTHON SDK](#create-datasets-with-the-sdk) 和 [Azure Machine Learning studio](#create-datasets-with-the-studio)中。


## <a name="prerequisites"></a>必要條件

在本文中，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure Machine Learning 工作區](../machine-learning/how-to-manage-workspace.md)。

* [安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含 `azureml-datasets` 套件。

    * 建立 [Azure Machine Learning 計算實例](../machine-learning/concept-compute-instance.md#managing-a-compute-instance)，此實例是完全設定且受管理的開發環境，其中包含已安裝的整合式筆記本和 SDK。

    **OR**

    * 使用您自己的 Python 環境，並使用 [這些指示](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)自行安裝 SDK。

> [!NOTE]
> 某些資料集類別具有 [azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 套件的相依性，其僅與64位 Python 相容。 針對 Linux 使用者，只有下列發行版本才支援這些類別： Red Hat Enterprise Linux (7、8) 、Ubuntu (14.04、16.04、18.04) 、Fedora (27、28) 、Debian (8、9) 和 CentOS (7) 。

## <a name="create-datasets-with-the-sdk"></a>使用 SDK 建立資料集

若要透過 Python SDK 中的 Azure 開放資料集類別建立 Azure Machine Learning 資料集，請確定您已使用安裝套件 `pip install azureml-opendatasets` 。 每個離散資料集都是在 SDK 中以自己的類別來表示，而某些類別可作為 Azure Machine Learning [ `TabularDataset` 、 `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)或兩者。 請參閱 [參考檔](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) ，以取得完整的 `opendatasets` 類別清單。

您可以將特定 `opendatasets` 類別以或的形式抓取，這樣可 `TabularDataset` `FileDataset` 讓您直接操作及/或下載檔案。 其他類別只能使用**only** `get_tabular_dataset()` `get_file_dataset()` `Dataset` Python SDK 中類別的或函式來取得資料集。

下列程式碼顯示 MNIST `opendatasets` 類別可以傳回 `TabularDataset` 或 `FileDataset` 。 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

在此範例中，糖尿病 `opendatasets` 類別只能作為 `TabularDataset` 使用，因此使用 `get_tabular_dataset()` 。

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>註冊資料集

使用您的工作區註冊 Azure Machine Learning 資料集，以便與其他人共用這些資料集，並在工作區中的實驗之間重複使用這些資料集。 當您註冊從開啟的資料集建立的 Azure Machine Learning 資料集時，將不會立即下載任何資料，但稍後會在定型期間要求 (時存取資料，例如從中央儲存體位置) 。

若要向工作區註冊您的資料集，請使用 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) 方法。 
```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>使用 studio 建立資料集

您也可以使用 [Azure Machine Learning studio](https://ml.azure.com)從 Azure 開放資料集建立 Azure Machine Learning 資料集，這是一個合併的 web 介面，其中包含機器學習工具，可針對所有技能等級的資料科學執行者執行資料科學案例。

> [!Note]
> 透過 Azure Machine Learning studio 建立的資料集會自動註冊到工作區。

1. 在工作區中，選取 [**資產**] 底下的 [**資料集**] 索引標籤。 在 [ **建立資料集** ] 下拉式功能表中，選取 [ **從開啟的資料集**]。

    ![使用 UI 開啟資料集](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. 選取資料集的磚來加以選取。  (您可以使用 [搜尋] 列篩選選項。 ) 選取 **[下一步]**。

    ![選擇資料集](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. 選擇用來註冊資料集的名稱，並選擇性地使用可用的篩選準則來篩選資料。 在此情況下，針對「 **公用假日** 」資料集，您可以將時間週期篩選為一年，並將國家/地區代碼篩選為只有美國。 如需詳細資料，請參閱 [Azure 開放資料集目錄](https://azure.microsoft.com/services/open-datasets/catalog) ，例如欄位描述和日期範圍。 選取 [建立]。

    ![設定資料集參數和建立資料集](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    資料集現在已可在您的工作區中的 **資料集**下使用。 您可以使用與您所建立之其他資料集相同的方式來使用它。


## <a name="access-datasets-for-your-experiments"></a>存取實驗的資料集

在機器學習實驗中使用您的資料集來定型 ML 模型。 [深入了解如何使用資料集進行定型](../machine-learning/how-to-train-with-datasets.md)。

## <a name="example-notebooks"></a>Notebook 範例

如需開啟資料集功能的範例和示範，請參閱這些 [範例筆記本](samples.md)。

## <a name="next-steps"></a>下一步

* [定型您的第一個 ML 模型](../machine-learning/tutorial-1st-experiment-sdk-train.md)。

* [使用資料集進行定型](../machine-learning/how-to-train-with-datasets.md)。

* [建立 Azure machine learning 資料集](../machine-learning/how-to-create-register-datasets.md)。



