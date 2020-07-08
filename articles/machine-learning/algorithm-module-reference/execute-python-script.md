---
title: 執行 Python 腳本：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [執行 Python 腳本] 模組來執行 Python 程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: tracking-python
author: likebupt
ms.author: keli19
ms.date: 06/16/2020
ms.openlocfilehash: f64c79a970ec54c07c2934a92a9ca349ea56ca40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907545"
---
# <a name="execute-python-script-module"></a>執行 Python 腳本模組

本文說明 Azure Machine Learning 設計工具（預覽）中的 [執行 Python 腳本] 模組。

使用此模組來執行 Python 程式碼。 如需有關 Python 架構和設計原則的詳細資訊，請參閱[這篇文章](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)。

使用 Python，您可以執行現有模組不支援的工作，例如：

+ 使用將資料視覺化 `matplotlib` 。
+ 使用 Python 程式庫來列舉工作區中的資料集和模型。
+ 從匯[入資料](./import-data.md)模組不支援的來源讀取、載入和運算元據。
+ 執行您自己的深入學習程式碼。 


Azure Machine Learning 使用 Python 的 Anaconda 散發，其中包含許多常用的資料處理公用程式。 我們會自動更新 Anaconda 版本。 目前的版本為：
 -  適用于 Python 3.6 的 Anaconda 4.5 + 散發 

預先安裝的套件包括：
-    adal==1.2.2
-    applicationinsights==0.11.9
-    attrs = = 19.3。0
-    azure-common = = 1.1.25
-    azure-核心 = = 1.3。0
-    azure-graphrbac==0.61.1
-    azure-identity = = 1.3。0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    azure-管理-keyvault = = 2.2。0
-    azure-管理-資源 = = 8.0。1
-    azure-管理-儲存體 = = 8.0。0
-    azure-儲存體-blob = = 1.5。0
-    azure-儲存體-通用 = = 1.4。2
-    azureml-核心 = = 1.1.5。5
-    azureml-dataprep-native = = 14.1。0
-    azureml-dataprep = = 1.3。5
-    azureml-預設值 = = 1.1.5。1
-    azureml-設計工具-傳統模組 = = 0.0.118
-    azureml-設計工具-核心 = = 0.0.31
-    azureml-設計工具-內部 = = 0.0.18
-    azureml-模型管理-sdk = = 1.0.1 b6. post1
-    azureml-管線-核心 = = 1.1。5
-    azureml-遙測 = = 1.1.5。3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0。0
-    certifi = = 2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    按一下 = = 7.1。1
-    clouDPIckle = = 1.3。0
-    configparser = = 3.7。4
-    coNtextlib2 = = 0.6.0. post1
-    密碼編譯 = = 2。8
-    cycler==0.10.0
-    dill==0.3.1.1
-    distro==1.4.0
-    docker = = 4.2。0
-    docutils==0.15.2
-    dotnetcore2 = = 2.1.13
-    flask = = 1.0。3
-    fusepy==3.0.1
-    gensim==3.8.1
-    google-api-核心 = = 1.16。0
-    google-auth = = 1.12。0
-    google-雲端核心 = = 1.3。0
-    google-雲端-儲存體 = = 1.26。0
-    google 可繼續-media = = 0.5。0
-    googleapis-common-protos = = 1.51。0
-    gunicorn = = 19.9。0
-    idna = = 2。9
-    不平衡-學習 = = 0.4。3
-    isodate==0.6.0
-    itsdangerous = = 1.1。0
-    jeepney = = 0.4。3
-    jinja2 = = 2.11。1
-    jmespath = = 0.9。5
-    joblib = = 0.14。0
-    json-記錄-.py = = 0。2
-    jsonpickle = = 1。3
-    jsonschema = = 3.0。1
-    kiwisolver==1.1.0
-    liac-arff = = 2.4。0
-    lightgbm==2.2.3
-    markupsafe = = 1.1。1
-    matplotlib = = 3.1。3
-    itertools = = 6.0。0
-    msal-extensions = = 0.1。3
-    msal = = 1.1。0
-    msrest = = 0.6.11
-    msrestazure = = 0.6。3
-    ndg-httpsclient==0.5.1
-    nimbusml = = 1.6。1
-    numpy = = 1.18。2
-    oauthlib==3.1.0
-    pandas = = 0.25。3
-    pathspec = = 0.7。0
-    pip = = 20.0。2
-    portalocker = = 1.6。0
-    protobuf = = 3.11。3
-    pyarrow = = 0.16。0
-    pyasn1-模組 = = 0.2。8
-    pyasn1 = = 0.4。8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7。3
-    pyjwt = = 1.7。1
-    pyopenssl = = 19.1。0
-    pyparsing = = 2.4。6
-    pyrsistent = = 0.16。0
-    python-dateutil = = 2.8。1
-    pytz==2019.3
-    要求-oauthlib = = 1.3。0
-    要求 = = 2.23。0
-    rsa = = 4。0
-    ruamel.yaml==0.15.89
-    s3transfer = = 0.3。3
-    scikit-learn-學習 = = 0.22。2
-    scipy = = 1.4。1
-    secretstorage = = 3.1。2
-    setuptools = = 46.1.1. post20200323
-    六 = = 1.14。0
-    智慧型-開啟 = = 1.10。0
-    urllib3 = = 1.25。8
-    websocket-client = = 0.57。0
-    werkzeug = = 0.16。1
-    滾輪 = = 0.34。2

 若要安裝不在預先安裝清單中的封裝（例如， *scikit-learn-其他*），請將下列程式碼新增至您的腳本： 

 ```python
import os
os.system(f"pip install scikit-misc")
```

使用下列程式碼來安裝封裝以獲得更好的效能，特別是針對推斷：
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> 如果您的管線包含多個執行 Python 腳本模組，而這些模組需要不在預先安裝清單中的封裝，請在每個模組中安裝套件。

## <a name="upload-files"></a>上傳檔案
執行 Python 腳本模組支援使用[Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-)上傳檔案。

下列範例顯示如何在執行 Python 腳本模組中上傳影像檔案：

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
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
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

管線執行完成之後，您可以在模組的右面板中預覽影像。

> [!div class="mx-imgBorder"]
> ![已上傳影像的預覽](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>如何設定執行 Python 腳本

執行 Python 腳本模組包含範例 Python 程式碼，可供您做為起點。 若要設定執行 Python 腳本模組，請在 [ **Python 腳本**] 文字方塊中提供要執行的一組輸入和 Python 程式碼。

1. 將**執行 Python 腳本**模組新增至您的管線。

2. 從您想要用於輸入的設計工具，在**Dataset1**的任何資料集上加入及連接。 在您的 Python 腳本中，以**DataFrame1**的形式參考此資料集。

    資料集的使用是選擇性的。 如果您想要使用 Python 產生資料，或使用 Python 程式碼將資料直接匯入模組，請使用此方法。

    此模組支援在**Dataset2**上新增第二個資料集。 以**DataFrame2**的形式參考 Python 腳本中的第二個資料集。

    使用此模組載入時，會自動將儲存在 Azure Machine Learning 中的資料集轉換成 pandas 的資料框架。

    ![執行 Python 輸入對應](media/module/python-module.png)

4. 若要包含新的 Python 套件或程式碼，請在**腳本**組合上新增包含這些自訂資源的 zip 壓縮檔案。 **腳本**組合的輸入必須是以檔案類型資料集的形式上傳到您工作區的 zip 壓縮檔案。 您可以在 [**資料集**資產] 頁面上上傳資料集。 您可以從左側模組樹狀結構的 [**我的資料集**] 清單中，將資料集模組拖曳至 [設計工具撰寫] 頁面。 

    在管線執行期間，您可以使用上傳的壓縮封存中包含的任何檔案。 如果封存包含目錄結構，則會保留結構，但是您必須在路徑前面加上名為**src**的目錄。

5. 在 [ **Python 腳本**] 文字方塊中，輸入或貼上有效的 Python 腳本。

    > [!NOTE]
    >  撰寫腳本時請小心。 請確定沒有任何語法錯誤，例如使用未宣告的變數或未匯入模組或函數。 請特別注意預先安裝的模組清單。 若要匯入未列出的模組，請在您的腳本中安裝對應的套件，例如：
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    [ **Python 腳本**] 文字方塊已預先填入批註中的一些指示，以及用於資料存取和輸出的範例程式碼。 您必須編輯或取代此程式碼。 遵循適用于縮排和大小寫的 Python 慣例：

    + 腳本必須包含名為的函式， `azureml_main` 做為此模組的進入點。
    + 進入點函式必須有兩個輸入引數， `Param<dataframe1>` 和 `Param<dataframe2>` ，即使您的腳本中未使用這些引數也一樣。
    + 連線到第三個輸入埠的壓縮檔案會解壓縮並儲存在目錄中 `.\Script Bundle` ，也會新增至 Python `sys.path` 。 

    如果您的 .zip 檔案包含 `mymodule.py` ，請使用將它匯入 `import mymodule` 。

    您可以將兩個資料集傳回至設計工具，這必須是類型的序列 `pandas.DataFrame` 。 您可以在 Python 程式碼中建立其他輸出，並直接將其寫入 Azure 儲存體。

6. 提交管線，或選取模組並選取 [**執行選取**]，只執行 Python 腳本。

    所有的資料和程式碼都會載入至虛擬機器，並使用指定的 Python 環境執行。

## <a name="results"></a>結果

內嵌 Python 程式碼所進行的任何計算結果，都必須以的形式提供 `pandas.DataFrame` ，這會自動轉換成 Azure Machine Learning 的資料集格式。 然後，您可以將結果與管線中的其他模組搭配使用。

此模組會傳回兩個資料集：  
  
+ **結果資料集 1**，由 Python 腳本中第一個傳回的 pandas 資料框架所定義。

+ **結果資料集 2**，在 Python 腳本中由第二個傳回的 pandas 資料框架所定義。


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
