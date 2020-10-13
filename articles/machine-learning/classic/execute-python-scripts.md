---
title: ML Studio (傳統) ：執行 Python 腳本-Azure
description: 瞭解如何使用「執行 Python 腳本」模組，在 Machine Learning Studio (傳統) 實驗和 web 服務中使用 Python 程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: devx-track-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 5c728b219168f61f7f791b7db280a701ff216985
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362379"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>在 Azure Machine Learning Studio (傳統) 中執行 Python 機器學習服務腳本

**適用於：** ![適用於。](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (傳統版)   ![不適用於。](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)


Python 是許多資料科學家的工具胸中的重要工具。 它是用於一般機器學習工作流程的每個階段，包括資料探索、功能解壓縮、模型定型和驗證，以及部署。

本文說明如何使用「執行 Python 腳本」模組，在 Azure Machine Learning Studio 中使用 Python 程式碼 (傳統) 實驗和 web 服務。

## <a name="using-the-execute-python-script-module"></a>使用執行 Python 腳本模組

您可以透過 [執行 Python 腳本][execute-python-script] 模組，在 Studio (傳統) 中使用 python 的主要介面。 它最多接受三個輸入，並產生最多兩個輸出，類似于「 [執行 R 腳本][execute-r-script] 」模組。 Python 程式碼是透過稱為的特殊命名的進入點函式，在 [參數] 方塊中輸入 `azureml_main` 。

![執行 Python 腳本模組](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![模組參數方塊中的 python 程式碼範例](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>輸入參數

Python 模組的輸入會公開為 Pandas 資料框架。 `azureml_main`函數最多可接受兩個選擇性的 Pandas 資料框架做為參數。

輸入連接埠和函式參數之間的對應是有位置關係的：

- 第一個連接的輸入連接埠會對應至函式的第一個參數。
- 第二個輸入連接埠 (如果連接) 會對應至函式的第二個參數。
- 第三個輸入用來匯 [入其他的 Python 模組](#import-modules)。

更詳細的輸入埠如何對應至函式參數的語法如下 `azureml_main` 所示。

![輸入埠設定和產生的 Python 簽名表](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>輸出傳回值

函式 `azureml_main` 必須傳回以 Python [順序](https://docs.python.org/2/c-api/sequence.html) 封裝的單一 Pandas 資料框架，例如元組、清單或 NumPy 陣列。 此序列的第一個元素會傳回至模組的第一個輸出埠。 模組的第二個輸出埠用於 [視覺效果](#visualizations) ，且不需要傳回值。 此配置如下所示。

![將輸入埠對應至參數，並將值傳回至輸出埠](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>輸入和輸出資料類型的轉譯

Studio 資料集與 Panda 資料框架不同。 因此，Studio (傳統) 中的輸入資料集會轉換為 Pandas 資料框架，而輸出資料框架會轉換回 Studio (傳統) 資料集。 在這個轉換過程中，也會執行下列翻譯：

 **Python 資料類型** | **Studio 轉譯程式** |
| --- | --- |
| 字串和數值| 已轉譯為 |
| Pandas ' NA ' | 轉譯為「遺漏值」 |
| 索引向量 | 不 |
| 非字串資料行名稱 | `str`在資料行名稱上呼叫 |
| 重複的資料行名稱 | 新增數值尾碼： (1) 、 (2) 、 (3) 等等。

**Python 函式中所有的輸入資料框架一律具有從0到資料列數目減1的64位數位索引*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>匯入現有的 Python 指令碼模組

用來執行 Python 的後端是以 [Anaconda](https://www.anaconda.com/distribution/)為基礎，這是一種廣泛使用的科學 Python 散發套件。 它隨附在以資料為中心的工作負載中最常見的 Python 套件200。 Studio (傳統) 目前並不支援使用 Pip 或 Conda 等套件管理系統來安裝和管理外部程式庫。  如果您發現需要併入其他程式庫，請使用下列案例作為指南。

常見的使用案例是將現有 Python 腳本併入 Studio (傳統) 實驗中。 [ [執行 Python 腳本][execute-python-script] ] 模組接受包含第三個輸入埠之 Python 模組的 zip 檔案。 該檔案會由執行架構在執行階段解壓縮，且內容會新增至 Python 解譯器的程式庫路徑。 然後 `azureml_main` 進入點函式可以直接匯入這些模組。 

例如，請考慮包含簡單 "Hello，World" 函式的 file Hello.py。

![Hello.py 檔案中的使用者定義函數](./media/execute-python-scripts/figure4.png)

接下來，我們會建立包含 Hello.py 的 Hello.zip 檔案：

![包含使用者定義之 Python 程式碼的 Zip 檔案](./media/execute-python-scripts/figure5.png)

將 zip 檔案以資料集的形式上傳到 Studio (傳統) 。 然後，建立並執行實驗，以使用 Hello.zip 檔案中的 Python 程式碼，方法是將它附加到 **執行 Python 腳本** 模組的第三個輸入埠，如下圖所示。

![使用 Hello.zip 作為執行 Python 腳本模組輸入的範例實驗](./media/execute-python-scripts/figure6a.png)

![以 zip 檔案形式上傳的使用者定義 Python 程式碼](./media/execute-python-scripts/figure6b.png)

模組輸出顯示 zip 檔案已解除封裝，且函式 `print_hello` 已執行。

![顯示使用者定義函數的模組輸出](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>存取 Azure 儲存體 Blob

您可以使用下列步驟來存取儲存在 Azure Blob 儲存體帳戶中的資料：

1. 在本機下載 [適用于 Python 的 Azure Blob 儲存體套件](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) 。
1. 將 zip 檔案上傳至您的 Studio (傳統) 工作區作為資料集。
1. 建立 BlobService 物件 `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 在**儲存體設定**設定索引標籤中停用**需要安全傳輸**

![停用 Azure 入口網站所需的安全傳輸](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>運作 Python 指令碼

當評分實驗中使用的任何[執行 Python 指令碼][execute-python-script]模組發佈為 Web 服務時，系統會呼叫這些模組。 例如，下圖顯示計分實驗，其中包含用以評估單一 Python 運算式的程式碼。

![Web 服務的 Studio 工作區](./media/execute-python-scripts/figure3a.png)

![Python Pandas 運算式](./media/execute-python-scripts/python-script-with-python-pandas.png)

從這個實驗建立的 web 服務會採取下列動作：

1. 以字串形式取得 Python 運算式作為輸入 () 
1. 將 Python 運算式傳送至 Python 解譯器
1. 傳回包含運算式和評估結果的資料表。

## <a name="working-with-visualizations"></a><a id="visualizations"></a>使用視覺效果

使用 MatplotLib 建立的繪圖可由 [執行 Python 腳本][execute-python-script]傳回。 不過，繪圖不會在使用 R 時自動重新導向至影像。如此一來，使用者就必須明確地將任何繪圖儲存為 PNG 檔案。

若要從 MatplotLib 產生影像，您必須執行下列步驟：

1. 從預設 Qt 型轉譯器將後端切換為「匯總」。
1. 建立新的圖表物件。
1. 取得軸並產生所有繪圖。
1. 將圖表儲存至 PNG 檔案。

下圖將說明此程式，這些影像會使用 Pandas 中的 scatter_matrix 函數來建立散佈圖矩陣。

![將 MatplotLib 圖儲存至影像的程式碼](./media/execute-python-scripts/figure-v1-8.png)

![按一下 [執行 Python 腳本] 模組上的 [視覺化] 以查看圖形](./media/execute-python-scripts/figure-v2-9a.png)

![使用 Python 程式碼將範例實驗的繪圖視覺化](./media/execute-python-scripts/figure-v2-9b.png)

您可以藉由將多個圖形儲存至不同的影像來傳回它們。 Studio (傳統) 執行時間會挑選所有影像，並將它們串連以進行視覺效果。

## <a name="advanced-examples"></a>進階範例

安裝在 Studio (傳統) 中的 Anaconda 環境包含常見的套件，例如 NumPy、SciPy 和 Scikits-learn-學習。 這些封裝可有效用於機器學習管線中的資料處理。

例如，下列實驗和腳本說明如何使用 Scikits-Learn 中的集團學習工具，計算資料集的功能重要性分數。 分數可以用來執行受監督的功能選取，然後再送到另一個模型。

以下 Python 函式是用於根據分數計算重要性分數及將功能排序：

![依分數排名特徵的函式](./media/execute-python-scripts/figure8.png)

下列實驗會接著計算並傳回 Azure Machine Learning Studio (傳統) 中「Pima indian diabetes 的印度糖尿病」資料集的重要性分數：

![使用 Python 實驗 Pima indian diabetes 印度糖尿病資料集中的功能排名](./media/execute-python-scripts/figure9a.png)

![執行 Python 腳本模組輸出的視覺效果](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>限制

[ [執行 Python 腳本][execute-python-script] ] 模組目前有下列限制：

### <a name="sandboxed-execution"></a>沙箱執行

Python 執行時間目前為沙箱化，不允許以持續的方式存取網路或本機檔案系統。 所有本機儲存的文件都會被隔離，並且在模組結束時加以刪除。 Python 程式碼無法在其執行的機器上存取大部分的目錄，但目前的目錄及其子目錄例外。

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>缺少複雜的開發和偵錯工具支援

Python 模組目前不支援 IDE 功能，例如 intellisense 和偵錯。 此外，如果模組在執行階段失敗，則會提供完整的 Python 堆疊追蹤。 但是，它必須在模組的輸出記錄檔中檢視。 我們目前建議您在如 IPython 的環境中開發 Python 指令碼及進行偵錯，然後將程式碼匯入到模組。

### <a name="single-data-frame-output"></a>單一資料框架輸出

Python 進入點是唯一獲得允許的位置，可以將單一資料框架傳回為輸出。 目前無法將任意的 Python 物件（例如定型的模型）直接傳回給 Studio (傳統) 執行時間。 如同[執行 R 指令碼][execute-r-script] (具有相同的限制)，在許多情況下可以將物件存放至位元組陣列，然後在資料框架內傳回。

### <a name="inability-to-customize-python-installation"></a>無法自訂 Python 安裝

目前，新增自訂 Python 模組的唯一方法是透過稍早所述的 zip 檔案機制。 雖然這對小型模組是可行的，但是大型模組很麻煩 (特別是具有原生 Dll) 或大量模組的模組。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Python 開發人員中心](https://azure.microsoft.com/develop/python/)。

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
