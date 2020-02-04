---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842105"
---
## <a name="run-the-function-locally"></a>在本機執行函式

Visual Studio Code 可與 [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) 整合，讓您能夠先在本機開發電腦上執行此專案，再發佈至 Azure。

1. 若要呼叫您的函式，請按 F5 以啟動函式應用程式專案。 Core Tools 的輸出會顯示在**終端機**面板中。

1. 如果您尚未安裝 Azure Functions Core Tools，請在出現提示時選取 [安裝]  。 安裝 Core Tools 後，您的應用程式將會在 [終端機]  面板中啟動。

1. 在**終端機**面板中，複製 HTTP 觸發函式的 URL 端點。 

    ![Azure 本機輸出](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. 將 HTTP 要求的 URL 貼到瀏覽器的網址列。 將查詢字串 `?name=<yourname>` 附加至此 URL 並執行 GET 要求。 

1. 此時會傳回回應，其內容在瀏覽器中會顯示如下：

    ![瀏覽器中的函式 localhost 回應](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. 按 Shift + F5 以停止 Core Tools，並中斷偵錯工具的連線。
