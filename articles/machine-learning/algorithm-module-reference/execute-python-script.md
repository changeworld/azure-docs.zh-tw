---
title: 執行 Python 文稿:模組參照
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用執行 Python 文本模組來運行 Python 代碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 79dc1b188e91028a98f43dc24972228f2d2101be
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684737"
---
# <a name="execute-python-script-module"></a>執行 Python 文稿模組

本文介紹 Azure 機器學習設計器(預覽)中的模組。

使用此模組執行 Python 代碼。 有關 Python 的架構結構和設計原則的詳細資訊,請參閱[以下文章](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)。

使用 Python,您可以執行現有模組目前不支援的任務,例如:

+ 使用視覺化資料`matplotlib`
+ 使用 Python 函式庫列舉工作區中的資料集和模型
+ 從[匯入資料](./import-data.md)模組不支援的來源讀取、載入和操作資料
+ 執行您自己的深度學習碼 


Azure 機器學習使用 Python 的 Anaconda 分佈,其中包括許多用於數據處理的常用實用程式。 我們將自動更新 Anaconda 版本。 目前版本是:
 -  適用於 Python 3.6 的 Anaconda 4.5+ 發行版 

預先安裝的套件包括:
-    adal=1.2.2
-    應用程式見解=0.11.9
-    attrs=19.3.0
-    azure 公共 =1.1.25
-    azure 核心 =1.3.0
-    azure-圖形桿菌=0.61.1
-    azure 標識=1.3.0
-    azure-mgmt 授權=0.60.0
-    azure-mgmt-容器註冊=2.8.0
-    azure-mgmt-鍵保管=2.2.0
-    azure-mgmt 資源=8.0.1
-    azure-mgmt 存儲=8.0.0
-    azure 儲存 blob=1.5.0
-    azure 儲存 -公共=1.4.2
-    azureml-core=1.1.5.5
-    azureml 資料準備-本機 =14.1.0
-    azureml 資料準備=1.3.5
-    azureml 預設值=1.1.5.1
-    azureml-設計器-經典模組=0.0.118
-    azureml-designer-core=0.0.31
-    azureml-設計器內部=0.0.18
-    azureml 模型管理-sdk=1.0.1b6.post1
-    azureml-管道-核心=1.1.5
-    azureml 遙測=1.1.5.3
-    後埠.tempfile=1.0
-    後埠.weakref=1.0.post1
-    博托3=1.12.29
-    博托科=1.15.29
-    快取工具=4.0.0
-    證書=2019.11.28
-    cffi=1.12.3
-    字元 =3.0.4
-    按下=7.1.1
-    雲片=1.3.0
-    設定器=3.7.4
-    上下文lib2=0.6.0.後1
-    密碼學=2.8
-    循環器=0.10.0
-    迪爾=0.3.1.1
-    分約=1.4.0
-    docker=4.2.0
-    多庫蒂爾=0.15.2
-    點網芯2=2.1.13
-    燒瓶=1.0.3
-    保險絲=3.0.1
-    gensim=3.8.1
-    谷歌-api-core=1.16.0
-    google-auth=1.12.0
-    谷歌雲核心=1.3.0
-    google 雲存儲 =1.26.0
-    谷歌可恢復媒體=0.5.0
-    googleapis-公共原型=1.51.0
-    古尼科恩=19.9.0
-    伊德納=2.9
-    不平衡學習=0.4.3
-    等日期=0.6.0
-    其危險=1.1.0
-    吉普尼=0.4.3
-    jinja2=2.11.1
-    jmespath=0.9.5
-    joblib=0.14.0
-    json-日誌記錄-py=0.2
-    jsonpickle=1.3
-    jsonschema=3.0.1
-    奇異器=1.1.0
-    利亞克-阿爾夫=2.4.0
-    光gbm=2.2.3
-    標記安全 =1.1.1
-    墊子利布=3.1.3
-    更多 itertools=6.0.0
-    msal 延伸=0.1.3
-    msal=1.1.0
-    msrest=0.6.11
-    msrestazure=0.6.3
-    ndg-Htttttt
-    尼姆布姆=1.6.1
-    數位=1.18.2
-    奧思利布=3.1.0
-    熊貓=0.25.3
-    路徑規格=0.7.0
-    點 =20.0.2
-    波特洛克=1.6.0
-    普羅托布夫=3.11.3
-    pyarrow=0.16.0
-    pyasn1-模組=0.2.8
-    pyasn1=0.4.8
-    除蟲器=2.20
-    皮西多姆克斯=3.7.3
-    pyjwt=1.7.1
-    pyopenssl=19.1.0
-    除位 =2.4.6
-    熱西滕=0.16.0
-    python-dateutil=2.8.1
-    pytz_2019.3
-    請求-奧阿特利布=1.3.0
-    請求=2.23.0
-    rsa=4.0
-    魯阿梅爾.亞姆爾=0.15.89
-    s3傳輸=0.3.3
-    學學習=0.22.2
-    奇皮=1.4.1
-    秘密存儲=3.1.2
-    設定工具=46.1.1.後20200323
-    六=1.14.0
-    智慧打開=1.10.0
-    urllib3=1.25.8
-    websocket 用戶端=0.57.0
-    韋克澤古=0.16.1
-    車輪=0.34.2

 要安裝未在預安裝清單中的其他套件(例如*scikit-misc),* 請加入文稿加入以下代碼: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>上傳檔案
**執行 Python 文稿**支援使用 Azure 機器學習 Python [SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-)上傳檔。

下面的範例展示如何在**執行 Python 文稿**模組中上傳影像檔:

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
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
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

導管執行完成後,您可以在模組的右邊面板中預覽影像

> [!div class="mx-imgBorder"]
> ![上傳影像](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>如何設定執行 Python 文稿

**執行 Python 文稿**模組包含可用於起點的範例 Python 代碼。 要設定**執行 Python 文稿**模組,請提供一組輸入和 Python 代碼,用於在 Python**文本**文字框中執行。

1. 將**執行 Python 文本**模組添加到管道中。

2. 在**Dataset1**上添加和連接要用於輸入的設計器的任何數據集。 在 Python 文稿中參考此資料集為**資料的幀1**。

    如果要使用 Python 生成數據,或者使用 Python 代碼將數據直接導入模組,則使用數據集是可選的。

    此模組支援在**數據集 2**上添加第二個數據集。 將 Python 文稿中的第二個資料集引用為 DataFrame2。

    Azure 機器學習中存儲的數據集在載入此模組時會自動轉換為**熊貓**資料。"幀"。

    ![執行 Python 輸入對應](media/module/python-module.png)

4. 要包括新的 Python 包或代碼,請在**文本捆綁包**上添加包含這些自定義資源的壓縮檔。 **文本捆綁包**的輸入必須是作為檔案類型數據集上載到工作區的壓縮檔。 您可以在 **「資料集」** 資產頁中上傳資料集,還可以從設計器創作頁中左側模組樹中的 **「我的數據集」** 清單中拖放資料集模組。 

    上載的壓縮存檔中包含的任何檔都可以在管道執行期間使用。 如果存檔包含目錄結構,則保留該結構,但必須預置名為**src**的目錄到路徑。

5. 在**Python 文稿**文字框中,鍵入或貼上有效的 Python 文稿。

    > [!NOTE]
    > 編寫文本時請非常小心,並確保沒有語法錯誤,例如使用未聲明的物件或未導入的模組。 還要特別注意預安裝的模組清單。 要匯入未列出的模組,請在文本中安裝相應的包,例如
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    **Python 文本**文字框預先填充了註解中的一些說明,以及用於數據訪問和輸出的範例代碼。 您必須編輯或替換此代碼。 請務必遵循有關縮進和套管的 Python 約定。

    + 文本必須包含一個稱為`azureml_main`此模組的入口點的函數。
    + 入口點函數必須具有兩個輸入參數:`Param<dataframe1>``Param<dataframe2>`和 ,即使腳本中不使用這些參數也是如此。
    + 連接到第三個輸入連接埠的壓縮檔案將解壓縮並儲存在目錄中,`.\Script Bundle`該目錄也將添加到 Python`sys.path`中。 

    因此,如果 zip`mymodule.py`檔包含`import mymodule`,請使用 匯入它。

    + 兩個數據集可以返回到設計器,它必須是類型`pandas.DataFrame`序列。 您可以在 Python 代碼中創建其他輸出,並將它們直接寫入 Azure 儲存。

6. 提交導管,或選擇模組,然後按下 **「執行」** 僅運行 Python 文稿。

    所有數據和代碼都載入到虛擬機器中,並使用指定的 Python 環境執行。

## <a name="results"></a>結果

嵌入的 Python 代碼執行的任何計算的結果必須作為熊貓提供。DataFrame,它會自動轉換為 Azure 機器學習數據集格式,以便您可以將結果與管道中的其他模組一起使用。

該模組傳回兩個資料集:  
  
+ **結果資料集 1,** 由 Python 文稿中第一個傳回的熊貓資料框定義

+ **結果資料集 2,** 由 Python 文稿中第二個傳回的熊貓資料幀定義


## <a name="next-steps"></a>後續步驟

請參考 Azure 機器學習[可用的模組集](module-reference.md)。 