---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673310"
---
## <a name="update-the-tests"></a>更新測試

因為原型也會建立一組測試，所以您需要更新這些測試，以處理 `run` 方法簽章中的新 `msg` 參數。  

瀏覽至 _src/test/java_  底下的測試程式碼位置，開啟 *Function.java* 專案檔案，並以下列程式碼取代 `//Invoke` 下的程式碼行。

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::