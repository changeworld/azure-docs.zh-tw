---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842102"
---
## <a name="publish-the-project-to-azure"></a>將專案發佈到 Azure

在這一節中，您會在 Azure 訂用帳戶中建立函式應用程式和相關資源，然後部署程式碼。 

1. 選擇活動列中的 Azure 圖示，然後在 [Azure：  函式] 區域中，選擇 [部署至函式應用程式...]  按鈕。

    ![將專案發佈至 Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. 依照提示提供下列資訊：

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | Prompt | 值 | 描述 |
    | ------ | ----- | ----- |
    | 選取訂閱 | 您的訂用帳戶 | 當您有多個訂用帳戶時顯示。 |
    | 在 Azure 中選取函式應用程式 | + 建立新的函式應用程式 | 發佈至現有的函式應用程式會覆寫該應用程式在 Azure 中的內容。 |
    | 輸入函式應用程式的全域唯一名稱 | 唯一的名稱 | 函式應用程式名稱的有效字元為 `a-z`、`0-9` 和 `-`。 |
    | 選取新資源的位置 | 區域 | 選擇您附近的[區域](https://azure.microsoft.com/regions/)。 | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | Prompt | 值 | 描述 |
    | ------ | ----- | ----- |
    | 選取訂閱 | 您的訂用帳戶 | 當您有多個訂用帳戶時顯示。 |
    | 在 Azure 中選取函式應用程式 | + 建立新的函式應用程式 | 發佈至現有的函式應用程式會覆寫該應用程式在 Azure 中的內容。 |
    | 輸入函式應用程式的全域唯一名稱 | 唯一的名稱 | 函式應用程式名稱的有效字元為 `a-z`、`0-9` 和 `-`。 |
    | 選取執行階段 | 您的版本 | 選擇您在本機上執行的語言版本。 |
    | 選取新資源的位置 | 區域 | 選擇您附近的[區域](https://azure.microsoft.com/regions/)。 | 

    ::: zone-end

    
1.  完成時，您的訂用帳戶中會建立下列 Azure 資源：

    + **[資源群組](../articles/azure-resource-manager/management/overview.md)** ：包含所有已建立的 Azure 資源。 該名稱是以您的函數應用程式名稱為根據。
    + **[儲存體帳戶](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** ：使用以您的函數應用程式名稱為根據的唯一名稱建立標準儲存體帳戶。
    + **[主控方案](../articles/azure-functions/functions-scale.md)** ：在美國西部區域建立一個取用方案，來裝載您的無伺服器函數應用程式。
    + **函數應用程式**：您的專案已部署到此新函數應用程式，並在其中執行。
    + **[Application Insights]()** ：系統會根據您的函式名稱建立連線至函式應用程式的執行個體。

    建立函式應用程式並套用部署套件之後，即會顯示通知。 
    
1. 在通知中選取 [檢視輸出]  ，即可檢視建立和部署結果，包括您所建立的 Azure 資源。

    ![建立完成通知](media/functions-publish-project-vscode/function-create-notifications.png)

1. 返回 [Azure：  函式] 區域 (位於提要欄位中)，在訂用帳戶下展開新的函式應用程式。 展開 [函式]  ，以滑鼠右鍵按一下 (Windows) 或按住 Ctrl 並在 (MacOS) **HttpExample** 上按一下，然後選擇 [複製函式 URL]  。

    ![複製新 HTTP 觸發程序的函數 URL](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
