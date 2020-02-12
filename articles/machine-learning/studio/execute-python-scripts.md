---
title: 執行 Python 指令碼
titleSuffix: ML Studio (classic) - Azure
description: 瞭解如何使用 [執行 Python 腳本] 模組，在 Machine Learning Studio （傳統）實驗和 web 服務中使用 Python 程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: a5512069ec609783d166b8417a7006ec4ab17fe1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153429"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>以 Azure Machine Learning Studio 執行 Python 機器學習服務腳本（傳統）

Python 是許多資料科學家工具櫃中的重要工具。 它用於一般機器學習工作流程的每個階段，包括資料探索、功能解壓縮、模型定型和驗證，以及部署。

本文說明如何使用 [執行 Python 腳本] 模組，在您的 Azure Machine Learning Studio （傳統）實驗和 web 服務中使用 Python 程式碼。

## <a name="using-the-execute-python-script-module"></a>使用執行 Python 腳本模組

Studio （傳統）中的 Python 主要介面是透過[執行 Python 腳本][execute-python-script]模組。 它最多接受三個輸入，並產生最多兩個輸出，類似于[執行 R 腳本][execute-r-script]模組。 透過稱為 `azureml_main`的特殊命名進入點函式，在參數方塊中輸入 Python 程式碼。

![執行 Python 腳本模組](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![模組參數方塊中的 python 程式碼範例](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>輸入參數

Python 模組的輸入會公開為 Pandas 資料框架。 `azureml_main` 函數最多接受兩個選擇性的 Pandas 資料框架做為參數。

輸入連接埠和函式參數之間的對應是有位置關係的：

- 第一個連接的輸入連接埠會對應至函式的第一個參數。
- 第二個輸入連接埠 (如果連接) 會對應至函式的第二個參數。
- 第三個輸入是用來匯[入其他的 Python 模組](#import-modules)。

輸入埠如何對應至 `azureml_main` 函式參數的更詳細語義如下所示。

![輸入埠設定和產生的 Python 簽章的資料表](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>輸出傳回值

`azureml_main` 函式必須傳回以 Python[序列](https://docs.python.org/2/c-api/sequence.html)（例如元組、清單或 NumPy 陣列）封裝的單一 Pandas 資料框架。 此順序的第一個元素會傳回至模組的第一個輸出埠。 模組的第二個輸出埠會用於[視覺效果](#visualizations)，而不需要傳回值。 此配置如下所示。

![將輸入埠對應至參數並將值傳回輸出埠](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>輸入和輸出資料類型的轉譯

Studio 資料集與 Panda 資料框架不同。 因此，Studio （傳統）中的輸入資料集會轉換為 Pandas 資料框架，而輸出資料框架會轉換回 Studio （傳統）資料集。 在這個轉換過程中，也會執行下列翻譯：

 **Python 資料類型** | **Studio 轉譯程式** |
| --- | --- |
| 字串和數值| 已轉譯為 |
| Pandas ' NA ' | 轉譯為「遺漏值」 |
| 索引向量 | 不支援 |
| 非字串資料行名稱 | 在資料行名稱上呼叫 `str` |
| 重複的資料行名稱 | 新增數值尾碼：（1）、（2）、（3）等等。

**Python 函式中的所有輸入資料框架一律具有64位的數值索引，從0到資料列數目減 1*

## <a id="import-modules"></a>匯入現有的 Python 腳本模組

用來執行 Python 的後端是以[Anaconda](https://www.anaconda.com/distribution/)為基礎，這是廣泛使用的科學 Python 散發套件。 其中包含以資料為中心的工作負載中最常見的 Python 套件200。 Studio （傳統）目前不支援使用封裝管理系統（例如 Pip 或 Conda）來安裝和管理外部程式庫。  如果您發現需要併入其他程式庫，請使用下列案例做為指南。

常見的使用案例是將現有的 Python 腳本納入 Studio （傳統）實驗中。 [執行 Python 腳本][execute-python-script]模組接受在第三個輸入埠包含 Python 模組的 zip 檔案。 該檔案會由執行架構在執行階段解壓縮，且內容會新增至 Python 解譯器的程式庫路徑。 然後 `azureml_main` 進入點函式可以直接匯入這些模組。 

作為範例，請考量包含簡單 “Hello, World” 函式的 Hello.py 檔案。

![Hello.py 檔案中的使用者定義函數](./media/execute-python-scripts/figure4.png)

接下來，我們會建立包含 Hello.py 的 Hello.zip 檔案：

![包含使用者定義 Python 程式碼的 Zip 檔案](./media/execute-python-scripts/figure5.png)

將 zip 檔案當做資料集上傳至 Studio （傳統）。 然後，藉由將您的 Python 程式碼附加至 [**執行 Python 腳本**] 模組的第三個輸入埠，來建立並執行使用該檔案的實驗，如下圖所示。

![使用 Hello .zip 做為執行 Python 腳本模組輸入的範例實驗](./media/execute-python-scripts/figure6a.png)

![以 zip 檔案上傳的使用者定義 Python 程式碼](./media/execute-python-scripts/figure6b.png)

模組輸出顯示 zip 檔案已解除封裝，且函式 `print_hello` 已執行。

![顯示使用者定義函數的模組輸出](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>存取 Azure 儲存體 Blob

您可以使用下列步驟來存取儲存在 Azure Blob 儲存體帳戶中的資料：

1. 在本機下載[適用于 Python 的 Azure Blob 儲存體套件](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip)。
1. 將 zip 檔案上傳至您的 Studio （傳統）工作區做為資料集。
1. 使用 `protocol='http'` 建立您的 BlobService 物件

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 停**用儲存設定**設定索引標籤中**所需的安全傳輸**

![停用 Azure 入口網站所需的安全傳輸](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>運作 Python 指令碼

在評分實驗中使用的任何[執行 Python 腳本][execute-python-script]模組，會在發佈為 web 服務時呼叫。 例如，下圖顯示計分實驗，其中包含用來評估單一 Python 運算式的程式碼。

![Web 服務的 Studio 工作區](./media/execute-python-scripts/figure3a.png)

![Python Pandas 運算式](./media/execute-python-scripts/python-script-with-python-pandas.png)

從這個實驗建立的 web 服務會採取下列動作：

1. 以 Python 運算式做為輸入（字串形式）
1. 將 Python 運算式傳送至 Python 解譯器
1. 傳回包含運算式和評估結果的資料表。

## <a id="visualizations"></a>使用視覺效果

[執行 Python 腳本][execute-python-script]可以傳回使用 MatplotLib 建立的繪圖。 不過，繪圖不會在使用 R 時自動重新導向至影像。因此，使用者必須明確地將任何繪圖儲存至 PNG 檔案。

若要從 MatplotLib 產生影像，您必須採取下列步驟：

1. 從預設的 Qt 轉譯器將後端切換到「匯總」。
1. 建立新的繪圖物件。
1. 取得軸並在其中產生所有繪圖。
1. 將圖表儲存至 PNG 檔案。

下列影像說明此程式，可使用 Pandas 中的 scatter_matrix 函數建立散佈圖矩陣。

![將 MatplotLib 圖形儲存至影像的程式碼](./media/execute-python-scripts/figure-v1-8.png)

![按一下 [執行 Python 腳本] 模組上的 [視覺化] 以查看圖形](./media/execute-python-scripts/figure-v2-9a.png)

![使用 Python 程式碼視覺化範例實驗的繪圖](./media/execute-python-scripts/figure-v2-9b.png)

將多個圖形儲存成不同的影像，是可行的。 Studio （傳統）執行時間會挑選所有影像，並串連它們以進行視覺效果。

## <a name="advanced-examples"></a>進階範例

安裝在 Studio （傳統）中的 Anaconda 環境包含常見的套件，例如 NumPy、SciPy 和 Scikits-learn。 這些封裝可以有效地用於機器學習管線中的資料處理。

例如，下列實驗和腳本說明如何在 Scikits-learn 中使用集團學習工具-瞭解如何計算資料集的功能重要性分數。 分數可以用來執行受監督的功能選取，然後再送到另一個模型。

以下 Python 函式是用於根據分數計算重要性分數及將功能排序：

![以分數排列功能的函數](./media/execute-python-scripts/figure8.png)

接著，下列實驗會計算並傳回 Azure Machine Learning Studio （傳統）中 "Pima indian diabetes 印度糖尿病" 資料集內的特性重要性分數：

![使用 Python 進行實驗以排名 Pima indian diabetes 印度糖尿病資料集的功能](./media/execute-python-scripts/figure9a.png)

![執行 Python 腳本模組輸出的視覺效果](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>限制

[[執行 Python 腳本][execute-python-script]] 模組目前具有下列限制：

### <a name="sandboxed-execution"></a>沙箱化執行

Python 執行時間目前已進行沙箱化，而且不允許以持續的方式存取網路或本機檔案系統。 所有本機儲存的文件都會被隔離，並且在模組結束時加以刪除。 Python 程式碼無法在其執行的機器上存取大部分的目錄，但目前的目錄及其子目錄例外。

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>缺少複雜的開發和調試支援

Python 模組目前不支援 IDE 功能，例如 intellisense 和偵錯。 此外，如果模組在執行階段失敗，則會提供完整的 Python 堆疊追蹤。 但是，它必須在模組的輸出記錄檔中檢視。 我們目前建議您在如 IPython 的環境中開發 Python 指令碼及進行偵錯，然後將程式碼匯入到模組。

### <a name="single-data-frame-output"></a>單一資料框架輸出

Python 進入點是唯一獲得允許的位置，可以將單一資料框架傳回為輸出。 目前無法直接將任意 Python 物件（例如訓練模型）傳回 Studio （傳統）執行時間。 如同[執行 R 腳本][execute-r-script]（具有相同的限制），在許多情況下，可能會將物件 pickle 到位元組陣列，然後在資料框架內傳回它。

### <a name="inability-to-customize-python-installation"></a>無法自訂 Python 安裝

目前，新增自訂 Python 模組的唯一方法是透過稍早所述的 zip 檔案機制。 雖然這適用于小型模組，但大型模組（特別是具有原生 Dll 的模組）或大量模組很麻煩。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Python 開發人員中心](https://azure.microsoft.com/develop/python/)。

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
