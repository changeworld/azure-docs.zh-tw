---
title: 如何在您的工作區中執行 Jupyter 筆記本
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning studio 中執行 Jupyter 筆記本，而不需要離開您的工作區。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 7bb1ce8141f609feb4f354aa85f202915e197f37
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599235"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>如何在工作區中執行 Jupyter Notebook

瞭解如何在 Azure Machine Learning studio 中，直接在您的工作區中執行 Jupyter 筆記本。 您可以啟動 [Jupyter](https://jupyter.org/) 或 [JupyterLab](https://jupyterlab.readthedocs.io)，也可以在不離開工作區的情況下編輯和執行您的筆記本。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://aka.ms/AMLFree)。
* Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

## <a name="create-notebooks"></a><a name="create"></a> 建立筆記本

在您的 Azure Machine Learning 工作區中，建立新的 Jupyter 筆記本並開始工作。 新建立的筆記本會儲存在預設工作區儲存體中。 此筆記本可與具有該工作區存取權的任何人共用。 

建立新的筆記本： 

1. 在 [Azure Machine Learning Studio](https://ml.azure.com) 中開啟您的工作區。
1. 選取左側的 [筆記本]。 
1. 在 [我的檔案] 區段中，選取 [使用者檔案] 清單上方的 **建立新檔案** 圖示。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="建立新檔案":::

1. 為檔案命名。 
1. 針對 Jupyter 筆記本檔案，選取 [ **筆記本** ] 作為檔案類型。
1. 選取檔案目錄。
1. 選取 [建立]。

您也可以建立文字檔。  選取 [文字] 做為檔案類型，並將副檔名加入名稱 (例如，myfile.py 或 myfile.txt .txt)。  

您也可以使用 [筆記本] 頁面頂端的工具上傳資料夾和檔案，包括筆記本。  在 [預覽] 區段中會顯示筆記本和大部分的文字檔案類型。  其他檔案類型大多不能預覽。

> [!IMPORTANT]
> 筆記本和腳本中的內容可能會從您的會話讀取資料，並在您的 Azure 中存取資料，而不需要您的組織。  只從受信任的來源載入檔案。 如需詳細資訊，請參閱 [安全程式碼的最佳做法](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)。

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
1. 選取 **計算** 目標，或建立一個新的，並等候它正在執行。
1. 選取 **開啟終端機** 圖示。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="開啟終端機":::

1. 如果您沒有看到此圖示，請選取計算目標右邊的 **...** ，然後選取 [ **開啟終端** 機]。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="從 ... 開啟終端機":::


瞭解如何[將 Git 存放庫複製到您的工作區檔案系統](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)。

### <a name="copy-and-paste-in-terminal"></a>在終端機中複製並貼上

> * Windows： `Ctrl-Insert` 複製並使用 `Ctrl-Shift-v` 或 `Shift-Insert` 貼上。
> * Mac 作業系統：`Cmd-c` 進行複製，`Cmd-v` 即可貼上。
> * FireFox/IE 可能無法正確支援剪貼簿權限。

### <a name="share-notebooks-and-other-files"></a>共用筆記本和其他檔案

複製並貼上 URL，以共用筆記本或檔案。  只有工作區的其他使用者可以存取此 URL。  瞭解如何[授與對工作區的存取權](how-to-assign-roles.md)。

## <a name="edit-a-notebook"></a>編輯筆記本

若要編輯筆記本，開啟工作區的 [使用者檔案] 區段中的任何筆記本。 按一下您要編輯的儲存格。 

您可以編輯筆記本，而不需要連接到計算實例。  當您想要在筆記本中執行資料格時，請選取或建立計算實例。  如果您選取已停止的計算實例，則會在您執行第一個資料格時自動啟動。

當計算實例正在執行時，您也可以在任何 Python 筆記本中使用 [Intellisense](https://code.visualstudio.com/docs/editor/intellisense)支援的程式碼完成。

您也可以從筆記本工具列啟動 Jupyter 或 JupyterLab。  Azure Machine Learning 不會提供 Jupyter 或 JupyterLab 的更新和修正錯誤，因為它們是 Microsoft 支援服務界限外的開放原始碼產品。

### <a name="focus-mode"></a>焦點模式

使用焦點模式來展開目前的視圖，讓您可以將焦點放在使用中的索引標籤上。 焦點模式會隱藏 [筆記本檔案瀏覽器]。

1. 在終端機視窗工具列中，選取 [ **焦點模式]** 以開啟焦點模式。 視您的視窗寬度而定，這可能位於工具列的 [ **...** ] 功能表項目底下。
1. 在焦點模式中，選取 [ **標準] view** 以返回標準視圖。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="切換焦點模式/標準視圖":::


### <a name="use-intellisense"></a>使用 IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) 是一種程式碼完成的輔助工具，其中包含許多功能：列出成員、參數資訊、快速諮詢和完成文字。 這些功能有助於深入了解您使用的程式碼、追蹤所鍵入的參數，以及幾個按鍵即可新增屬性和方法呼叫。  

輸入程式碼時，請使用 Ctrl + 空格鍵來觸發 IntelliSense。

### <a name="clean-your-notebook-preview"></a>清理您的筆記本 (預覽版) 

> [!IMPORTANT]
> 收集功能目前處於公開預覽狀態。
> 此預覽版本會在沒有服務等級協定的情況下提供，不建議用於實際執行工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在建立筆記本的過程中，您通常會得到用於資料探索或偵測的資料格。 *收集* 功能可協助您在沒有這些多餘儲存格的情況下產生乾淨的筆記本。

1. 執行您所有的筆記本儲存格。
1. 選取包含您想要新筆記本執行之程式碼的資料格。 例如，提交實驗的程式碼，或可能註冊模型的程式碼。
1. 選取出現在資料格工具列上的 **收集** 圖示。
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="螢幕擷取畫面：選取收集圖示":::
1. 輸入新「收集的」筆記本的名稱。  

新的筆記本只會包含程式碼儲存格，而且所有儲存格都需要產生與您選取要收集的資料格相同的結果。

### <a name="save-and-checkpoint-a-notebook"></a>儲存並檢查筆記本的檢查點

當您建立 *.ipynb* 檔案時，Azure Machine Learning 建立檢查點檔案。

在筆記本工具列中，選取功能表，然後選取 **[ &gt; 儲存和檢查點** ] 以手動方式儲存筆記本，它會新增與筆記本相關聯的檢查點檔案。

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="筆記本工具列中 [儲存] 工具的螢幕擷取畫面":::

每隔30秒會自動儲存每個筆記本。 [自動儲存] 只會更新初始 *.ipynb* 檔，而不會更新檢查點檔案。
 
選取 [筆記本] 功能表中的 [ **檢查點** ]，以建立命名檢查點，並將筆記本還原至已儲存的檢查點。

## <a name="delete-a-notebook"></a>刪除筆記本

您「無法」刪除 **Samples** 筆記本。  這些筆記本是 Studio 的一部分，而且會在每次發佈新的 SDK 時更新。  

您「可以」透過下列任何方式刪除 **使用者檔案** 筆記本：

* 在 Studio 中，選取資料夾或檔案後面的 [...]。  請務必使用支援的瀏覽器 (Microsoft Edge、Chrome 或 Firefox)。
* 從任何筆記本工具列中，選取 [ [**開啟終端**](#terminal)  機] 以存取計算實例的終端機視窗。
* 使用 Jupyter 或 JupyterLab 各自的工具。

## <a name="run-a-notebook-or-python-script"></a>執行筆記本或 Python 腳本

若要執行筆記本或 Python 腳本，您必須先連接到執行中的 [計算實例](concept-compute-instance.md)。 如果您沒有計算執行個體，請使用下列步驟建立一個： 

1. **+** 在筆記本或腳本工具列中選取。 
2. 為計算命名，然後選擇 [虛擬機器大小]。 
3. 選取 [建立]。
4. 計算實例會自動連接到檔案。  您現在可以使用計算實例左邊的工具來執行筆記本儲存格或 Python 腳本

只有您可以查看和使用您所建立的計算執行個體。  您的 **使用者檔案** 會與 VM 分開儲存，而且由工作區中的所有計算執行個體共用。

### <a name="view-logs-and-output"></a>檢視記錄和輸出

使用 [筆記本](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) widget 來查看執行和記錄的進度。 小工具沒有同步，會一直提供更新直到定型完成。 Jupyter 和 JupterLab 也支援 Azure Machine Learning 小工具。

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="螢幕擷取畫面： Jupyter 筆記本 widget ":::

## <a name="explore-variables-in-the-notebook"></a>探索筆記本中的變數

在筆記本工具列上，使用 [ **變數瀏覽器** ] 工具來顯示已在筆記本中建立之所有變數的名稱、類型、長度和範例值。

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="螢幕擷取畫面： Variable explorer 工具":::

選取工具以顯示 [變數瀏覽器] 視窗。

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="螢幕擷取畫面：變數瀏覽器視窗":::

## <a name="navigate-with-a-toc"></a>使用 TOC 流覽

在筆記本工具列上，使用 [  **目錄** ] 工具來顯示或隱藏目錄。  啟動具有標題的 markdown 資料格，以將其新增至目錄。 按一下資料表中的專案，以滾動到筆記本中的該資料格。  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="螢幕擷取畫面：筆記本中的目錄":::

## <a name="change-the-notebook-environment"></a>變更筆記本環境

筆記本工具列可讓您變更您的筆記本執行所在的環境。  

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

筆記本會自動尋找已連線的計算實例上安裝的所有 Jupyter 核心。  將核心程序加入計算執行個體：

1. 選取筆記本工具列中的 [ [**開啟終端**](#terminal) 機]。
1. 使用終端機視窗建立新的環境。  例如，下列程式碼會建立 `newenv` ：
    ```shell
    conda create -y --name newenv
    ```
1. 啟用環境。  例如，建立 `newenv` 之後：

    ```shell
    conda activate newenv
    ```
1. 將 pip 和 ipykernel 套件安裝至新環境，並為該 conda 環境建立核心

    ```shell
    conda install -y pip
    conda install -y ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

> [!NOTE]
> 針對筆記本內的套件管理，請使用 **% pip** 或 **% conda** 魔術函式將套件自動安裝 **到目前** 執行中的核心，而不是代表所有套件的 **！ pip** 或 **！ conda** ， (包括目前執行中核心以外的套件) 

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

## <a name="shortcut-keys"></a>快速鍵
類似于 Jupyter 筆記本，Azure Machine Learning Studio 筆記本具有模式使用者介面。 鍵盤會根據筆記本儲存格所在的模式來執行不同的動作。 Azure Machine Learning Studio 筆記本支援指定程式碼資料格的下列兩種模式：命令模式和編輯模式。

### <a name="command-mode-shortcuts"></a>命令模式快速鍵

沒有文字游標提示您輸入時，儲存格就會處於命令模式。 儲存格處於命令模式時，您可以將筆記本當做整體編輯，但無法輸入個別的儲存格。 按下 `ESC` 或使用滑鼠選取資料格的編輯器區域以外的地方，進入命令模式。  作用中資料格的左邊框線為藍色和實線，而其 [ **執行** ] 按鈕為藍色。

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="命令模式中的筆記本儲存格 ":::

| 快速鍵                      | 描述                          |
| ----------------------------- | ------------------------------------|
| Enter                         | 進入編輯模式             |        
| Shift + Enter                 | 執行儲存格，選取下方         |     
| 控制/命令 + Enter       | 執行儲存格                            |
| Alt + Enter                   | 執行資料格，插入下方的程式碼資料格    |
| 控制/命令 + Alt + Enter | 執行資料格，插入下方的 markdown 資料格|
| Alt + R                       | 全部執行      |                       
| Y                             | 將儲存格轉換成程式碼    |                         
| M                             | 將資料格轉換成 markdown  |                       
| 向上/K                          | 選取上方儲存格    |               
| 下/J                        | 選取下方儲存格    |               
| A                             | 在上方插入程式碼儲存格  |            
| B                             | 在下方插入程式碼資料格   |           
| 控制/命令 + Shift + A   | 在上方插入 markdown 資料格    |      
| Control/Command + Shift + B   | 在下方插入 markdown 資料格   |       
| X                             | 剪下選取的資料格    |               
| C                             | 複製選取的資料格   |               
| Shift + V                     | 貼上選取的儲存格           |
| V                             | 貼上選取的儲存格    |       
| D D                           | 刪除選取的資料格|                
| O                             | TextWriter         |              
| Shift + O                     | 切換輸出滾動   |          
| 我                           | 中斷核心 |                   
| 0 0                           | 重新開機核心 |                     
| Shift + 空格鍵                 | 向上捲動  |                         
| Space                         | 向下捲動|
| 索引標籤                           | 將焦點變更為下一個可設定焦點的專案 (停用索引標籤停用) |
| 控制/命令 + S           | 儲存筆記本 |                      
| 1                             | 變更為 h1|                       
| 2                             | 變更為 h2|                        
| 3                             | 變更為 h3|                        
| 4                             | 變更為 h4 |                       
| 5                             | 變更為 h5 |                       
| 6                             | 變更為 h6 |                       

### <a name="edit-mode-shortcuts"></a>編輯模式快速鍵

編輯模式會以文字游標指示，提示您在編輯器區域中輸入。 當儲存格處於編輯模式時，您可以在資料格中輸入資料格。 按下 `Enter` 或使用滑鼠選取儲存格的編輯器區域，進入編輯模式。 現用儲存格的左邊框線為綠色和影線，而其 **執行** 按鈕為綠色。 您也會在編輯模式中的儲存格看到游標提示。

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="編輯模式中的筆記本儲存格":::

使用下列擊鍵快速鍵，在編輯模式下，您可以更輕鬆地在 Azure Machine Learning 筆記本中流覽並執行程式碼。

| 快速鍵                      | 描述|                                     
| ----------------------------- | ----------------------------------------------- |
| 逸出                        | 進入命令模式|  
| 控制/命令 + 空間       | 啟用 IntelliSense |
| Shift + Enter                 | 執行儲存格，選取下方 |                         
| 控制/命令 + Enter       | 執行儲存格  |                                      
| Alt + Enter                   | 執行資料格，插入下方的程式碼資料格  |              
| 控制/命令 + Alt + Enter | 執行資料格，插入下方的 markdown 資料格  |          
| Alt + R                       | 執行所有儲存格     |                              
| Up                            | 移動資料指標向上或上一個儲存格    |             
| Down                          | 向下移動游標或下一個資料格 |                  
| 控制/命令 + S           | 儲存筆記本   |                                
| 控制/命令 + 向上鍵          | 移至儲存格開端   |                             
| 控制/命令 + 向下鍵        | 移至儲存格末端 |                                 
| 索引標籤                           | 程式碼完成或縮排 (如果已啟用 tab 鍵補漏)  |
| 控制/命令 + M           | 啟用/停用索引標籤陷阱  |                       
| Control/Command +]           | 縮排 |                                         
| Control/Command + [           | Dedent  |                                        
| 控制/命令 + A           | 全選|                                      
| 控制/命令 + Z           | 復原 |                                           
| Control/Command + Shift + Z   | 取消復原 |                                           
| Control/Command + Y           | 取消復原 |                                           
| 控制/命令 + 首頁        | 移至儲存格開端|                                
| 控制/命令 + End         | 移至儲存格末端   |                               
| 控制/命令 + 左方        | 往左移一個單字 |                               
| Control/Command + Right       | 往右移一個單字 |                              
| 控制/命令 + 倒退鍵   | 刪除此前的文字 |                             
| 控制/命令 + 刪除      | 刪除此後的文字 |                              
| 控制/命令 +/           | 在 cu 上切換批註

## <a name="find-compute-details"></a>尋找計算詳細資料

在 [Studio](https://ml.azure.com)中的 [計算] 頁面上，尋找計算執行個體的詳細資料。

## <a name="next-steps"></a>後續步驟

* [執行您的第一個實驗](tutorial-1st-experiment-sdk-train.md)
* [使用快照集備份檔案儲存體](../storage/files/storage-snapshots-files.md)