---
title: 如何使用 Node.js Azure 儲存體的 Azure 佇列儲存體
description: 瞭解如何使用 Azure 佇列儲存體來建立和刪除佇列。 瞭解如何使用 Node.js 來插入、取得和刪除訊息。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 12ae05e10cdf0fa9a5f0725acaa1784eedc3612c
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803675"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>如何使用 Node.js 的 Azure 佇列儲存體

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>總覽

本指南說明如何使用 Azure 佇列儲存體來完成常見案例。 這些範例使用 Node.js API 撰寫。 涵蓋的案例包括插入、查看、取得和刪除佇列訊息。 此外，也將瞭解如何建立和刪除佇列。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

若要建立空白 Node.js 應用程式，請參閱 [在 Azure App Service 中建立 Node.js web 應用](../../app-service/quickstart-nodejs.md)程式、 [建立 Node.js 應用程式，並將其部署至](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) 使用 PowerShell 或 [Azure 雲端服務](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)Visual Studio Code。

## <a name="configure-your-application-to-access-storage"></a>設定您的應用程式以存取儲存體

[適用于 JavaScript 的 Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript)包含一組便利程式庫，可與儲存體 REST 服務通訊。

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>使用 Node package manager (npm) 取得封裝

1. 使用命令列介面，例如 PowerShell (Windows) 、Terminal (Mac) 或 Bash (Unix) ，流覽至您在其中建立範例應用程式的資料夾。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. `npm install @azure/storage-queue`在命令視窗中輸入。

1. 確認 `node_modules` 已建立資料夾。 在該資料夾內，您會找到 `@azure/storage-queue` 包含存取儲存體所需的用戶端程式庫的套件。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. `npm install azure-storage`在命令視窗中輸入。

1. 確認 `node_modules` 已建立資料夾。 在該資料夾內，您會找到 `azure-storage` 封裝，其中包含存取儲存體所需的程式庫。

---

### <a name="import-the-package"></a>匯入封裝

使用您的程式碼編輯器，將下列程式碼新增至您想要使用佇列的 JavaScript 檔案頂端。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>如何建立佇列

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

下列程式碼會取得名為的環境變數值 `AZURE_STORAGE_CONNECTION_STRING` ，並使用它來建立 [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient) 物件。 然後，這個物件會用來建立可 [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) 讓您使用特定佇列的物件。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

如果佇列已經存在，則會擲回例外狀況。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Azure 模組會讀取環境變數 `AZURE_STORAGE_ACCOUNT` `AZURE_STORAGE_ACCESS_KEY` ，或會 `AZURE_STORAGE_CONNECTION_STRING` 取得連接到 Azure 儲存體帳戶所需的資訊。 如果未設定這些環境變數，您必須在呼叫時指定帳戶資訊 `createQueueService` 。

下列程式碼會建立 `QueueService` 物件，讓您能夠使用佇列。

```javascript
var queueSvc = azure.createQueueService();
```

呼叫 `createQueueIfNotExists` 方法，以指定的名稱建立新的佇列，或傳回佇列（如果已經存在的話）。

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

如果建立佇列，則 `result.created` 為 true。 如果佇列已存在，則 `result.created` 為 false。

---

## <a name="how-to-insert-a-message-into-a-queue"></a>如何將訊息插入佇列中

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

若要將訊息新增至佇列，請呼叫 [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) 方法。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

若要將訊息插入佇列中，請呼叫 `createMessage` 方法來建立新訊息，並將它新增至佇列。

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>如何查看下一個訊息

您可以藉由呼叫方法來查看佇列中的訊息，而不需要將它們從佇列中移除 `peekMessages` 。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

依預設，會 [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) 查看單一訊息。 下列範例會查看佇列中的前五個訊息。 如果看到的訊息少於五個，則只會傳回可見的訊息。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

依預設，會 `peekMessages` 查看單一訊息。

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` 包含訊息。

---

`peekMessages`當佇列中沒有任何訊息時呼叫，不會傳回錯誤。 但是，不會傳回任何訊息。

## <a name="how-to-change-the-contents-of-a-queued-message"></a>如何變更佇列訊息的內容

下列範例會更新訊息的文字。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

藉由呼叫，在佇列中就地變更訊息的內容 [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-) 。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

藉由呼叫，在佇列中就地變更訊息的內容 `updateMessage` 。

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>如何將訊息清除佇列

清除佇列訊息是兩階段的進程：

1. 取得訊息。

1. 刪除訊息。

下列範例會取得訊息，然後將它刪除。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

若要取得訊息，請呼叫 [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) 方法。 此呼叫會讓訊息不會在佇列中隱藏，因此沒有其他用戶端可以處理這些訊息。 當您的應用程式處理訊息之後，請呼叫 [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) 將它從佇列中刪除。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

根據預設，訊息只會隱藏30秒。 30秒之後，其他用戶端就會看到它。 當您呼叫時，可以藉由設定來指定不同的值 [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) `receiveMessages` 。

`receiveMessages`當佇列中沒有任何訊息時呼叫，不會傳回錯誤。 但是，不會傳回任何訊息。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

若要取得訊息，請呼叫 `getMessages` 方法。 此呼叫會讓訊息不會在佇列中隱藏，因此沒有其他用戶端可以處理這些訊息。 當您的應用程式處理訊息之後，請呼叫 `deleteMessage` 將它從佇列中刪除。

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

根據預設，訊息只會隱藏30秒。 30秒之後，其他用戶端就會看到它。 您可以使用 with 來指定不同的 `options.visibilityTimeout` 值 `getMessages` 。

`getMessages`當佇列中沒有任何訊息時，使用時不會傳回錯誤。 但是，不會傳回任何訊息。

---

## <a name="additional-options-for-dequeuing-messages"></a>清除佇列訊息的其他選項

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

自訂從佇列中擷取訊息的方法有兩種：

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages)：取出訊息批次 (最多 32) 。
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout)：設定較長或較短的隱藏超時。

下列範例會使用 `receiveMessages` 方法，在一個呼叫中取得五個訊息。 接著其會使用 `for` 迴圈處理每個訊息。 另外，對此方法傳回的所有訊息，將隱藏逾時設為五分鐘。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

自訂從佇列中擷取訊息的方法有兩種：

- `options.numOfMessages`：取出訊息批次 (最多 32) 。
- `options.visibilityTimeout`：設定較長或較短的隱藏超時。

下列範例會使用 `getMessages` 方法，在一個呼叫中取得15個訊息。 接著其會使用 `for` 迴圈處理每個訊息。 另外，對此方法傳回的所有訊息，將隱藏逾時設為五分鐘。

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>如何取得佇列長度

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

方法會傳回 [`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) 佇列的相關中繼資料，包括佇列中等待的大約訊息數。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

方法會傳回 `getQueueMetadata` 佇列的相關中繼資料，包括佇列中等待的大約訊息數。

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>如何列出佇列

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

若要取出佇列清單，請呼叫 [`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) 。 若要取得依特定前置詞篩選的清單，請在呼叫中將 [選項](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) 設 `listQueues` 為前置詞。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

若要取出佇列清單，請使用 `listQueuesSegmented` 。 若要取得依特定前置詞篩選的清單，請使用 `listQueuesSegmentedWithPrefix` 。

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

如果無法傳回所有佇列，請傳遞 `result.continuationToken` 做為的第一個參數， `listQueuesSegmented` 或的第二個參數， `listQueuesSegmentedWithPrefix` 以取得更多結果。

---

## <a name="how-to-delete-a-queue"></a>如何刪除佇列

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

若要刪除佇列及其內含的所有訊息，請 [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) 在物件上呼叫方法 `QueueClient` 。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

若要清除佇列中的所有訊息，而不將它刪除，請呼叫 [`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) 。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

若要刪除佇列及其內含的所有訊息，請 `deleteQueue` 在佇列物件上呼叫方法。

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

若要清除佇列中的所有訊息，而不將它刪除，請呼叫 `clearMessages` 。

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>後續步驟

既然您已瞭解佇列儲存體的基本概念，請遵循下列連結以瞭解更複雜的儲存體工作。

- 造訪 [Azure 儲存體 team blog](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog) 以瞭解最新功能
- 造訪 GitHub 上的[適用于 JavaScript 的 Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript)
