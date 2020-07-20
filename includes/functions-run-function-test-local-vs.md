---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056703"
---
1. 若要執行您的函式，請在 Visual Studio 中按 F5。 您可能需要啟用防火牆例外狀況，工具才能處理 HTTP 要求。 在本機執行函式時，系統不會強制執行授權層級。

2. 從 Azure Functions 執行階段輸出複製函式的 URL。

    ![Azure 本機執行階段](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. 將 HTTP 要求的 URL 貼到瀏覽器的網址列。 將查詢字串 `?name=<YOUR_NAME>` 附加至此 URL 並執行要求。 下圖顯示瀏覽器中對於函式傳回之本機 GET 要求所做出的回應： 

    ![瀏覽器中的函式 localhost 回應](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. 在 Visual Studio 中，按 Shift+F5 可停止偵錯。