---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424657"
---
## <a name="invoke-the-function-on-azure"></a>在 Azure 上叫用函式

由於您的函式會使用 HTTP 觸發程序，因此您在叫用函式時，可以在瀏覽器中對其 URL 提出 HTTP 要求，或使用 curl 之類的工具。 

# <a name="browser"></a>[瀏覽器](#tab/browser)

將發佈命令的輸出中顯示的完整 **叫用 URL** 複製到瀏覽器網址列中 (請附加查詢參數 `&name=Functions`)。 瀏覽器應該會顯示與您在本機執行函式時類似的輸出。

![使用瀏覽器在 Azure 上執行函式的輸出](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

使用 **叫用 URL** 來執行 [`curl`](https://curl.haxx.se/) (請附加參數 `&name=Functions`)。 命令的輸出應該是文字 "Hello Functions"。

![使用 curl 在 Azure 上執行函式的輸出](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
