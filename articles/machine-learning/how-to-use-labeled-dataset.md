---
title: 使用標籤創建和流覽資料集
titleSuffix: Azure Machine Learning
description: 瞭解如何從 Azure 機器學習標記專案匯出資料標籤，並將其用於機器學習任務。
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549485"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>使用標籤創建和流覽 Azure 機器學習資料集

在本文中，您將學習如何從 Azure 機器學習資料標記專案匯出資料標籤並將其載入到流行的格式，例如用於資料探索的熊貓資料框或用於圖像轉換的 Torchvision 資料集。 

## <a name="what-are-datasets-with-labels"></a>什麼是帶標籤的資料集 

具有標籤的 Azure 機器學習資料集是具有標籤屬性[的表格資料集](how-to-create-register-datasets.md#dataset-types)，我們將它們稱為標記資料集。 這些特定類型的表格資料集僅作為 Azure 機器學習資料標記專案的輸出創建。 使用[這些步驟](how-to-create-labeling-projects.md)創建資料標記專案。 機器學習支援圖像分類（多標籤或多類）的資料標記專案，以及物件標識以及邊界框。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果沒有 Azure 訂閱，請先創建[一個免費帳戶](https://aka.ms/AMLFree)。"
* 用於 Python 的[Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或訪問 Azure[機器學習工作室](https://ml.azure.com/)。
    * 安裝[azure-contrib 資料集](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)包
* Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* 訪問 Azure 機器學習資料標記專案。 如果沒有標記專案，請使用[這些步驟](how-to-create-labeling-projects.md)創建一個 。

## <a name="export-data-labels"></a>匯出資料標籤 

完成資料標記專案時，可以從標籤專案匯出標籤資料。 這樣，可以捕獲對資料的引用及其標籤，然後以[COCO 格式](http://cocodataset.org/#format-data)或 Azure 機器學習資料集匯出它們。 在標籤專案的 [專案詳細資料]**** 頁面上使用 [匯出]**** 按鈕。

### <a name="coco"></a>可哥 

 COCO 檔案會建立在 Azure Machine Learning 工作區的預設 Blob 存放區中，而其所在的資料夾位於 *export/coco* 內。 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning 資料集

您可以在 Azure 機器學習工作室的 **"資料集"** 部分訪問匯出的 Azure 機器學習資料集。 資料集**詳細資訊**頁還提供示例代碼，以便從 Python 訪問標籤。

![所匯出的資料集](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>流覽標記的資料集

將標記的資料集載入到熊貓資料框或 Torchvision 資料集中，以利用流行的開源庫進行資料探索，PyTorch 提供用於圖像轉換和培訓的庫。

### <a name="pandas-dataframe"></a>Pandas 資料框架

您可以使用類[`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--)中的方法將標記的資料集載入到熊貓資料框中`azureml-contrib-dataset`。 使用以下 shell 命令安裝類： 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>azureml.contrib 命名空間經常更改，因為我們正在改進服務。 因此，此命名空間中的任何項目均應被視為預覽，而且 Microsoft 不會完全支援。

在轉換為熊貓資料框時，我們為檔流提供了以下檔處理選項。
* 下載：將資料檔案下載到本地路徑。
* 裝載：將資料檔案裝載到裝載點。 裝載僅適用于基於 Linux 的計算，包括 Azure 機器學習筆記本 VM 和 Azure 機器學習計算。

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

### <a name="torchvision-datasets"></a>火炬視覺資料集

您可以使用[to_torchvision（）](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--)方法從`azureml-contrib-dataset`類將標記的資料集載入到 Torchvision 資料集中。 要使用此方法，您需要安裝[PyTorch。](https://pytorch.org/) 

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

* 有關完整的培訓示例，請參閱[帶有標籤筆記本的資料集](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)。
