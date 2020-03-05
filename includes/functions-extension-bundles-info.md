---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201019"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> 在啟動期間，主機會下載並安裝[儲存體繫結延伸模組](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher)和其他 Microsoft 繫結延伸模組。 之所以會執行此安裝，是因為具有下列屬性的 *host.json* 檔案依預設會啟用繫結延伸模組：
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> 如果發生任何與繫結延伸模組相關的錯誤，請檢查 *host.json* 中是否有上述屬性存在。
::: zone-end  