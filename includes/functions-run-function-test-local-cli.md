---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 0bb0c22227946cba6790b536b3cb8db24af3ccbc
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422855"
---
## <a name="run-the-function-locally"></a>在本機執行函式

1. 啟動 *LocalFunctionProj* 資料夾中的本機 Azure Functions 執行階段主機，以執行您的函式：

    ```
    func start
    ```

    在輸出的結尾處，應該會出現下列幾行： 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > 如果 HttpExample 未如下顯示，表示您可能不是從專案的根資料夾啟動主機。 在此情況下，請使用 **Ctrl**+**C** 停止主機，瀏覽至專案的根資料夾，然後再次執行先前的命令。

1. 從這個輸出中將 `HttpExample` 函式的 URL 複製到瀏覽器，並附加查詢字串 `?name=<YOUR_NAME>`，使其成為完整的 URL (如 `http://localhost:7071/api/HttpExample?name=Functions`)。 瀏覽器應該會顯示類似於 `Hello Functions` 的訊息：

    ![在本機瀏覽器中執行函式的結果](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. 您在其中啟動專案的終端機，也會在您提出要求時顯示記錄輸出。

1. 完成作業後，請使用 **Ctrl**+**C** 並選擇 `y` 以停止函式主機。
