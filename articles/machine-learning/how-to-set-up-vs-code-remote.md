---
title: 'Visual Studio Code (preview 中連接到計算實例) '
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Visual Studio Code 中連接到 Azure Machine Learning 計算實例
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jmartens
author: j-martens
ms.date: 09/03/2020
ms.openlocfilehash: 2c7ff633705d3db327c563b41ce199a5342dda82
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89461556"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>在 Visual Studio Code (preview 中連接到 Azure Machine Learning 計算實例) 

在本文中，您將瞭解如何使用 Visual Studio Code 連接到 Azure Machine Learning 的計算實例。

[Azure Machine Learning 計算實例](concept-compute-instance.md)是完全受控的雲端式工作站，適用于資料科學家，並為 IT 系統管理員提供管理和企業就緒功能。

有兩種方式可從 Visual Studio Code 連接到計算實例：

* 遠端 Jupyter Notebook server。 此選項可讓您將計算實例設定為遠端 Jupyter Notebook 伺服器。
* [Visual Studio Code 遠端開發](https://code.visualstudio.com/docs/remote/remote-overview)。 Visual Studio Code 遠端開發可讓您使用容器、遠端電腦或 Windows 子系統 Linux 版 (WSL) 作為功能完整的開發環境。

## <a name="configure-compute-instance-as-remote-notebook-server"></a>將計算實例設定為遠端筆記本伺服器

為了將計算實例設定為遠端 Jupyter Notebook 伺服器，您需要幾個必要條件：

* Azure Machine Learning Visual Studio Code 擴充功能。 如需詳細資訊，請參閱 [Azure Machine Learning Visual Studio Code 延伸模組設定指南](tutorial-setup-vscode-extension.md)。
* Azure Machine Learning 工作區。 [使用 Azure Machine Learning Visual Studio Code 擴充功能來建立新的工作區](how-to-manage-resources-vscode.md#create-a-workspace) （如果您還沒有的話）。

若要連接到計算實例：

1. 在 Visual Studio Code 中開啟 Jupyter Notebook。
1. 當整合式筆記本體驗載入時，請選取 [ **Jupyter 伺服器**]。

    > [!div class="mx-imgBorder"]
    > ![啟動 Azure Machine Learning 遠端 Jupyter 筆記本伺服器下拉式清單](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    或者，您也可以使用命令選擇區：

    1. 從功能表列中選取 [檢視] > [命令選擇區]，以開啟命令選擇區。
    1. 在文字方塊中輸入 `Azure ML: Connect to Compute instance Jupyter server` 。

1. `Azure ML Compute Instances`從 Jupyter 伺服器選項清單中選擇。
1. 從訂用帳戶清單中選取您的訂用帳戶。 如果您先前已設定預設 Azure Machine Learning 工作區，則會略過此步驟。
1. 選取您的工作區。
1. 從清單中選取您的計算實例。 如果您沒有帳戶，請選取 [ **建立新的 Azure ML Compute 實例** ]，然後依照提示建立一個。
1. 您必須重載 Visual Studio Code，才能讓變更生效。
1. 開啟 Jupyter Notebook 並執行儲存格。

> [!IMPORTANT]
> 您 **必須** 執行資料格才能建立連接。

此時，您可以繼續在 Jupyter 筆記本中執行儲存格。

> [!TIP]
> 您也可以使用 Python 腳本檔案 ( .py) 包含類似 Jupyter 的程式碼儲存格。 如需詳細資訊，請參閱 [Visual Studio Code Python 互動式檔](https://code.visualstudio.com/docs/python/jupyter-support-py)。

## <a name="configure-compute-instance-remote-development"></a>設定計算實例遠端開發

如需完整功能的遠端開發體驗，您需要幾個必要條件：

* [Visual Studio Code 遠端 SSH 延伸](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)模組。
* 啟用 SSH 的計算實例。 如需詳細資訊， [請參閱建立計算實例指南](concept-compute-instance.md#create)。

> [!NOTE]
> 在 Windows 平臺上，您必須 [安裝與 OpenSSH 相容的 SSH 用戶端](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) （如果沒有的話）。 因為 ssh 命令必須在路徑中，所以 Windows 不支援 PuTTY。

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>取得您的計算實例的 IP 和 SSH 埠

1. 移至 Azure Machine Learning studio https://ml.azure.com/ 。
2. 選取您的 [工作區](concept-workspace.md)。
1. 按一下 [ **計算實例** ] 索引標籤。
1. 在 [ **應用程式 URI** ] 欄位中，按一下您想要用來做為遠端計算的計算實例的 [ **SSH** ] 連結。 
1. 在對話方塊中，記下 [IP 位址] 和 [SSH 埠]。 
1. 將您的私密金鑰儲存至本機電腦上的 ~/.ssh/目錄;例如，針對新檔案開啟編輯器，並將金鑰貼入： 

   **Linux**：

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**：

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   私密金鑰看起來會像這樣：

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. 變更檔案的許可權，以確保只有您可以讀取檔案。  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>將實例新增為主機

`~/.ssh/config`在編輯器中開啟 (Linux) 或 `C:\Users<username>.ssh\config` (Windows) 的檔案，並新增類似以下內容的新專案：

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

以下是欄位的一些詳細資料：

|欄位|描述|
|----|---------|
|主機|針對計算實例使用您喜歡的任何速記 |
|HostName|這是計算實例的 IP 位址 |
|Port|這是上述 SSH 對話方塊中顯示的埠 |
|User|這必須是 `azureuser` |
|IdentityFile|應指向您儲存私密金鑰的檔案 |

現在，您應該能夠使用您在上面使用的縮寫，以 ssh 連線到您的計算實例 `ssh azmlci1` 。

### <a name="connect-vs-code-to-the-instance"></a>將 VS Code 連接到實例

1. 按一下 [Visual Studio Code] 活動列中的 [遠端 SSH] 圖示，以顯示您的 SSH 設定。

1. 以滑鼠右鍵按一下您剛才建立的 SSH 主機設定。

1. 選取 **[連接到目前視窗中的主機**]。 

從這裡開始，您會完全使用計算實例，而且您現在可以編輯、偵測、使用 git、使用延伸模組等，就像您可以使用本機 Visual Studio Code 一樣。

## <a name="next-steps"></a>接下來的步驟

現在您已設定 Visual Studio Code 遠端，您可以使用計算實例作為 Visual Studio Code 的遠端計算，以 [互動方式進行程式碼的偵錯工具](how-to-debug-visual-studio-code.md)。

[教學課程：進行第一個 ML 模型的定型](tutorial-1st-experiment-sdk-train.md)會示範如何搭配使用計算執行個體與整合式筆記本。