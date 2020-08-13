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
ms.openlocfilehash: e6f3a541f1e9dbca2c9949fb0c5cde28cd43e8e5
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183004"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>從 Azure 開放資料集建立 Azure Machine Learning 資料集
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何從[Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)建立 Azure Machine Learning 資料集，以存取本機或遠端實驗的資料。 

藉由建立[Azure Machine Learning 資料集](../machine-learning/how-to-create-register-datasets.md)，您可以建立資料來源位置的參考，以及其中繼資料的複本。 由於資料會保留在現有的位置，因此不會產生額外的儲存成本，也不會造成意外變更原始資料來源的風險。 此外，會延遲評估資料集，以協助工作流程效能速度。
 
若要瞭解資料集如何放入 Azure Machine Learning 的整體資料存取工作流程中，請參閱[安全存取資料](../machine-learning/concept-data.md#data-workflow)一文。


開放資料集位於 Microsoft Azure 的雲端，同時包含在[Azure Machine Learning PYTHON SDK](#create-datasets-with-the-sdk)和[Azure Machine Learning studio](#create-datasets-with-the-studio)中。

## <a name="why-use-azure-open-datasets"></a>為何要使用 Azure 開放資料集？ 

Azure 開放資料集是策劃的公用資料集，您可以使用這些公用資料集，將案例專有的功能新增至機器學習解決方案，以獲得更準確的模型。 資料集包含用於天氣、人口普查、假日、公共安全和位置的公用領域資料，可協助您將機器學習模型定型並擴充預測性解決方案。 

如需詳細資訊，請參閱[什麼是開放資料集？](overview-what-are-open-datasets.md)

## <a name="prerequisites"></a>必要條件

若要建立及使用資料集，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure Machine Learning 工作區](../machine-learning/how-to-manage-workspace.md)。

* [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括 azureml 資料集封裝。

    * 建立[Azure Machine Learning 計算實例](../machine-learning/concept-compute-instance.md#managing-a-compute-instance)，這是完全設定且受管理的開發環境，其中包含已安裝的整合式筆記本和 SDK。

    **OR**

    * 使用您自己的 Jupyter 筆記本，並透過[這些指示](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)自行安裝 SDK。

> [!NOTE]
> 有些資料集類別具有[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)套件的相依性，這只與64位 Python 相容。 針對 Linux 使用者，只有下列散發版本支援這些類別： Red Hat Enterprise Linux (7、8) 、Ubuntu (14.04、16.04、18.04) 、Fedora (27、28) 、Debian (8、9) 和 CentOS (7) 。

## <a name="create-datasets-with-the-sdk"></a>使用 SDK 建立資料集

若要透過 Azure Machine Learning Python SDK 中的開啟資料集類別來建立資料集，請確定您已使用安裝套件 `pip install azureml-opendatasets` 。 每個離散資料集會以自己在 SDK 中的類別來表示，而某些類別則可當做 `TabularDataset` 、 `FileDataset` 或兩者使用。 如需類別的完整清單，請參閱[參考檔](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)。

您可以將某些類別抓取為 `TabularDataset` 或 `FileDataset` ，以讓您直接操作和（或）下載檔案。 其他**類別只能使用**其中一個或函數來取得資料 `get_tabular_dataset()` 集 `get_file_dataset()` 。 下列程式碼範例顯示這些類別類型的一些範例。

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

當您註冊從開啟的資料集建立的資料集時，不會立即下載任何資料，但稍後在定型期間要求 (時，就會存取資料，例如從中央儲存位置) 。

## <a name="create-datasets-with-the-studio"></a>使用 studio 建立資料集

您也可以使用[Azure Machine Learning studio](https://ml.azure.com)，從開啟的資料集建立資料集。

1. 在工作區中，選取 [**資產**] 底下的 [**資料集**] 索引標籤。 在 [**建立資料集**] 下拉式功能表中，選取 [**從開啟的資料集**]。

    ![使用 UI 開啟資料集](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. 選取資料集，選取其磚。  (您可以選擇使用 [搜尋] 列進行篩選。 ) 選取 **[下一步]**。

    ![選擇資料集](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. 選擇用來註冊資料集的名稱，並選擇性地使用可用的篩選準則來篩選資料。 在此情況下，針對**公用假日**資料集，您可以將時間週期篩選為僅限一年，並將國家/地區代碼篩選為美國。 選取 [建立]。

    ![設定資料集參數並建立資料集](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    資料集現在可在您的工作區中的 [**資料集**] 下取得。 您可以使用與您所建立的其他資料集相同的方式來使用它。


## <a name="access-datasets-for-your-experiments"></a>存取您實驗的資料集

在您的機器學習實驗中使用您的資料集來定型 ML 模型。 [深入了解如何使用資料集進行定型](../machine-learning/how-to-train-with-datasets.md)。

## <a name="example-notebooks"></a>Notebook 範例

如需開放資料集功能的範例和示範，請參閱這些[筆記本](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials)。

## <a name="next-steps"></a>後續步驟

* [使用資料集來定型模型](../machine-learning/how-to-train-with-datasets.md)。

* [建立 Azure machine learning 資料集](../machine-learning/how-to-create-register-datasets.md)。



