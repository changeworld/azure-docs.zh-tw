---
title: 執行 Python 腳本：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [執行 Python 腳本] 模組來執行 Python 程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python
author: likebupt
ms.author: keli19
ms.date: 10/21/2020
ms.openlocfilehash: e6a7eabec76cf27044b5d0e13acfc2431cb19b77
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323766"
---
# <a name="execute-python-script-module"></a>執行 Python 腳本模組

本文說明 Azure Machine Learning 設計工具中的「執行 Python 腳本」模組。

使用此模組來執行 Python 程式碼。 如需 Python 架構和設計原則的詳細資訊，請參閱 [如何在 Azure Machine Learning 設計工具中執行 python 程式碼](../how-to-designer-python.md)。

使用 Python，您可以執行現有模組不支援的工作，例如：

+ 使用將資料視覺化 `matplotlib` 。
+ 使用 Python 程式庫來列舉您工作區中的資料集和模型。
+ 從匯 [入資料](./import-data.md) 模組不支援的來源讀取、載入及運算元據。
+ 執行您自己的深度學習程式碼。 

## <a name="supported-python-packages"></a>支援的 Python 套件

Azure Machine Learning 使用 Python 的 Anaconda 散發，其中包含許多常用來處理資料的公用程式。 我們會自動更新 Anaconda 版本。 目前的版本為：
 -  適用于 Python 3.6 的 Anaconda 4.5 + 散發套件 

如需完整清單，請參閱 [預先安裝的 Python 套件](#preinstalled-python-packages)一節。

若要安裝不在預先安裝清單中的套件 (例如 *scikit-learn-其他* ) ，請將下列程式碼新增至您的腳本： 

```python
import os
os.system(f"pip install scikit-misc")
```

您可以使用下列程式碼來安裝封裝，以取得更好的效能，特別是針對推斷：
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> 如果您的管線包含多個執行 Python 腳本模組，而這些模組需要的套件不在預先安裝的清單中，請在每個模組中安裝套件。

> [!WARNING]
> Excute Python 腳本模組不支援安裝相依于額外原生程式庫的套件，其具有像是 "apt-get" 的命令，例如 JAVA、PyODBC 等等。這是因為此模組是在只預先安裝 Python 且具有非系統管理員許可權的簡單環境中執行。  

## <a name="upload-files"></a>上傳檔案
[執行 Python 腳本] 模組支援使用 [Azure Machine Learning PYTHON SDK](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#upload-file-name--path-or-stream-)上傳檔案。

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

管線執行完成之後，您可以在模組的右面板中預覽映射。

> [!div class="mx-imgBorder"]
> ![已上傳影像的預覽](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>如何設定執行 Python 腳本

[執行 Python 腳本] 模組包含可作為起點的範例 Python 程式碼。 若要設定 [執行 Python 腳本] 模組，請提供一組要在 [ **Python 腳本** ] 文字方塊中執行的輸入和 Python 程式碼。

1. 將 [ **執行 Python 腳本** ] 模組新增至您的管線。

2. 從您想要用於輸入的設計工具中，新增並連接 **Dataset1** 任何資料集。 在您的 Python 腳本中，以 **DataFrame1** 的形式參考此資料集。

    資料集的使用是選擇性的。 如果您想要使用 Python 產生資料，或使用 Python 程式碼將資料直接匯入模組，請使用此方法。

    此模組支援在 **Dataset2** 上新增第二個資料集。 以 **DataFrame2** 的形式參考 Python 腳本中的第二個資料集。

    使用此模組載入時，儲存在 Azure Machine Learning 中的資料集會自動轉換成 pandas 資料框架。

    ![執行 Python 輸入對應](media/module/python-module.png)

4. 若要包含新的 Python 套件或程式碼，請將包含這些自訂資源的壓縮檔案連接至 **腳本** 組合埠。 或者，如果您的腳本大於 16 KB，請使用 **腳本** 套件組合埠來避免像 *命令列一樣的錯誤超過16597個字元的限制* 。 

    
    1. 將腳本和其他自訂資源組合成 zip 檔案。
    1. 將 zip 檔案以檔案 **資料集** 的形式上傳至 studio。 
    1. 從 [設計師撰寫] 頁面左側模組窗格中的 [ *資料集* ] 清單，拖曳資料集模組。 
    1. 將資料集模組連接至 [ **執行 R 腳本** ] 模組的 **腳本** 組合埠。
    
    在管線執行期間，可以使用上傳的 zip 封存中包含的任何檔案。 如果封存包含目錄結構，則會保留結構。
 
    > [!WARNING]
    > **請勿** 使用 **應用程式** 作為資料夾或您的腳本名稱，因為 **應用程式** 是內建服務的保留字。 但是您可以使用其他命名空間，例如 `app123` 。
   
    以下是腳本套件組合範例，其中包含 python 腳本檔案和 txt 檔案：
      
    > [!div class="mx-imgBorder"]
    > ![腳本套件組合範例](media/module/python-script-bundle.png)  

    以下是的內容 `my_script.py` ：

    ```python
    def my_func(dataframe1):
    return dataframe1
    ```
    以下是示範如何在腳本套件組合中使用檔案的範例程式碼：    

    ```python
    import pandas as pd
    from my_script import my_func
 
    def azureml_main(dataframe1 = None, dataframe2 = None):
 
        # Execution logic goes here
        print(f'Input pandas.DataFrame #1: {dataframe1}')
 
        # Test the custom defined python function
        dataframe1 = my_func(dataframe1)
 
        # Test to read custom uploaded files by relative path
        with open('./Script Bundle/my_sample.txt', 'r') as text_file:
            sample = text_file.read()
    
        return dataframe1, pd.DataFrame(columns=["Sample"], data=[[sample]])
    ```

5. 在 [ **Python 腳本** ] 文字方塊中，輸入或貼上有效的 Python 腳本。

    > [!NOTE]
    >  撰寫腳本時請務必小心。 請確定沒有語法錯誤，例如使用未宣告的變數或未匯入模組或函數。 請特別注意預先安裝的模組清單。 若要匯入未列出的模組，請在您的腳本中安裝對應的封裝，例如：
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    **Python 腳本** 文字方塊已預先填入批註中的一些指示，以及資料存取和輸出的範例程式碼。 您必須編輯或取代此程式碼。 遵循適用于縮排和大小寫的 Python 慣例：

    + 腳本必須包含名 `azureml_main` 為此模組的進入點的函式。
    + 輸入點函式必須有兩個輸入引數， `Param<dataframe1>` 而且 `Param<dataframe2>` 即使您的腳本中未使用這些引數，也是如此。
    + 連接到第三個輸入埠的壓縮檔會解壓縮並儲存在目錄中 `.\Script Bundle` ，也會新增至 Python `sys.path` 。 

    如果您的 .zip 檔案包含 `mymodule.py` ，請使用將它匯入 `import mymodule` 。

    您可以將兩個資料集傳回給設計工具，這必須是型別的序列 `pandas.DataFrame` 。 您可以在 Python 程式碼中建立其他輸出，並將它們直接寫入 Azure 儲存體。

    > [!WARNING]
    > **不** 建議您在 **執行 Python 腳本模組** 中連接到資料庫或其他外部儲存體。 您可以使用匯 [入資料模組](./import-data.md) 和 [匯出資料模組](./export-data.md)     

6. 提交管線。

    所有的資料和程式碼都會載入至虛擬機器，並使用指定的 Python 環境執行。

## <a name="results"></a>結果

內嵌 Python 程式碼所做的任何計算結果都必須提供為 `pandas.DataFrame` ，以自動轉換成 Azure Machine Learning 資料集格式。 然後，您可以將結果與管線中的其他模組搭配使用。

此模組會傳回兩個資料集：  
  
+ **結果資料集 1** ，由 Python 腳本中第一個傳回的 pandas 資料框架所定義。

+ **結果資料集 2** ，在 Python 腳本中由第二個傳回的 pandas 資料框架所定義。

## <a name="preinstalled-python-packages"></a>預先安裝的 Python 套件
預先安裝的套件為：
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
-    azureml-dataprep-原生 = = 14.1。0
-    azureml-dataprep = = 1.3。5
-    azureml-預設值 = = 1.1.5。1
-    azureml-設計工具-傳統模組 = = 0.0.118
-    azureml-設計工具-核心 = = 0.0.31
-    azureml-設計工具-internal = = 0.0.18
-    azureml-模型-管理-sdk = = 1.0.1 b6. post1
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
-    google-雲端-核心 = = 1.3。0
-    google-雲端-儲存體 = = 1.26。0
-    google 可繼續-媒體 = = 0.5。0
-    >www.googleapis.com-common-protos = = 1.51。0
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
-    >jsonschema.net = = 3.0。1
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
-    pyopenssl = = 19.1.0 版
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
-    6 = = 1.14。0
-    smart-open = = 1.10。0
-    urllib3 = = 1.25。8
-    websocket-用戶端 = = 0.57。0
-    werkzeug = = 0.16。1
-    輪子 = = 0.34。2

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。