---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279595"
---
## <a name="test"></a>驗證 Azure 中的函式

您可以使用網頁瀏覽器來驗證已部署的函式。  請將 URL (包括函式索引鍵) 複製到網頁瀏覽器的位址列中。 請先將查詢字串 `&name=<yourname>` 附加至 URL，然後再執行要求。

![使用網頁瀏覽器來呼叫函式。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

或者，您可以使用 cURL 來驗證已部署的函式。 使用您從上一個步驟複製的 URL (包括函式索引鍵)，將查詢字串 `&name=<yourname>` 附加至 URL。

![使用 cURL 來呼叫 Azure 中的函式。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

