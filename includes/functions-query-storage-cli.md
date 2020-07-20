---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474158"
---
### <a name="query-the-storage-queue"></a>查詢儲存體佇列

您可以使用 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 命令檢視您帳戶中的儲存體佇列，如下列範例所示：

```azurecli-interactive
az storage queue list --output tsv
```

此命令的輸出會包含名為 `outqueue` 的佇列，這是函式執行時所建立的佇列。

接著，請使用 [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) 命令檢視此佇列中的訊息，如下列範例所示：

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

傳回的字串應該會與您傳送用來測試函式的訊息相同。

> [!NOTE]  
> 上述範例解碼了 base64 傳回的字串。 這是因為佇列儲存體繫結會以 [base64 字串](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)的形式寫入和讀取自 Azure 儲存體。