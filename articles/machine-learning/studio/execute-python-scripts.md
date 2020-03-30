---
title: 執行 Python 指令碼
titleSuffix: ML Studio (classic) - Azure
description: 瞭解如何在機器學習工作室（經典）實驗和 Web 服務中使用執行 Python 腳本模組使用 Python 代碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: c79f6bd63fa5d8d8c6b22ff271d8ca513a94fd64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218090"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>在 Azure 機器學習工作室中執行 Python 機器學習腳本（經典）

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Python 是許多資料科學家工具箱中有價值的工具。 它用於典型機器學習工作流的每個階段，包括資料探索、功能提取、模型培訓和驗證以及部署。

本文介紹如何使用執行 Python 腳本模組在 Azure 機器學習工作室（經典）實驗和 Web 服務中使用 Python 代碼。

## <a name="using-the-execute-python-script-module"></a>使用執行 Python 腳本模組

工作室中 Python 的主要介面（經典）是通過執行[Python 腳本][execute-python-script]模組。 它最多接受三個輸入，並最多生成兩個輸出，類似于執行[R 腳本][execute-r-script]模組。 Python 代碼通過名為`azureml_main`的特命名進入點函數輸入到參數框中。

![執行 Python 腳本模組](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![模組參數框中的 python 代碼示例](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>輸入參數

Python 模組的輸入公開為熊貓資料幀。 該`azureml_main`函數最多接受兩個可選的熊貓資料幀作為參數。

輸入連接埠和函式參數之間的對應是有位置關係的：

- 第一個連接的輸入連接埠會對應至函式的第一個參數。
- 第二個輸入連接埠 (如果連接) 會對應至函式的第二個參數。
- 第三個輸入用於[導入其他 Python 模組](#import-modules)。

下面顯示了輸入埠如何映射到`azureml_main`函數參數的更詳細的語義。

![輸入埠配置表和生成的 Python 簽名](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>輸出傳回值

該`azureml_main`函數必須返回在 Python[序列](https://docs.python.org/2/c-api/sequence.html)（如元組、清單或 NumPy 陣列）中打包的單個熊貓資料幀。 此序列的第一個元素返回到模組的第一個輸出埠。 模組的第二個輸出埠用於[視覺化](#visualizations)，不需要傳回值。 此方案如下所示。

![將輸入埠映射到參數並將值返回輸出埠](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>輸入和輸出資料類型的轉換

工作室資料集與熊貓資料幀不同。 因此，Studio 中的輸入資料集（經典）將轉換為熊貓資料幀，輸出資料幀將轉換回工作室（經典）資料集。 在此轉換過程中，還會執行以下翻譯：

 **Python 資料類型** | **工作室翻譯程式** |
| --- | --- |
| 字串和數位| 按方式翻譯 |
| 熊貓"NA" | 翻譯為"缺失值" |
| 索引向量 | 不支援* |
| 非字串列名稱 | 調用`str`列名稱 |
| 重複列名稱 | 添加數位尾碼：（1）、（2）、（3）等。

**Python 函數中的所有輸入資料幀始終具有從 0 到行數減去 1 的 64 位數位索引*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>匯入現有的 Python 指令碼模組

用於執行 Python 的後端基於[Anaconda，](https://www.anaconda.com/distribution/)一種廣泛使用的科學 Python 發行版本。 它附帶近 200 個在以資料為中心的工作負載中使用的最常見的 Python 包。 Studio（經典版）目前不支援使用像 Pip 或 Conda 這樣的包管理系統來安裝和管理外部庫。  如果您發現需要合併其他庫，請使用以下方案作為指南。

常見的用例是將現有的 Python 腳本合併到 Studio（經典）實驗中。 [執行 Python 腳本][execute-python-script]模組接受在第三個輸入埠包含 Python 模組的 ZIP 檔案。 該檔案會由執行架構在執行階段解壓縮，且內容會新增至 Python 解譯器的程式庫路徑。 然後 `azureml_main` 進入點函式可以直接匯入這些模組。 

例如，請考慮包含簡單"你好，世界"功能的檔Hello.py。

![Hello.py檔中的使用者定義的函數](./media/execute-python-scripts/figure4.png)

接下來，我們會建立包含 Hello.py 的 Hello.zip 檔案：

![包含使用者定義的 Python 代碼的 Zip 檔](./media/execute-python-scripts/figure5.png)

將 ZIP 檔案作為資料集上載到 Studio（經典）。 然後創建並運行一個實驗，通過在 Hello.ZIP 檔案中使用 Python 代碼，將其附加到執行 Python**腳本**模組的第三個輸入埠，如下圖所示。

![使用 Hello.zip 作為執行 Python 腳本模組的輸入的示例實驗](./media/execute-python-scripts/figure6a.png)

![使用者定義的 Python 代碼上傳為 ZIP 檔案](./media/execute-python-scripts/figure6b.png)

模組輸出顯示 zip 檔案已解除封裝，且函式 `print_hello` 已執行。

![顯示使用者定義的功能的模組輸出](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>訪問 Azure 存儲 Blob

可以使用以下步驟訪問存儲在 Azure Blob 存儲帳戶中的資料：

1. 在本地下載[Python 的 Azure Blob 存儲包](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip)。
1. 將 ZIP 檔案作為資料集上載到 Studio（經典）工作區。
1. 使用`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 禁用存儲**配置**設置選項卡中**所需的安全傳輸**

![禁用 Azure 門戶中所需的安全傳輸](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>運作 Python 指令碼

當評分實驗中使用的任何[執行 Python 指令碼][execute-python-script]模組發佈為 Web 服務時，系統會呼叫這些模組。 例如，下圖顯示了一個評分實驗，其中包含用於計算單個 Python 運算式的代碼。

![Web 服務的工作室工作區](./media/execute-python-scripts/figure3a.png)

![Python 熊貓表達](./media/execute-python-scripts/python-script-with-python-pandas.png)

通過此實驗創建的 Web 服務將採取以下操作：

1. 以 Python 運算式作為輸入（作為字串）
1. 將 Python 運算式發送到 Python 解譯器
1. 返回包含運算式和計算結果的表。

## <a name="working-with-visualizations"></a><a id="visualizations"></a>使用視覺效果

使用 MatplotLib 創建的繪圖可以通過執行[Python 腳本][execute-python-script]返回。 但是，繪圖不會像使用 R 時那樣自動重定向到圖像。因此，使用者必須顯式將任何繪圖保存到 PNG 檔。

要從 MatplotLib 生成圖像，您必須執行以下步驟：

1. 從預設基於 Qt 的渲染器將後端切換到"AGG"。
1. 創建新的繪圖物件。
1. 獲取軸並生成所有繪圖。
1. 將圖形保存到 PNG 檔。

此過程在以下圖像中進行了說明，這些圖像使用熊貓中的scatter_matrix函數創建散佈圖矩陣。

![將 MatplotLib 圖形保存到圖像的代碼](./media/execute-python-scripts/figure-v1-8.png)

![按一下執行 Python 腳本模組上的視覺化以查看圖形](./media/execute-python-scripts/figure-v2-9a.png)

![使用 Python 代碼視覺化示例實驗的繪圖](./media/execute-python-scripts/figure-v2-9b.png)

通過將多個圖形保存到不同的圖像中，可以返回多個圖形。 Studio（經典）運行時選取所有圖像並串聯它們以進行視覺化。

## <a name="advanced-examples"></a>進階範例

安裝在 Studio 中的 Anaconda 環境（經典版）包含常見套裝軟體，如 NumPy、SciPy 和 Scikits-Learn。 這些包可以有效地用於機器學習管道中的資料處理。

例如，以下實驗和腳本說明了在 Scikits-學習計算資料集要素重要性分數中的合奏學習者的使用。 分數可用於在輸入其他模型之前執行受監督的要素選擇。

以下 Python 函式是用於根據分數計算重要性分數及將功能排序：

![按分數對要素進行排名的函數](./media/execute-python-scripts/figure8.png)

然後，以下實驗計算並返回 Azure 機器學習工作室中的"Pima 印度糖尿病"資料集中的特徵的重要性分數（經典）：

![使用 Python 對 Pima 印度糖尿病資料集中的功能進行排名的實驗](./media/execute-python-scripts/figure9a.png)

![執行 Python 腳本模組的輸出視覺化](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>限制

[執行 Python 腳本][execute-python-script]模組當前具有以下限制：

### <a name="sandboxed-execution"></a>沙箱執行

Python 運行時當前已沙箱化，不允許以持久方式訪問網路或本地檔案系統。 所有本機儲存的文件都會被隔離，並且在模組結束時加以刪除。 Python 程式碼無法在其執行的機器上存取大部分的目錄，但目前的目錄及其子目錄例外。

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>缺乏複雜的開發和調試支援

Python 模組目前不支援 IDE 功能，例如 intellisense 和偵錯。 此外，如果模組在執行階段失敗，則會提供完整的 Python 堆疊追蹤。 但是，它必須在模組的輸出記錄檔中檢視。 我們目前建議您在如 IPython 的環境中開發 Python 指令碼及進行偵錯，然後將程式碼匯入到模組。

### <a name="single-data-frame-output"></a>單個資料幀輸出

Python 進入點是唯一獲得允許的位置，可以將單一資料框架傳回為輸出。 當前無法將任意 Python 物件（如訓練的模型）直接返回到 Studio（經典）運行時。 如同[執行 R 指令碼][execute-r-script] (具有相同的限制)，在許多情況下可以將物件存放至位元組陣列，然後在資料框架內傳回。

### <a name="inability-to-customize-python-installation"></a>無法自訂 Python 安裝

目前，新增自訂 Python 模組的唯一方法是透過稍早所述的 zip 檔案機制。 雖然這適用于小型模組，但對於大型模組（尤其是具有本機 DLL 的模組）或大量模組來說，這非常麻煩。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [Python 開發人員中心](https://azure.microsoft.com/develop/python/)。

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
