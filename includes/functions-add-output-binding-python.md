---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "78191002"
---
將 `msg` 參數新增至函式定義和 `msg.set(name)` 陳述式下的 `if name:`，以更新 *HttpExample\\\_\_init\_\_.py*，使其符合下列程式碼。

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

`msg` 參數是 [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) 的執行個體。 其 `set` 方法會將字串訊息寫入至佇列，在此案例中為傳至 URL 查詢字串中所含函式的名稱。
