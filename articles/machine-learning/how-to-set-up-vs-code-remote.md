---
title: 互動式調試： VS Code & ML 計算實例
titleSuffix: Azure Machine Learning
description: 設定 VS Code Remote，以互動方式使用 Azure Machine Learning 來偵錯工具代碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169744"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>以互動方式在具有 VS Code 遠端的 Azure Machine Learning 計算實例上進行偵錯工具

在本文中，您將瞭解如何在 Azure Machine Learning 計算實例上設定 Visual Studio Code 遠端，讓您可以從 VS Code**以互動方式進行程式碼的偵錯工具**。 

+ [Azure Machine Learning 計算實例](concept-compute-instance.md)是一種完全受控的雲端式工作站，適用于資料科學家，並為 IT 系統管理員提供管理和企業就緒功能。 


+ [Visual Studio Code 遠端](https://code.visualstudio.com/docs/remote/remote-overview)開發可讓您使用容器、遠端電腦或適用于 Linux 的 Windows 子系統（WSL）作為功能完整的開發環境。 

## <a name="prerequisite"></a>必要條件  

在 Windows 平臺上，您必須[安裝 OpenSSH 相容的 SSH 用戶端](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client)（如果尚未存在的話）。 

> [!Note]
> Windows 上不支援 PuTTY，因為 ssh 命令必須在路徑中。 

## <a name="get-ip-and-ssh-port"></a>取得 IP 和 SSH 埠 

1. 前往 https://ml.azure.com/的 Azure Machine Learning studio。

2. 選取您的[工作區](concept-workspace.md)。
1. 按一下 [**計算實例**] 索引標籤。
1. 在 [**應用程式 URI** ] 資料行中，按一下您想要用來做為遠端計算之計算實例的**SSH**連結。 
1. 在對話方塊中，記下 [IP 位址] 和 [SSH 埠]。 
1. 將您的私密金鑰儲存到本機電腦上的 ~/.ssh/目錄;例如，開啟新檔案的編輯器，並在中貼上金鑰： 

   **Linux**： 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**： 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   私密金鑰看起來會像這樣：
   ```
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

## <a name="add-instance-as-a-host"></a>將實例新增為主機 

在編輯器中開啟 `~/.ssh/config` （Linux）或 `C:\Users<username>.ssh\config` （Windows）檔案，並新增類似以下的專案：

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
|Host|針對計算實例使用您喜歡的任何縮寫 |
|HostName|這是計算實例的 IP 位址 |
|連接埠|這是上述 SSH 對話方塊上顯示的埠 |
|User|這必須 `azureuser` |
|IdentityFile|應該指向您儲存私密金鑰的檔案 |

現在，您應該能夠使用您在上面使用的縮寫來以 ssh 連線到您的計算實例，`ssh azmlci1`。 

## <a name="connect-vs-code-to-the-instance"></a>將 VS Code 連接到實例 

1. [安裝 Visual Studio Code](https://code.visualstudio.com/)。

1. [安裝遠端 SSH 延伸](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)模組。 

1. 按一下左側的 [遠端 SSH] 圖示，以顯示您的 SSH 設定。

1. 以滑鼠右鍵按一下您剛才建立的 SSH 主機設定。

1. 選取 **[連接到目前視窗中的主機**]。 

從這裡開始，您會完全使用計算實例，而且您現在可以編輯、偵錯工具、使用 git、使用延伸模組等等，如同您可以使用本機 Visual Studio Code。 

## <a name="next-steps"></a>後續步驟

既然您已設定 Visual Studio Code 遠端，您就可以使用計算實例做為 Visual Studio Code 的遠端計算，以互動方式來對程式碼進行驗證。 

[教學課程：將您的第一個 ML 模型定型](tutorial-1st-experiment-sdk-train.md)示範如何使用具有整合式筆記本的計算實例。