---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673460"
---
## <a name="update-the-tests"></a>更新測試

因為原型也會建立一組測試，所以您需要更新這些測試，以處理 `run` 方法簽章中的新 `msg` 參數。  

瀏覽至 _src/test/java_  底下的測試程式碼位置，開啟 *Function.java* 專案檔案，並以下列程式碼取代 `//Invoke` 下的程式碼行。

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
