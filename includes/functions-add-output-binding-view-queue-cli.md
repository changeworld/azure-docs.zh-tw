---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90606719"
---
您可以在 [Azure 入口網站](../articles/storage/queues/storage-quickstart-queues-portal.md)或 [Microsoft Azure 儲存體總管](https://storageexplorer.com/)中檢視佇列。 您也可以在 Azure CLI 中檢視佇列，如下列步驟所說明：

1. 開啟函式專案的 *local.setting.json* 檔案，並複製連接字串值。 在終端機或命令視窗中，執行下列命令 (請貼上您的特定連接字串以取代 `<MY_CONNECTION_STRING>`)，以建立名為 `AZURE_STORAGE_CONNECTION_STRING`的環境變數。 (此環境變數意味著您無須使用 `--connection-string` 引數將連接字串提供給每個後續命令。)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (選擇性) 使用 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 命令檢視您帳戶中的儲存體佇列。 此命令的輸出應該會包含名為 `outqueue` 的佇列，這是函式將其第一個訊息寫入至該佇列時所建立的。
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. 使用 [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) 命令來讀取此佇列中的訊息，這應該是您先前測試函式時所使用的名字。 此命令會讀取並移除佇列中的第一個訊息。 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    此指令碼會使用 certutil 將本機暫存檔案中的 base64 編碼訊息集合解碼。 如果沒有任何輸出，請嘗試從指令碼中移除 `> NUL`，以停止隱藏 certutil 輸出 (以防萬一發生錯誤)。 
    
    ---
    
    由於訊息本文會以 [base64 編碼](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)儲存，因此必須先將訊息解碼才會顯示。 執行 `az storage message get` 之後，就會從佇列中移除此訊息。 如果 `outqueue` 中只有一個訊息，則當您第二次執行此命令時，將不會擷取訊息，而是會收到錯誤。
