---
title: 如何在工作區中執行 Jupyter 筆記本
titleSuffix: Azure Machine Learning
description: 瞭解如何執行 Jupyter Notebook，而不需要將您的工作區離開 Azure Machine Learning studio。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 67da2cb31d59838bb3ad2b964530d85d8be9be4c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783652"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>如何在工作區中執行 Jupyter 筆記本
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何在 Azure Machine Learning studio 中，直接在您的工作區中執行 Jupyter 筆記本。 當您啟動[Jupyter](https://jupyter.org/)或[JupyterLab](https://jupyterlab.readthedocs.io)時，您也可以在不離開工作區的情況下編輯和執行您的筆記本。

瞭解您可以如何：

* 在您的工作區中建立 Jupyter 筆記本
* 從筆記本執行實驗
* 變更筆記本環境
* 尋找用來執行筆記本的計算實例詳細資料

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://aka.ms/AMLFree)。
* Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

## <a name="create-notebooks"></a><a name="create"></a>建立筆記本

在您的 Azure Machine Learning 工作區中，建立新的 Jupyter 筆記本並開始工作。 新建立的筆記本會儲存在預設工作區儲存體中。 此筆記本可與具有工作區存取權的任何人共用。 

若要建立新的筆記本： 

1. 在[Azure Machine Learning studio](https://ml.azure.com)中開啟您的工作區。
1. 在左側選取 [**筆記本**]。 
1. 在 [**我**的檔案] 區段中，選取 [**使用者**檔案清單] 上方的 [**建立新**檔案] 圖示。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="建立新檔案":::

1. 將檔案命名為。 
1. 針對 Jupyter Notebook 檔案，請選取 [ **Python 筆記本**] 做為檔案類型。
1. 選取檔案目錄。
1. 選取 [建立]  。

> [!TIP]
> 您也可以建立文字檔。  選取 [**文字**] 做為檔案類型，並將副檔名新增至 [名稱] （例如 myfile.py 或 myfile.txt .txt）  

您也可以使用 [筆記本] 頁面頂端的工具上傳資料夾和檔案，包括筆記本。  筆記本和大部分的文本檔案類型會顯示在 [預覽] 區段中。  其他大部分的檔案類型都不提供預覽。

### <a name="clone-samples"></a>複製範例

您的工作區包含一個**範例**資料夾，其中的筆記本是設計用來協助您探索 SDK，並做為您自己的機器學習專案的範例。  您可以將這些筆記本複製到您的工作區儲存體容器上自己的資料夾中。  

如需範例，請參閱[教學課程：建立您的第一個 ML 實驗](tutorial-1st-experiment-sdk-setup.md#azure)。

### <a name="a-nameterminal-use-files-from-git-and-version-my-files"></a><a name="terminal">使用 Git 的檔案和檔案版本 my files

您可以使用終端機視窗來存取所有 Git 作業。 所有 Git 檔案和資料夾都會儲存在您的工作區檔案系統中。

> [!NOTE]
> 在 **~/cloudfiles/code/Users**資料夾下的任何位置新增檔案和資料夾，以便在您的所有 Jupyter 環境中都可以看到它們。

若要存取終端機：

1. 在[Azure Machine Learning studio](https://ml.azure.com)中開啟您的工作區。
1. 在左側選取 [**筆記本**]。
1. 選取位於左側 [**使用者**檔案] 區段中的任何筆記本。  如果您沒有任何筆記本，請先[建立筆記本](#create)
1. 選取**計算**目標，或建立一個新的，然後等候它執行。
1. 選取 [**開啟終端**機] 圖示。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="開啟終端機":::

1. 如果您看不到此圖示，請選取計算目標右邊的 [ **...** ]，然後選取 [**開啟終端**機]。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="開啟 [終端機] .。。":::


深入瞭解如何[將 Git 存放庫複製到您的工作區檔案系統](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)。


### <a name="share-notebooks-and-other-files"></a>共用筆記本和其他檔案

複製並貼上 URL，以共用筆記本或檔案。  只有工作區的其他使用者才能存取此 URL。  深入瞭解授[與您工作區的存取權](how-to-assign-roles.md)。

## <a name="edit-a-notebook"></a>編輯筆記本

若要編輯筆記本，請開啟位於工作區 [**使用者**檔案] 區段中的任何筆記本。 按一下您要編輯的資料格。 

當執行中的計算實例正在執行時，您也可以使用任何 Python 筆記本中的程式碼完成功能（由[Intellisense](https://code.visualstudio.com/docs/editor/intellisense)提供技術支援）。

您也可以從 [筆記本] 工具列啟動 Jupyter 或 JupyterLab。  Azure Machine Learning 不會提供 Jupyter 或 JupyterLab 的更新和修正錯誤，因為它們是 Microsoft 支援服務界限外的開放原始碼產品。

### <a name="useful-keyboard-shortcuts"></a>實用的鍵盤快速鍵

|鍵盤  |動作  |
|---------|---------|
|Shift+Enter     |  執行資料格       |
|Ctrl + M （Windows）     |  啟用/停用筆記本中的索引標籤補漏白。       |
|Ctrl + Shift + M （Mac & Linux）     |    啟用/停用筆記本中的索引標籤補漏白。     |
|Tab 鍵（啟用索引標籤設陷時） | 新增 ' \t ' 字元（縮排）
|Tab 鍵（停用索引標籤設陷時） | 將焦點變更至下一個可設定的專案（刪除儲存格按鈕、執行按鈕等）

## <a name="delete-a-notebook"></a>刪除筆記本

您*無法*刪除**範例**筆記本。  這些筆記本是 studio 的一部分，而且會在每次發行新的 SDK 時進行更新。  

您*可以*用下列任一種方式刪除**使用者**檔案的筆記本：

* 在 studio 中，選取資料夾或檔案結尾的 [ **...** ]。  請務必使用支援的瀏覽器（Microsoft Edge、Chrome 或 Firefox）。
* 從任何筆記本工具列中，選取 [[**開啟終端**](#terminal)機] 以存取計算實例的終端機視窗。
* 在 Jupyter 或 JupyterLab 中都有其工具。

## <a name="run-an-experiment"></a>執行實驗

若要從筆記本執行實驗，您必須先連接到執行中的[計算實例](concept-compute-instance.md)。 如果您沒有計算實例，請使用下列步驟來建立一個： 

1. 選取**+** [筆記本] 工具列中的。 
2. 將計算命名為，然後選擇**虛擬機器大小**。 
3. 選取 [建立]  。
4. 計算實例會自動連接到筆記本，而您現在可以執行儲存格。

只有您可以查看和使用您所建立的計算實例。  您的**使用者**檔案會與 VM 分開儲存，而且會在工作區中的所有計算實例之間共用。

### <a name="view-logs-and-output"></a>查看記錄和輸出

使用[筆記本](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)widget 來查看執行和記錄的進度。 Widget 是非同步，而且會在定型完成之前提供更新。 Jupyter 和 JupterLab 也支援 Azure Machine Learning widget。

## <a name="change-the-notebook-environment"></a>變更筆記本環境

[筆記本] 工具列可讓您變更您的筆記本執行所在的環境。  

這些動作不會變更筆記本狀態或筆記本中任何變數的值：

|動作  |結果  |
|---------|---------| --------|
|停止核心     |  停止任何正在執行的儲存格。 執行儲存格會自動重新開機核心。 |
|流覽至另一個工作區區段     |     執行中的儲存格已停止。 |

這些動作將會重設筆記本狀態，並會重設筆記本中的所有變數。

|動作  |結果  |
|---------|---------| --------|
| 變更核心 | 筆記本使用新的核心 |
| 切換計算    |     筆記本會自動使用新的計算。 |
| 重設計算 | 當您嘗試執行資料格時，再次啟動 |
| 停止計算     |    不會執行任何資料格  |
| 在 Jupyter 或 JupyterLab 中開啟筆記本     |    在新索引標籤中開啟的筆記本。  |

### <a name="add-new-kernels"></a>加入新的核心

筆記本會自動尋找已連線計算實例上安裝的所有 Jupyter 核心。  若要將核心新增至計算實例：

1. 選取 [筆記本] 工具列中的 [[**開啟終端**](#terminal)機]。
1. 使用 [終端機] 視窗來建立新的環境。
1. 啟用環境。  例如，建立`newenv`之後：

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

可以安裝任何[可用的 Jupyter](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)核心。

### <a name="status-indicators"></a>狀態指示器

[**計算**] 下拉式清單旁邊的指標會顯示其狀態。  此狀態也會顯示在下拉式清單中。  

|Color |計算狀態 |
|---------|---------| 
| 綠色 | 計算正在執行 |
| 紅色 |計算失敗 | 
| 黑色 | 已停止計算 |
|  淺藍色 |計算建立、啟動、重新開機、設定 |
|  灰色 |計算刪除，正在停止 |

[**核心**] 下拉式清單旁的指標會顯示其狀態。

|Color |核心狀態 |
|---------|---------|
|  綠色 |核心連線、閒置、忙碌中|
|  灰色 |核心未連線 |

## <a name="find-compute-details"></a>尋找計算詳細資料 

在[studio](https://ml.azure.com)的 [**計算**] 頁面上尋找計算實例的詳細資料。

## <a name="next-steps"></a>後續步驟

* [執行您的第一個實驗](tutorial-1st-experiment-sdk-train.md)
* [使用快照集備份檔案儲存體](../storage/files/storage-snapshots-files.md)
