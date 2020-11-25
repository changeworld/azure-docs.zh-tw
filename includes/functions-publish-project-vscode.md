---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a525d1e14e642a64235c263ba29bf7a181bf9e30
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008151"
---
## <a name="publish-the-project-to-azure"></a>將專案發佈到 Azure

在這一節中，您會在 Azure 訂用帳戶中建立函式應用程式和相關資源，然後部署程式碼。

> [!IMPORTANT]
> 發佈至現有的函式應用程式會覆寫該應用程式在 Azure 中的內容。


1. 選擇活動列中的 Azure 圖示，然後在 [Azure：  函式] 區域中，選擇 [部署至函式應用程式...]  按鈕。

    ![將專案發佈至 Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. 依照提示提供下列資訊：

    - **選取資料夾**：從您的工作區選擇資料夾，或瀏覽至其中一個包含您函式應用程式的資料夾。 如果您已經開啟有效的函式應用程式，您就不會看到此提示。

    - **選取訂用帳戶**：選擇要使用的訂用帳戶。 如果您只有一個訂用帳戶，就不會看見此選項。

    - **在 Azure 中選取函式應用程式**：選擇 `- Create new Function App`。 (請勿選擇本文中未涵蓋的 `Advanced` 選項)。
      
    - **輸入函式應用程式的全域唯一名稱**：輸入在 URL 路徑中有效的名稱。 您輸入的名稱會進行驗證，以確定該名稱在 Azure Functions 中是唯一的。
    
    - **選取新資源的位置**：若要獲得較佳的效能，請選擇您附近的 [區域](https://azure.microsoft.com/regions/)。 
    
1.  完成時，系統會依您的函式應用程式名稱，在訂用帳戶中建立下列 Azure 資源：
    
    - 資源群組，這是相關資源的邏輯容器。
    - 標準 Azure 儲存體帳戶，其可維護專案的狀態和其他資訊。
    - 耗用量方案，定義無伺服器函式應用程式的基礎主機。 
    - 函式應用程式，可提供用來執行函式程式碼的環境。 函式應用程式可讓您將多個函式群組為邏輯單位，以方便您管理、部署，並在相同主控方案中共用資源。
    - 連線至函式應用程式的 Application Insights 執行個體，可追蹤無伺服器函式的使用量。

    建立函式應用程式並套用部署套件之後，即會顯示通知。 
    
1. 在通知中選取 [檢視輸出]  ，即可檢視建立和部署結果，包括您所建立的 Azure 資源。 如果您錯過通知，請選取右下角的鈴鐺圖示，以再次查看。

    ![建立完成通知](media/functions-publish-project-vscode/function-create-notifications.png)
