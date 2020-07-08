---
title: 如何在工作區中執行 Jupyter Notebook
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning Studio 中執行 Jupyter Notebook，而不需離開您的工作區。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 06/27/2020
ms.openlocfilehash: 476f3925886a6de68b49e1861d22e6cfaf594202
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85601443"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>如何在工作區中執行 Jupyter Notebook
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何在您的 Azure Machine Learning Studio 工作區中直接執行您的 Jupyter Notebook。 您可以啟動 [Jupyter](https://jupyter.org/) 或 [JupyterLab](https://jupyterlab.readthedocs.io)，也可以在不離開工作區的情況下編輯和執行您的筆記本。

看看您如何：

* 在工作區中建立 Jupyter Notebook
* 從筆記本執行實驗
* 變更筆記本環境
* 尋找用來執行筆記本的計算執行個體詳細資料

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://aka.ms/AMLFree)。
* Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

## <a name="create-notebooks"></a><a name="create"></a> 建立筆記本

在您的 Azure Machine Learning 工作區中，建立新的 Jupyter 筆記本並開始工作。 新建立的筆記本會儲存在預設工作區儲存體中。 此筆記本可與具有該工作區存取權的任何人共用。 

建立新的筆記本： 

1. 在 [Azure Machine Learning Studio](https://ml.azure.com) 中開啟您的工作區。
1. 選取左側的 [筆記本]。 
1. 在 [我的檔案] 區段中，選取 [使用者檔案] 清單上方的**建立新檔案**圖示。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="建立新檔案":::

1. 為檔案命名。 
1. 針對 Jupyter Notebook 檔案，請選取 [Python 筆記本] 做為檔案類型。
1. 選取檔案目錄。
1. 選取 [建立]。

您也可以建立文字檔。  選取 [文字] 做為檔案類型，並將副檔名加入名稱 (例如，myfile.py 或 myfile.txt .txt)。  

您也可以使用 [筆記本] 頁面頂端的工具上傳資料夾和檔案，包括筆記本。  在 [預覽] 區段中會顯示筆記本和大部分的文字檔案類型。  其他檔案類型大多不能預覽。

> [!IMPORTANT]
> 筆記本和腳本中的內容可能會從您的會話讀取資料，並在 Azure 中存取資料，而不需要您的組織。  僅從信任的來源載入檔案。 如需詳細資訊，請參閱[安全程式碼最佳作法](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)。

### <a name="clone-samples"></a>複製範例

您的工作區包含一個 **Samples** 資料夾，其中的筆記本是為了協助您探索 SDK，並做為您自己的機器學習專案而設計。  您可以將這些筆記本複製到您的工作區儲存體容器上的資料夾中。  

如需範例，請參閱[教學課程：建立第一個 ML 實驗](tutorial-1st-experiment-sdk-setup.md#azure)。

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> 使用 Git 中的檔案並建立我的檔案版本

您可以使用終端機視窗來存取所有 Git 作業。 所有 Git 檔案和資料夾都會儲存在您的工作區檔案系統中。

> [!NOTE]
> 將您的檔案和資料夾新增至 **~/cloudfiles/code/Users** 資料夾下的任何位置，以便在您的所有 Jupyter 環境中都可以看到它們。

存取終端機：

1. 在 [Azure Machine Learning Studio](https://ml.azure.com) 中開啟您的工作區。
1. 選取左側的 [筆記本]。
1. 選取位於左側 [使用者檔案] 區段中的任何筆記本。  如果您沒有任何筆記本，請先[建立筆記本](#create)。
1. 選取 [計算] 目標或建立一個新的目標，然後等候它執行。
1. 選取**開啟終端機**圖示。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="開啟終端機":::

1. 如果您沒看到此圖示，請選取計算目標右邊的 [...]，然後選取 [開啟終端機]。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="從 ... 開啟終端機":::


瞭解如何[將 Git 存放庫複製到您的工作區檔案系統](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)。


### <a name="share-notebooks-and-other-files"></a>共用筆記本和其他檔案

複製並貼上 URL，以共用筆記本或檔案。  只有工作區的其他使用者才能存取此 URL。  瞭解如何[授與對工作區的存取權](how-to-assign-roles.md)。

## <a name="edit-a-notebook"></a>編輯筆記本

若要編輯筆記本，開啟工作區的 [使用者檔案] 區段中的任何筆記本。 按一下您要編輯的儲存格。 

您可以編輯筆記本，而不需要連接到計算實例。  當您想要執行筆記本中的資料格時，請選取或建立計算實例。  如果您選取已停止的計算實例，它會在您執行第一個資料格時自動啟動。

當計算實例正在執行時，您也可以使用任何 Python 筆記本中的程式碼完成（由[Intellisense](https://code.visualstudio.com/docs/editor/intellisense)提供技術支援）。

您也可以從 [筆記本] 工具列啟動 Jupyter 或 JupyterLab。  Azure Machine Learning 不會提供 Jupyter 或 JupyterLab 的更新和修正錯誤，因為它們是 Microsoft 支援服務界限外的開放原始碼產品。

### <a name="use-intellisense"></a>使用 IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)是一種程式碼完成輔助工具，其中包含許多功能：列出成員、參數資訊、快速諮詢和完整文字。 這些功能有助於深入了解您使用的程式碼、追蹤所鍵入的參數，以及幾個按鍵即可新增屬性和方法呼叫。  

輸入程式碼時，請使用 Ctrl + 空格鍵來觸發 IntelliSense。

### <a name="save-and-checkpoint-a-notebook"></a>儲存和檢查點筆記本

當您建立 *ipynb*檔案時，Azure Machine Learning 會建立檢查點檔案   。

在 [筆記本] 工具列中，選取功能表，然後選取 [檔案] [ ** &gt; 儲存與檢查點**] 以手動儲存筆記本，它會新增與筆記本相關聯的檢查點檔案。

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="筆記本工具列中儲存工具的螢幕擷取畫面":::

每隔30秒會自動儲存每個筆記本。自動儲存只會更新初始 *ipynb*檔案   ，而不是檢查點檔案。
 
選取 [筆記本] 功能表中的 [**檢查點**] 以建立命名的檢查點，並將筆記本還原至儲存的檢查點。


### <a name="useful-keyboard-shortcuts"></a>實用的鍵盤快速鍵

|鍵盤  |動作  |
|---------|---------|
|Shift+Enter     |  執行儲存格       |
|Ctrl+空格鍵 | 啟動 IntelliSense |
|Ctrl+M (Windows)     |  啟用/停用筆記本中的 Tab 鍵捕捉。       |
|Ctrl+Shift+M (Mac 和 Linux)     |    啟用/停用筆記本中的 Tab 鍵捕捉。     |
|Tab 鍵 (啟用 Tab 鍵捕捉時) | 新增 ' \t ' 字元 (縮排)
|Tab 鍵 (停用 Tab 鍵捕捉時) | 將焦點變更至下一個可設定焦點的項目 (刪除儲存格按鈕、執行按鈕等)

## <a name="delete-a-notebook"></a>刪除筆記本

您「無法」刪除 **Samples** 筆記本。  這些筆記本是 Studio 的一部分，而且會在每次發佈新的 SDK 時更新。  

您「可以」透過下列任何方式刪除 **使用者檔案**筆記本：

* 在 Studio 中，選取資料夾或檔案後面的 [...]。  請務必使用支援的瀏覽器 (Microsoft Edge、Chrome 或 Firefox)。
* 從任何 [筆記本] 工具列中，選取[**開啟終端機**](#terminal)以存取計算執行個體的終端機視窗。
* 使用 Jupyter 或 JupyterLab 各自的工具。

## <a name="run-an-experiment"></a>執行實驗

若要從筆記本執行實驗，您必須先連線到執行中的[計算執行個體](concept-compute-instance.md)。 如果您沒有計算執行個體，請使用下列步驟建立一個： 

1. 選取 [筆記本] 工具列中的 **+** 。 
2. 為計算命名，然後選擇 [虛擬機器大小]。 
3. 選取 [建立]。
4. 計算執行個體會自動連線到筆記本，您現在可以執行儲存格了。

只有您可以查看和使用您所建立的計算執行個體。  您的**使用者檔案**會與 VM 分開儲存，而且由工作區中的所有計算執行個體共用。

### <a name="view-logs-and-output"></a>檢視記錄和輸出

使用[筆記本小工具](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)檢視執行進度和記錄。 小工具沒有同步，會一直提供更新直到定型完成。 Jupyter 和 JupterLab 也支援 Azure Machine Learning 小工具。

## <a name="change-the-notebook-environment"></a>變更筆記本環境

[筆記本] 工具列可讓您變更您的筆記本執行所在的環境。  

以下這些動作不會變更筆記本狀態或筆記本中任何變數的值：

|動作  |結果  |
|---------|---------| --------|
|停止核心程序     |  停止任何正在執行的儲存格。 執行儲存格會自動重新啟動核心程序。 |
|瀏覽至另一個工作區區段     |     執行中的儲存格會停止。 |

以下這些動作會重設筆記本狀態，並重設筆記本中的所有變數。

|動作  |結果  |
|---------|---------| --------|
| 變更核心程序 | 筆記本使用新的核心程序。 |
| 切換計算    |     筆記本會自動使用新的計算。 |
| 重設計算 | 當您嘗試執行儲存格時再次啟動。 |
| 停止計算     |    不會執行任何儲存格。  |
| 在 Jupyter 或 JupyterLab 中開啟筆記本     |    在新索引標籤中開啟筆記本。  |

### <a name="add-new-kernels"></a>加入新的核心程序

筆記本會自動尋找連線的計算執行個體上安裝的所有 Jupyter 核心程序。  將核心程序加入計算執行個體：

1. 選取 [筆記本] 工具列中的[**開啟終端機**](#terminal)。
1. 使用終端機視窗建立新的環境。
1. 啟用環境。  例如，建立 `newenv` 之後：

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

您可以安裝任何[可用的 Jupyter 核心程序](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) 。

### <a name="status-indicators"></a>狀態指標

[計算] 下拉式清單旁邊的指標會顯示其狀態。  此狀態也會顯示在下拉式清單中。  

|Color |計算狀態 |
|---------|---------| 
| 綠色 | 計算正在執行 |
| 紅色 |計算失敗 | 
| 黑色 | 計算已停止 |
|  淺藍色 |計算建立、啟動、重新開機、設定 |
|  灰色 |計算已刪除，正在停止 |

[核心程序] 下拉式清單旁邊的指標會顯示其狀態。

|Color |核心狀態 |
|---------|---------|
|  綠色 |核心程序已連線、閒置、忙碌中|
|  灰色 |核心程序未連線 |

## <a name="find-compute-details"></a>尋找計算詳細資料 

在 [Studio](https://ml.azure.com)中的 [計算] 頁面上，尋找計算執行個體的詳細資料。

## <a name="next-steps"></a>後續步驟

* [執行您的第一個實驗](tutorial-1st-experiment-sdk-train.md)
* [使用快照集備份檔案儲存體](../storage/files/storage-snapshots-files.md)
