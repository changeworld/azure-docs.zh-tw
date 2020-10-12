---
title: Azure Cloud Shell 概觀 | Microsoft Docs
description: Azure Cloud Shell 的概觀。
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/20/2020
ms.author: damaerte
ms.openlocfilehash: f824bddf833a1e2c01a3b779abc2c5252d8e0547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89468652"
---
# <a name="overview-of-azure-cloud-shell"></a>Azure Cloud Shell 的概觀

Azure Cloud Shell 是可經由瀏覽器存取的已驗證互動式殼層，應用在 Azure 資源管理上。 它可讓您彈性地選擇最適合您工作方式的殼層體驗 (Bash 或 PowerShell)。

您可以透過下列三種方式存取 Cloud Shell：

- **直接連結**：開啟瀏覽器 [https://shell.azure.com](https://shell.azure.com) 。

- **Azure 入口網站**：選取 [Azure 入口網站](https://portal.azure.com)上的 Cloud Shell 圖示：

    ![從 Azure 入口網站啟動 Cloud Shell 的圖示](media/overview/portal-launch-icon.png)

- **程式碼片段**：在 [ [docs.microsoft.com]() ] 和 [ [Microsoft Learn](/learn/)] 上，選取出現 Azure CLI 和 Azure PowerShell 程式碼片段的 [ **試試看** ] 按鈕：

    ```azurecli-interactive
    az account show
    ```

    ```azurepowershell-interactive
    Get-AzSubscription
    ```

    [ **試試看** ] 按鈕會在檔中使用 Bash (（適用于 Azure CLI 程式碼片段) 或 PowerShell (Azure PowerShell 程式碼片段) ）直接開啟 Cloud Shell。

    若要執行命令，請使用程式碼片段中的 [**複製**]、使用**Ctrl** + **shift** + **v** (Windows/Linux) 或**Cmd** + **shift** + **v** (macOS) 貼上命令，然後按**enter**鍵。

## <a name="features"></a>特性

### <a name="browser-based-shell-experience"></a>以瀏覽器為基礎的體驗

Cloud Shell 以 Azure 管理工作為考量，提供以瀏覽器存取命令列的體驗。 使用 Cloud Shell 可以只有雲端才能提供的方式離開本機電腦工作。

### <a name="choice-of-preferred-shell-experience"></a>選擇慣用的殼層體驗

使用者可以在 Bash 或 PowerShell 之間進行選擇。

1. 選取 **Cloud Shell**。

    ![Cloud Shell 圖示](media/overview/overview-cloudshell-icon.png)

2. 選取 **Bash** 或 **PowerShell**。

    ![選擇 Bash 或 PowerShell](media/overview/overview-choices.png)

    第一次啟動之後，您可以使用 [shell 型別] 下拉式控制項，在 Bash 與 PowerShell 之間切換：

    ![選取 Bash 或 PowerShell 的下拉式控制項](media/overview/select-shell-drop-down.png)

### <a name="authenticated-and-configured-azure-workstation"></a>驗證及設定 Azure 工作站

Cloud Shell 由 Microsoft 管理，因此隨附受歡迎的命令列工具和語言支援。 Cloud Shell 也可安全地自動驗證，讓您可透過 Azure CLI 或 Azure PowerShell Cmdlet 快速存取您的資源。

檢視[安裝在 Cloud Shell 中的完整工具清單](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>整合式 Cloud Shell 編輯器

Cloud Shell 提供一種以開放原始碼 Monaco Editor 為基礎的整合式圖形化文字編輯器。 只需執行 `code .` 來透過 Azure CLI 或 Azure PowerShell 順暢地部署，即可建立和編輯設定。

[深入了解 Cloud Shell 編輯器](using-cloud-shell-editor.md)。

### <a name="multiple-access-points"></a>多個存取點

您可以從下列位置使用 Cloud Shell 這個彈性的工具：

* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI 檔](/cli/azure)
* [Azure PowerShell 文件](/powershell/azure/)
* [Azure 行動應用程式](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code Azure 帳戶延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>連線 Microsoft Azure 檔案儲存體

Cloud Shell 的機器是暫時性的，但您的檔案會以兩種方式保存：透過磁片映射，以及透過名為的已掛接檔案共用 `clouddrive` 。 第一次啟動時，Cloud Shell 會提示要代替您建立資源群組、儲存體帳戶及 Azure 檔案共用。 這是一次性的步驟，而且會針對所有工作階段自動連接。 單一檔案共用可以進行對應，並同時供 Cloud Shell 中的 Bash 和 PowerShell 使用。

深入瞭解以瞭解如何掛接新的 [或現有的儲存體帳戶](persisting-shell-storage.md) ，或瞭解 [Cloud Shell 中使用的持續性機制](persisting-shell-storage.md#how-cloud-shell-storage-works)。

> [!NOTE]
> Cloud shell 儲存體帳戶不支援 Azure 儲存體防火牆。

## <a name="concepts"></a>概念

* Cloud Shell 會在以每一工作階段、每位使用者為基礎所提供的暫存主機上執行
* Cloud Shell 會在無互動活動的 20 分鐘後逾時
* Cloud Shell 需要掛接 Azure 檔案共用
* Cloud Shell 會將相同的 Azure 檔案共用同時用於 Bash 和 PowerShell
* Cloud Shell 會以一台機器、一個使用者帳戶的方式指派
* Cloud Shell 會使用檔案共用中所保有的 5 GB 映像來保存 $HOME
* 權限設定為一般 Linux 使用者 (採用 Bash)

深入了解 [Cloud Shell 中的 Bash](features.md) 和 [Cloud Shell 中的 PowerShell](./features.md) 中的功能。

## <a name="pricing"></a>定價

裝載 Cloud Shell 的機器是免費提供的，但前提是必須掛接「Azure 檔案」共用。 所需成本和一般儲存體相同。

## <a name="next-steps"></a>後續步驟

[Cloud Shell 中 Bash 的快速入門](quickstart.md) <br>
[Cloud Shell 中 PowerShell 的快速入門](quickstart-powershell.md)