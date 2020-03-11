---
title: 執行 Python 腳本：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [執行 Python 腳本] 模組來執行 Python 程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 52eb3bdb463389d075421661610b5ee94d14d77d
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037064"
---
# <a name="execute-python-script-module"></a>執行 Python 腳本模組

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用此模組來執行 Python 程式碼。 如需有關 Python 架構和設計原則的詳細資訊，請參閱[下列文章](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)。

使用 Python，您可以執行現有模組目前不支援的工作，例如：

+ 使用 `matplotlib` 將資料視覺化
+ 使用 Python 程式庫來列舉工作區中的資料集和模型
+ 讀取、載入及操作匯[入資料](./import-data.md)模組不支援之來源的資料
+ 執行您自己的深入學習程式碼 


Azure Machine Learning 使用 Python 的 Anaconda 散發，其中包含許多常用的資料處理公用程式。 我們會自動更新 Anaconda 版本。 目前的版本為：
 -  適用于 Python 3.6 的 Anaconda 4.5 + 散發 

預先安裝的套件包括：
-  asn1crypto = = 0.24。0
- attrs = = 19.1。0
- azure-common = = 1.1.18
- azure-儲存體-blob = = 1.5。0
- azure-儲存體-common = = 1.4。0
- certifi = = 2019.3。9
- cffi = = 1.12。2
- chardet = = 3.0。4
- 密碼編譯 = = 2.6。1
- 散發版本 = = 1.4。0
- idna = = 2。8
- jsonschema = = 3.0。1
- lightgbm = = 2.2。3
- itertools = = 6.0。0
- numpy = = 1.16。2
- pandas = = 0.24。2
- Pillow = = 6.0。0
- pip = = 19.0。3
- pyarrow = = 0.12.1 從
- pycparser = = 2.19
- pycryptodomex = = 3.7。3
- pyrsistent = = 0.14.11
- python-dateutil = = 2.8。0
- pytz = = 2018。9
- 要求 = = 2.21。0
- scikit-learn-學習 = = 0.20。3
- scipy = = 1.2。1
- setuptools = = 40.8。0
- 六 = = 1.12。0
- torch = = 1.0.1. post2
- torchvision = = 0.2.2. post3
- urllib3 = = 1.24。1
- 滾輪 = = 0.33。1 

 若要安裝不在預先安裝清單中的其他套件（例如*scikit-learn*），請將下列程式碼新增至您的腳本： 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>上傳檔案
**執行 Python 腳本**支援使用[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-)上傳檔案。

下列範例顯示如何在**執行 Python 腳本**模組中上傳影像檔案：

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

成功提交管線之後，您可以在模組的右面板中預覽影像 ![上傳影像](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>如何設定執行 Python 腳本

**執行 Python 腳本**模組包含範例 Python 程式碼，可供您做為起點。 若要設定**執行 Python 腳本**模組，請在 [ **Python 腳本**] 文字方塊中提供一組要執行的輸入和 Python 程式碼。

1. 將**執行 Python 腳本**模組新增至您的管線。

2. 從您想要用於輸入的設計工具，在**Dataset1**的任何資料集上加入及連接。 在您的 Python 腳本中，以**DataFrame1**的形式參考此資料集。

    如果您想要使用 Python 產生資料，或使用 Python 程式碼將資料直接匯入模組，則可以選擇使用資料集。

    此模組支援在**Dataset2**上新增第二個資料集。 以 DataFrame2 的形式參考 Python 腳本中的第二個資料集。

    儲存在 Azure Machine Learning 中的資料集會自動轉換成**pandas**的資料。當此模組載入時，畫面格。

    ![執行 Python 輸入對應](media/module/python-module.png)

4. 若要包含新的 Python 套件或程式碼，請在**腳本**組合上新增包含這些自訂資源的 zip 壓縮檔案。 **腳本**組合的輸入必須是以檔案類型資料集的形式上傳到您工作區的 zip 壓縮檔案。 

    在管線執行期間，您可以使用上傳的壓縮封存中包含的任何檔案。 如果封存包含目錄結構，則會保留結構，但是您必須在路徑前面加上名為**src**的目錄。

5. 在 [ **Python 腳本**] 文字方塊中，輸入或貼上有效的 Python 腳本。

    [ **Python 腳本**] 文字方塊會預先填入批註中的一些指示，以及用於資料存取和輸出的範例程式碼。 您必須編輯或取代此程式碼。 請務必遵循適用于縮排和大小寫的 Python 慣例。

    + 腳本必須包含名為 `azureml_main` 的函式，做為此模組的進入點。
    + 進入點函數最多可包含兩個輸入引數： `Param<dataframe1>` 和 `Param<dataframe2>`
    + 連線到第三個輸入埠的壓縮檔案會解壓縮並儲存在目錄中，`.\Script Bundle`，這也會新增至 Python `sys.path`。 

    因此，如果您的 zip 檔案包含 `mymodule.py`，請使用 `import mymodule`將其匯入。

    + 您可以將兩個資料集傳回至設計工具，這必須是 `pandas.DataFrame`類型的序列。 您可以在 Python 程式碼中建立其他輸出，並直接將其寫入 Azure 儲存體。

6. 執行管線，或選取模組，然後按一下 [**執行選取**]，只執行 Python 腳本。

    所有的資料和程式碼都會載入至虛擬機器，並使用指定的 Python 環境執行。

## <a name="results"></a>結果

內嵌 Python 程式碼所執行的任何計算結果，都必須以 pandas 的形式提供。資料框架，它會自動轉換成 Azure Machine Learning 資料集格式，讓您可以將結果與管線中的其他模組搭配使用。

此模組會傳回兩個資料集：  
  
+ **結果資料集 1**，由 Python 腳本中第一個傳回的 pandas 資料框架所定義

+ 在 Python 腳本中，由第二個傳回的 pandas 資料框架所定義的**結果資料集 2**


## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 