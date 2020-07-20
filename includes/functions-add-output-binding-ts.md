---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/11/2020
ms.author: glenga
ms.openlocfilehash: dab5f0f24fa1f36b611eb79329336832d8a4b3cb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78191004"
---
加入會使用 `context.bindings` 上的 `msg` 輸出繫結物件的程式碼來建立佇列訊息。 在 `context.res` 陳述式前面新增此程式碼。

:::code language="typescript" range="10" source="~/functions-docs-typescript/functions-add-output-binding-storage-queue-cli/HttpExample/index.ts":::

此時，您的函式看起來應如下所示：

:::code language="typescript" source="~/functions-docs-typescript/functions-add-output-binding-storage-queue-cli/HttpExample/index.ts":::