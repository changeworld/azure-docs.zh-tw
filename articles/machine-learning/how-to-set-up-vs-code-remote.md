---
title: 互動式調試：VS 代碼& ML 計算實例
titleSuffix: Azure Machine Learning
description: 設置 VS 代碼遠端以使用 Azure 機器學習以交互調試代碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169744"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>使用 VS 代碼遠端在 Azure 機器學習計算實例上進行交互調試

在本文中，您將瞭解如何在 Azure 機器學習計算實例上設置視覺化工作室代碼遙控器，以便從 VS 代碼**以交互調試代碼**。 

+ [Azure 機器學習計算實例](concept-compute-instance.md)是面向資料科學家的完全託管的基於雲的工作站，為 IT 管理員提供管理和企業就緒功能。 


+ [視覺化工作室代碼遙控器](https://code.visualstudio.com/docs/remote/remote-overview)開發允許您使用容器、遠端電腦或適用于 Linux 的 Windows 子系統 （WSL） 作為功能齊全的開發環境。 

## <a name="prerequisite"></a>必要條件  

在 Windows 平臺上，如果尚未存在[，則必須安裝與 OpenSSH 相容的 SSH 用戶端](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client)。 

> [!Note]
> Windows 上不支援 PuTTY，因為 ssh 命令必須位於路徑中。 

## <a name="get-ip-and-ssh-port"></a>獲取 IP 和 SSH 埠 

1. 轉到 Azurehttps://ml.azure.com/機器學習工作室。

2. 選擇工作區[。](concept-workspace.md)
1. 按一下 **"計算實例"** 選項卡。
1. 在 **"應用程式 URI"** 列中，按一下要用作遠端計算的計算實例的**SSH**連結。 
1. 在對話方塊中，請注意 IP 位址和 SSH 埠。 
1. 將私密金鑰保存到本地電腦上的 @/.ssh/ 目錄;例如，打開新檔的編輯器並將金鑰粘貼到： 

   **Linux**： 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **視窗**： 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   私密金鑰將如下所示：
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. 更改檔的許可權，以確保只能讀取該檔。  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>將實例添加為主機 

在編輯器中`~/.ssh/config`打開檔（Linux）或`C:\Users<username>.ssh\config`（Windows）並添加新條目， 如下所示：

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

以下是欄位的一些詳細資訊： 

|欄位|描述|
|----|---------|
|Host|對計算實例使用任何你喜歡的速記 |
|HostName|這是計算實例的 IP 位址 |
|連接埠|這是上面 SSH 對話方塊中顯示的埠 |
|User|這需要 `azureuser` |
|身份檔|應指向保存私密金鑰的檔 |

現在，您應該能夠使用上面使用的速記對計算實例`ssh azmlci1`。 

## <a name="connect-vs-code-to-the-instance"></a>將 VS 代碼連接到實例 

1. [安裝視覺化工作室代碼](https://code.visualstudio.com/)。

1. [安裝遠端 SSH 擴展](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)。 

1. 按一下左側的遠端 SSH 圖示以顯示 SSH 配置。

1. 按右鍵您剛剛創建的 SSH 主機配置。

1. 選擇 **"在當前視窗中連接到主機**"。 

從現在開始，您完全在計算實例上工作，現在您可以編輯、調試、使用 git、使用擴展等 ， 就像使用本地視覺化工作室代碼一樣。 

## <a name="next-steps"></a>後續步驟

現在，您已經設置了 Visual Studio 代碼遠端，您可以使用計算實例作為從 Visual Studio Code 進行遠端計算，以交互調試代碼。 

[教程：訓練第一個 ML 模型](tutorial-1st-experiment-sdk-train.md)演示如何將計算實例與集成筆記本一起使用。