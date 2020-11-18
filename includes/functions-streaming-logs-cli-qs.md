---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424637"
---
執行下列命令，以檢視近乎即時的[串流記錄](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)：

```console
func azure functionapp logstream <APP_NAME> 
```

在個別終端機視窗或瀏覽器中，再次呼叫遠端函式。 Azure 中的函式執行會有詳細資訊記錄顯示在終端機中。 