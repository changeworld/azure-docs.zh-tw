---
title: 建立及探索具有標籤的資料集
titleSuffix: Azure Machine Learning
description: 了解如何從您的 Azure Machine Learning 標記專案匯出資料標籤，並將其用於機器學習工作。
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 1293534849c98cee51349bbefd3073cc8b94f876
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89647205"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>建立和探索具有標籤的 Azure Machine Learning 資料集

在本文中，您將了解如何從 Azure Machine Learning 資料標記專案中匯出資料標籤，並將其載入常用格式，例如，適用於資料探索的 Pandas 資料框架或用於影像轉換的 Torchvision 資料集。 

## <a name="what-are-datasets-with-labels"></a>什麼是具有標籤的資料集 

我們指的是標籤的 Azure Machine Learning 資料集，加上標籤的資料集。 標記資料集的這些特定資料集類型只會建立為 Azure Machine Learning 資料標記專案的輸出。 以[這些步驟](how-to-create-labeling-projects.md)建立資料標記專案。 Machine Learning 支援用於影像分類的資料標記專案 (多標籤或多類別)，以及搭配使用週框方塊的物體識別。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://aka.ms/AMLFree) 。
* [適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)，或 [Azure Machine Learning Studio](https://ml.azure.com/) 的存取權。
    * 安裝 [azure-contrib-dataset](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py&preserve-view=true) 套件
* Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* Azure Machine Learning 資料標記專案的存取權。 如果您沒有標記專案，請遵循[這些步驟](how-to-create-labeling-projects.md)來加以建立。

## <a name="export-data-labels"></a>匯出資料標籤 

當您完成資料標記專案時，您可以從標記專案匯出標籤資料。 這麼做可讓您同時擷取資料和其標籤的參考，並以 [COCO 格式](http://cocodataset.org/#format-data) 或 Azure Machine Learning 資料集來匯出這些資料。 在標籤專案的 [專案詳細資料] 頁面上使用 [匯出] 按鈕。

### <a name="coco"></a>COCO 

 COCO 檔案會建立在 Azure Machine Learning 工作區的預設 Blob 存放區中，而其所在的資料夾位於 *export/coco* 內。 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning 資料集

您可以在 Azure Machine Learning Studio 的 [資料集] 區段中，存取已匯出的 Azure Machine Learning 資料集。 資料集**詳細資料**頁面也會提供從 Python 存取標籤的程式碼範例。

![所匯出的資料集](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>探索已標記的資料集

將已標記的資料集載入至 Pandas 資料框架或 Torchvision 資料集，以利用熱門的開放原始碼程式庫來進行資料探索，以及利用 PyTorch 提供的程式庫來進行影像轉換和訓練。

### <a name="pandas-dataframe"></a>Pandas 資料框架

您可以使用 [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#&preserve-view=trueto-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 方法，將已標記的資料集從 `azureml-contrib-dataset` 類別載入 Pandas 資料框架。 使用下列殼層命令來安裝類別： 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>azureml.contrib 命名空間會因為我們致力於改善本服務而經常變更。 因此，此命名空間中的任何項目均應被視為預覽，而且 Microsoft 不會完全支援。

轉換成 Pandas 資料框架時，我們會針對檔案串流提供下列檔案處理選項。
* 下載：將您的資料檔案下載至本機路徑。
* 掛接：將您的資料檔案掛接到掛接點。 「掛接」僅適用於以 Linux 為基礎的計算，包括 Azure Machine Learning 筆記本 VM 和 Azure Machine Learning Compute。

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision 資料集

您可以使用 [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#&preserve-view=trueto-torchvision--) 方法，同樣將已標記的資料集從 `azureml-contrib-dataset` 類別載入 Torchvision 資料集。 若要使用此方法，您必須安裝 [PyTorch](https://pytorch.org/)。 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>後續步驟

* 如需完整的訓練範例，請參閱[已標記資料集的筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)。
