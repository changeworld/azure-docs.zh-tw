---
title: 快速入門：Azure 佇列儲存體程式庫 v12 - .NET
description: 了解如何使用 Azure 佇列 .NET v12 程式庫來建立佇列，並將訊息新增至該佇列。 接下來，您會了解如何讀取和刪除佇列中的訊息。 您也將了解如何刪除佇列。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/22/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: c69aa91596ff203445aa4fa3ccd59001ffe16649
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197482"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>快速入門：適用於 .NET 的 Azure 佇列儲存體用戶端程式庫 v12

開始使用適用於 .NET 的 Azure 佇列儲存體用戶端程式庫 v12。 Azure 佇列儲存體是用來儲存大量訊息的服務，以便日後擷取和處理。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

> [!NOTE]
> 若要開始使用舊版 SDK，請參閱[快速入門：使用適用於 .NET 的 Azure 儲存體 SDK v11 來管理佇列](storage-quickstart-queues-dotnet-legacy.md)。

使用適用於 .NET 的 Azure 佇列儲存體用戶端程式庫 v12：

* 建立佇列
* 將訊息新增至佇列
* 窺視佇列中的訊息
* 更新佇列中的訊息
* 從佇列接收訊息
* 刪除佇列中的訊息
* 刪除佇列

[API 參考文件](/dotnet/api/azure.storage.queues) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues) | [套件 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0) | [範例](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* Azure 儲存體帳戶 - [建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 適用於您作業系統的 [NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) \(英文\) 目前版本。 請務必取得 SDK，而不是執行階段。

## <a name="setting-up"></a>設定

本節會引導您準備專案以搭配適用於 .NET 的 Azure 佇列儲存體用戶端程式庫 v12 使用。

### <a name="create-the-project"></a>建立專案

建立一個名為 QueuesQuickstartV12  的 .NET Core 應用程式。

1. 在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 QueuesQuickstartV12  的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：*Program.cs*。

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. 切換至新建立的 QueuesQuickstartV12  目錄。

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>安裝套件

若您仍在應用程式目錄中，請使用 `dotnet add package` 命令安裝適用於 .NET 套件的 Azure 佇列儲存體用戶端程式庫。

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>設定應用程式架構

從專案目錄：

1. 在編輯器中開啟 Program.cs  檔案
1. 移除 `Console.WriteLine("Hello World!");` 陳述式
1. 新增 `using` 指示詞
1. 更新 `Main` 方法宣告以[支援非同步程式碼](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)



此程式碼如下：

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>物件模型

Azure 佇列儲存體是用來儲存大量訊息的服務。 一則佇列訊息的大小可能高達 64 KB。 佇列可以包含數百萬則訊息，最高可達儲存體帳戶的總容量限制。 佇列通常用來建立要以非同步方式處理的待處理項目 (backlog)。 佇列儲存體提供三種類型資源：

* 儲存體帳戶
* 儲存體帳戶中的佇列
* 佇列中的訊息

下圖顯示資源之間的關係。

![佇列儲存體架構圖](./media/storage-queues-introduction/queue1.png)

使用下列 .NET 類別與這些資源互動：

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient)：`QueueServiceClient` 可讓您管理儲存體帳戶中的所有佇列。
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient)：`QueueClient` 類別可讓您管理和操作個別佇列及其訊息。
* [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)：`QueueMessage` 類別代表在佇列上呼叫 [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) 時所傳回的個別物件。

## <a name="code-examples"></a>程式碼範例

這些範例程式碼片段會示範如何使用適用於 .NET 的 Azure 佇列儲存體用戶端程式庫執行下列動作：

* [取得連接字串](#get-the-connection-string)
* [建立佇列](#create-a-queue)
* [將訊息新增至佇列](#add-messages-to-a-queue)
* [窺視佇列中的訊息](#peek-at-messages-in-a-queue)
* [更新佇列中的訊息](#update-a-message-in-a-queue)
* [從佇列接收訊息](#receive-messages-from-a-queue)
* [刪除佇列中的訊息](#delete-messages-from-a-queue)
* [刪除佇列](#delete-a-queue)

### <a name="get-the-connection-string"></a>取得連接字串

以下程式碼會擷取儲存體帳戶的連接字串。 連接字串會儲存在[設定儲存體連接字串](#configure-your-storage-connection-string)一節中建立的環境變數。

在 `Main` 方法內新增此程式碼：

```csharp
Console.WriteLine("Azure Queue storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>建立佇列

決定新佇列的名稱。 下列程式碼會將 GUID 值附加到佇列名稱，以確保它是唯一的。

> [!IMPORTANT]
> 佇列名稱只能包含小寫字母、數字和連字號，且必須以字母或數字開頭。 每個連字號前後都必須緊接非連字號的字元。 名稱長度也必須為 3 到 63 個字元。 如需為佇列命名的詳細資訊，請參閱[為佇列和中繼資料命名](/rest/api/storageservices/naming-queues-and-metadata)。


建立 [QueueClient](/dotnet/api/azure.storage.queues.queueclient) 類別的執行個體。 然後，呼叫 [CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync) 方法，以在您的儲存體帳戶中建立佇列。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>將訊息新增至佇列

下列程式碼片段會藉由呼叫 [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) 方法，以非同步方式將訊息新增至佇列。 其也會儲存從 `SendMessageAsync` 呼叫傳回的 [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt)。 回條可用來在稍後的程式中更新訊息。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>窺視佇列中的訊息

藉由呼叫 [PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) 方法來窺視佇列中的訊息。 `PeekMessagesAsync` 方法會從佇列前面擷取一或多則訊息，但不會更改訊息的可見性。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>更新佇列中的訊息

藉由呼叫 [UpdateMessageAsync](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) 方法來更新訊息的內容。 `UpdateMessageAsync` 方法可以變更訊息的可見度逾時和內容。 訊息內容必須是大小上限為 64 KB 的 UTF-8 編碼字串。 連同訊息的新內容，傳入先前程式碼所儲存 `SendReceipt` 中的值。 `SendReceipt` 值會識別要更新的訊息。

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>從佇列接收訊息

藉由呼叫 [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) 方法來下載先前新增的訊息。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>刪除佇列中的訊息

訊息經過處理後，請將其從佇列中刪除。 在此情況下，處理只會在主控台上顯示訊息。

應用程式會在處理和刪除訊息之前呼叫 `Console.ReadLine`，藉以暫停使用者輸入。 在 [Azure 入口網站](https://portal.azure.com)中確認已正確建立資源，然後才予以刪除。 任何未明確刪除的訊息最後都會再次顯示在佇列中，以提供另一次進行處理的機會。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>刪除佇列

下列程式碼會使用 [DeleteAsync](/dotnet/api/azure.storage.queues.queueclient.deleteasync) 方法刪除佇列，以清除應用程式所建立的資源。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>執行程式碼

此應用程式會建立三則訊息，並將其新增至 Azure 佇列。 程式碼會列出佇列中的訊息，然後在最後刪除佇列之前，先擷取並刪除訊息。

在您的主控台視窗中，瀏覽至您的應用程式目錄，並建置和執行應用程式。

```console
dotnet build
```

```console
dotnet run
```

應用程式的輸出類似下列範例：

```output
Azure Queue storage v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

當應用程式在接收訊息之前暫停，請在 [Azure 入口網站](https://portal.azure.com)中檢查您的儲存體帳戶。 確認訊息在佇列中。

按下 **Enter** 鍵來接收和刪除訊息。 出現提示時，請再次按下 **Enter** 鍵，以刪除佇列並完成示範。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用非同步的 .NET 程式碼建立佇列，並將訊息新增至其中。 然後您會了解如何窺視、擷取和刪除訊息。 最後，您會了解如何刪除訊息佇列。

如需教學課程、範例、快速入門及其他文件，請瀏覽：

> [!div class="nextstepaction"]
> [適用於 .NET 和 .NET Core 開發人員的 Azure](https://docs.microsoft.com/dotnet/azure/)

* 若要深入了解，請參閱[適用於 .NET 的 Azure 儲存體程式庫](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage)。
* 若要查看更多 Azure 佇列儲存體範例應用程式，請繼續 [Azure 佇列儲存體 v12 .NET 用戶端程式庫範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)。
* 若要深入了解 .NET Core，請參閱[在 10 分鐘內開始使用 .NET](https://www.microsoft.com/net/learn/get-started/)。
