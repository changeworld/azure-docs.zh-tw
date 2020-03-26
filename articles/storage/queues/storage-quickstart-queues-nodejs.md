---
title: 快速入門：Azure 佇列儲存體程式庫 v12 - JavaScript
description: 了解如何使用 Azure 佇列 JavaScript v12 程式庫來建立佇列，並將訊息新增至該佇列。 接下來，您會了解如何讀取和刪除佇列中的訊息。 您也將了解如何刪除佇列。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 59a5308d2c0a1fa2e1f38f2fe3da3a2cc29448be
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199779"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>快速入門：適用於 JavaScript 的 Azure 佇列儲存體用戶端程式庫 v12

開始使用適用於 JavaScript 的 Azure 佇列儲存體用戶端程式庫 v12。 Azure 佇列儲存體是用來儲存大量訊息的服務，以便日後擷取和處理。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

使用適用於 JavaScript 的 Azure 佇列儲存體用戶端程式庫 v12：

* 建立佇列
* 將訊息新增至佇列
* 窺視佇列中的訊息
* 更新佇列中的訊息
* 從佇列接收訊息
* 刪除佇列中的訊息
* 刪除佇列

[API 參考文件](https://docs.microsoft.com/javascript/api/@azure/storage-queue/) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue) | [套件 (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-queue) | [範例](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* Azure 儲存體帳戶 - [建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 適用於您作業系統的 [Node.js](https://nodejs.org/en/download/) 目前版本。

## <a name="setting-up"></a>設定

本節會引導您準備專案以搭配適用於 JavaScript 的 Azure 佇列儲存體用戶端程式庫 v12 使用。

### <a name="create-the-project"></a>建立專案

建立名為 *queues-quickstart-v12* 的 Node.js 應用程式。

1. 在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為專案建立一個新目錄。

    ```console
    mkdir queues-quickstart-v12
    ```

1. 切換至新建立的 *queues-quickstart-v12* 目錄。

    ```console
    cd queues-quickstart-v12
    ```

1. 建立一個名為 *package.json* 的新文字檔。 此檔案會定義 Node.js 專案。 將此檔案儲存在 *queues-quickstart-v12* 目錄中。 以下是此檔案的內容：

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    如有需要，您可以將自己的姓名放在 `author` 欄位中。

### <a name="install-the-package"></a>安裝套件

若您仍在 *queues-quickstart-v12* 目錄中，請使用 `npm install` 命令安裝適用於 JavaScript 套件的 Azure 佇列儲存體用戶端程式庫。

```console
npm install
```

 此命令會讀取 *package.json* 檔案，並安裝適用於 JavaScript 套件的 Azure 佇列儲存體用戶端程式庫 v12，以及它所相依的所有程式庫。

### <a name="set-up-the-app-framework"></a>設定應用程式架構

從專案目錄：

1. 在程式碼編輯器中開啟另一個新的文字檔
1. 加入 `require` 呼叫以載入 Azure 和 Node.js 模組
1. 建立程式的結構，包括非常基本的例外狀況處理

    此程式碼如下：

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. 將新檔案以 *queues-quickstart-v12.js* 儲存在 *queues-quickstart-v12* 目錄中。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>物件模型

Azure 佇列儲存體是用來儲存大量訊息的服務。 一則佇列訊息的大小可能高達 64 KB。 佇列可以包含數百萬則訊息，最高可達儲存體帳戶的總容量限制。 佇列通常用來建立要以非同步方式處理的待處理項目 (backlog)。 佇列儲存體提供三種類型資源：

* 儲存體帳戶
* 儲存體帳戶中的佇列
* 佇列中的訊息

下圖顯示資源之間的關係。

![佇列儲存體架構圖](./media/storage-queues-introduction/queue1.png)

使用下列 JavaScript 類別與這些資源互動：

* [QueueServiceClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueserviceclient)：`QueueServiceClient` 可讓您管理儲存體帳戶中的所有佇列。
* [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient)：`QueueClient` 類別可讓您管理和操作個別佇列及其訊息。
* [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage)：`QueueMessage` 類別代表在佇列上呼叫 [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient?view=azure-node-latest#receivemessages-queuereceivemessageoptions-) 時所傳回的個別物件。

## <a name="code-examples"></a>程式碼範例

這些範例程式碼片段會示範如何使用適用於 JavaScript 的 Azure 佇列儲存體用戶端程式庫執行下列動作：

* [取得連接字串](#get-the-connection-string)
* [建立佇列](#create-a-queue)
* [將訊息新增至佇列](#add-messages-to-a-queue)
* [窺視佇列中的訊息](#peek-at-messages-in-a-queue)
* [更新佇列中的訊息](#update-a-message-in-a-queue)
* [從佇列接收訊息](#receive-messages-from-a-queue)
* [刪除佇列中的訊息](#delete-messages-from-a-queue)
* [刪除佇列](#delete-a-queue)

### <a name="get-the-connection-string"></a>取得連接字串

下列程式碼會從[設定儲存體連接字串](#configure-your-storage-connection-string)一節中建立的環境變數，擷取儲存體帳戶的連接字串。

在 `main` 函式內新增此程式碼：

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be 
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>建立佇列

決定新佇列的名稱。 下列程式碼會將 UUID 值附加到佇列名稱，以確保它是唯一的。

> [!IMPORTANT]
> 佇列名稱只能包含小寫字母、數字和連字號，且必須以字母或數字開頭。 每個連字號前後都必須緊接非連字號的字元。 名稱長度也必須為 3 到 63 個字元。 如需為佇列命名的詳細資訊，請參閱[為佇列和中繼資料命名](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)。

建立 [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) 類別的執行個體。 然後，呼叫 [create](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) 方法，以在您的儲存體帳戶中建立佇列。

將此程式碼加入到 `main` 函式的結尾處：

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>將訊息新增至佇列

下列程式碼片段會藉由呼叫 [sendMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) 方法，將訊息新增至佇列。 其也會儲存第三次 `sendMessage` 呼叫所傳回的 [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage)。 傳回的 `sendMessageResponse` 用來在稍後的程式中更新訊息內容。

將此程式碼加入到 `main` 函式的結尾處：

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>窺視佇列中的訊息

藉由呼叫 [peekMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) 方法來窺視佇列中的訊息。 `peekMessages` 方法會從佇列前面擷取一或多則訊息，但不會更改訊息的可見性。

將此程式碼加入到 `main` 函式的結尾處：

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>更新佇列中的訊息

藉由呼叫 [updateMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) 方法來更新訊息的內容。 `updateMessage` 方法可以變更訊息的可見度逾時和內容。 訊息內容必須是大小上限為 64 KB 的 UTF-8 編碼字串。 連同新內容，傳入先前在程式碼中儲存的回應中的 `messageId`和 `popReceipt`。 `sendMessageResponse` 屬性會識別要更新的訊息。

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>從佇列接收訊息

藉由呼叫 [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) 方法來下載先前新增的訊息。  在 `numberOfMessages` 欄位中，傳入要針對此呼叫接收的訊息數目上限。

將此程式碼加入到 `main` 函式的結尾處：

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>刪除佇列中的訊息

在接收和處理訊息後，從佇列中刪除訊息。 在此情況下，處理只會在主控台上顯示訊息。

藉由呼叫 [deleteMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) 方法來刪除訊息。 任何未明確刪除的訊息最後都會再次顯示在佇列中，以提供另一次進行處理的機會。

將此程式碼加入到 `main` 函式的結尾處：

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>刪除佇列

下列程式碼會使用 [delete](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) 方法刪除佇列，以清除應用程式所建立的資源。

將此程式碼新增到 `main` 函式的結尾並儲存檔案：

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>執行程式碼

此應用程式會建立三則訊息，並將其新增至 Azure 佇列。 程式碼會列出佇列中的訊息，然後在最後刪除佇列之前，先擷取並刪除訊息。

在主控台視窗中，瀏覽至包含 *queues-quickstart-v12.js* 檔案的目錄，然後執行下列 `node` 命令來執行應用程式。

```console
node queues-quickstart-v12.js
```

應用程式的輸出類似下列範例：

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

使用偵錯工具逐步執行程式碼，並在整個過程中檢查您的 [Azure 入口網站](https://portal.azure.com)。 檢查您的儲存體帳戶，確認已建立並刪除佇列中的訊息。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 JavaScript 程式碼建立佇列，並將訊息新增至其中。 然後您會了解如何窺視、擷取和刪除訊息。 最後，您會了解如何刪除訊息佇列。

如需教學課程、範例、快速入門及其他文件，請瀏覽：

> [!div class="nextstepaction"]
> [Azure for JavaScript 文件](https://docs.microsoft.com/azure/javascript/)

* 若要深入了解，請參閱[適用於 JavaScript 的 Azure 儲存體佇列用戶端程式庫](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)。
* 若要查看更多 Azure 佇列儲存體範例應用程式，請繼續 [Azure 佇列儲存體用戶端程式庫 v12 JavaScript 範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples)。
