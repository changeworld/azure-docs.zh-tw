---
title: 建立及探索具有標籤的資料集
titleSuffix: Azure Machine Learning
description: 瞭解如何從您的 Azure Machine Learning 標籤專案匯出資料標籤，並將其用於機器學習工作。
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76549485"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>使用標籤建立和探索 Azure Machine Learning 資料集

在本文中，您將瞭解如何從 Azure Machine Learning 資料標籤專案中匯出資料標籤，並將其載入至常用的格式，例如，適用于資料探索的 pandas 資料框架或用於影像轉換的 Torchvision 資料集。 

## <a name="what-are-datasets-with-labels"></a>什麼是具有標籤的資料集 

具有標籤的 Azure Machine Learning 資料集是以 label 屬性[TabularDatasets](how-to-create-register-datasets.md#dataset-types) ，我們會將其稱為資料集。 這些特定類型的 TabularDatasets 只會建立為 Azure Machine Learning 資料標籤專案的輸出。 使用[這些步驟](how-to-create-labeling-projects.md)建立資料標籤專案。 Machine Learning 支援將影像分類的資料標記專案、多標籤或多類別，以及物件識別與界限方塊一起使用。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://aka.ms/AMLFree) 。
* [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或[Azure Machine Learning studio](https://ml.azure.com/)的存取權。
    * 安裝[azure contrib-資料集](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)封裝
* Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* Azure Machine Learning 資料標籤專案的存取權。 如果您沒有標籤專案，請使用[下列步驟](how-to-create-labeling-projects.md)建立一個。

## <a name="export-data-labels"></a>匯出資料標籤 

當您完成資料標記專案時，您可以從標記專案匯出標籤資料。 這麼做可讓您同時捕獲資料及其標籤的參考，並以[COCO 格式](http://cocodataset.org/#format-data)或 Azure Machine Learning 資料集來匯出它們。 在標籤專案的 [專案詳細資料]  頁面上使用 [匯出]  按鈕。

### <a name="coco"></a>COCO 

 COCO 檔案會建立在 Azure Machine Learning 工作區的預設 Blob 存放區中，而其所在的資料夾位於 *export/coco* 內。 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning 資料集

您可以在 Azure Machine Learning studio 的 [**資料集**] 區段中，存取匯出的 Azure Machine Learning 資料集。 [Dataset**詳細**資料] 頁面也會提供範例程式碼，以從 Python 存取您的標籤。

![所匯出的資料集](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>探索標示的資料集

將加上標籤的資料集載入至 pandas 資料框架或 Torchvision 資料集，以利用熱門的開放原始碼程式庫來進行資料探索，以及 PyTorch 提供的程式庫來進行影像轉換和定型。

### <a name="pandas-dataframe"></a>Pandas 資料框架

您可以使用[`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) `azureml-contrib-dataset`類別的方法，將加上標籤的資料集載入至 pandas 資料框架。 使用下列 shell 命令安裝類別： 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Contrib 命名空間經常變更，因為我們會進行改善服務的工作。 因此，此命名空間中的任何項目均應被視為預覽，而且 Microsoft 不會完全支援。

轉換成 pandas 資料框架時，我們會提供檔案資料流程的下列檔案處理選項。
* 下載：將您的資料檔案下載至本機路徑。
* 掛接：將您的資料檔案掛接到掛接點。 Mount 僅適用于以 Linux 為基礎的計算，包括 Azure Machine Learning 筆記本 VM 和 Azure Machine Learning Compute。

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

您也可以使用`azureml-contrib-dataset`類別的[to_torchvision （）](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--)方法，將標示的資料集載入 Torchvision 資料集。 若要使用此方法，您必須安裝[PyTorch](https://pytorch.org/) 。 

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

* 如需完整的訓練範例，請參閱[具有標籤筆記本的資料集](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)。
