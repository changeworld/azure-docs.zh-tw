---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: e9465e014c1b8770f8968cbee89a5b455ad53470
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424626"
---
## <a name="run-the-function-in-azure"></a>在 Azure 中執行函式

1. 返回 [Azure：  函式] 區域 (位於提要欄位中)，在訂用帳戶下展開新的函式應用程式。 展開 [函式]，以滑鼠右鍵按一下 (Windows) 或按住 <kbd>Ctrl</kbd> 並在 (macOS) **HttpExample** 上按一下，然後選擇 [複製函式 URL]。

    ![複製新 HTTP 觸發程序的函數 URL](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. 將 HTTP 要求的這個 URL 貼到瀏覽器的網址列中，並在此 URL 的結尾處新增 `name` 查詢字串 `?name=Functions`，然後執行要求。 呼叫 HTTP URL 觸發函式的 URL 應採用下列格式：

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/httpexample?name=Functions
    ```

    下列範例顯示瀏覽器中對於函式所傳回遠端 GET 要求所做出的回應︰

    ![瀏覽器中的函式回應](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
