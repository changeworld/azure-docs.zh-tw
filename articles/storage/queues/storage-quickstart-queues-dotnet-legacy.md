---
title: 快速入門：使用適用於 .NET 的 Azure 儲存體 v11 來管理佇列
description: 在此快速入門中，您將了解如何使用適用於 .NET 的 Azure 儲存體用戶端程式庫，建立佇列及在其中新增訊息。 接下來，您會了解如何讀取和處理佇列中的訊息。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: d73f225f0e6f230509c856af0d15bc02e80fcd98
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425888"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>快速入門：使用適用於 .NET 的 Azure 儲存體 SDK v11 來管理佇列

在此快速入門中，您將了解如何使用適用於 .NET 的 Azure 儲存體用戶端程式庫第 11 版，來建立佇列及在其中新增訊息。 接下來，您會了解如何讀取和處理佇列中的訊息。 

> [!NOTE]
> 本快速入門會使用舊版的 Azure 佇列儲存體用戶端程式庫。 若要開始使用最新版本，請參閱[快速入門：適用於 .NET 的 Azure 佇列儲存體用戶端程式庫 v12](storage-quickstart-queues-dotnet.md)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

接下來，請下載並安裝您的作業系統適用的 .NET Core 2.0。 如果執行 Windows，您可以安裝 Visual Studio 並使用 .NET Framework (視您的喜好而定)。 您也可以選擇安裝編輯器來搭配您的作業系統使用。

### <a name="windows"></a>Windows

- 安裝 [.NET Core for Windows](https://www.microsoft.com/net/download/windows) 或 [.NET Framework](https://www.microsoft.com/net/download/windows) (隨附於 Visual Studio for Windows)
- 安裝 [Visual Studio for Windows](https://www.visualstudio.com/)。 如果您使用 .NET Core，可選擇性安裝 Visual Studio。  

如需選擇 .NET Core 或 .NET Framework 的相關資訊，請參閱[針對伺服器應用程式選擇 .NET Core 或 .NET Framework](/dotnet/standard/choosing-core-framework-server)。

### <a name="linux"></a>Linux

- 安裝 [.NET Core for Linux](https://www.microsoft.com/net/download/linux)
- 選擇性安裝 [Visual Studio Code](https://www.visualstudio.com/) 和 [C# 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

### <a name="macos"></a>macOS

- 安裝 [.NET Core for macOS](https://www.microsoft.com/net/download/macos).
- 選擇性安裝 [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="download-the-sample-application"></a>下載範例應用程式

本快速入門使用的範例應用程式是基本的主控台應用程式。 您可探索 [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart) 上的範例應用程式。

使用 [git](https://git-scm.com/) 將應用程式的複本下載至您的開發環境。 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

此命令會將存放庫複製到本機的 git 資料夾。 若要開啟 Visual Studio 解決方案，請尋找 *storage-queues-dotnet-quickstart* 資料夾並加以開啟，然後按兩下 *storage-queues-dotnet-quickstart.sln* 。 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

若要執行應用程式，您必須提供儲存體帳戶的連接字串。 此範例應用程式會讀取環境變數中的連接字串，並使用它來授權對 Azure 儲存體的要求。

在複製您的連接字串後，請在執行應用程式的本機電腦上，將該字串寫入至新的環境變數中。 若要設定環境變數，請開啟主控台視窗，並遵循您的作業系統所適用的指示。 將 `<yourconnectionstring>` 用實際的連接字串取代：

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

新增環境變數之後，您可能需要重新啟動任何需要讀取環境變數的執行中程式，包括主控台視窗。 例如，如果您使用 Visual Studio 做為編輯器，請在執行範例前重新啟動 Visual Studio。 

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

新增環境變數之後，從主控台視窗執行 `source ~/.bashrc`，讓變更生效。

### <a name="macos"></a>macOS

編輯 .bash_profile，然後新增環境變數：

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

新增環境變數之後，從主控台視窗執行 `source .bash_profile`，讓變更生效。

## <a name="run-the-sample"></a>執行範例

範例應用程式會建立佇列並在其中新增訊息。 此應用程式會先查看訊息，而不需從佇列中移除該訊息，然後擷取訊息並從佇列中加以刪除。

### <a name="windows"></a>Windows

如果您使用 Visual Studio 作為編輯器，請按 **F5** 來執行。 

否則，瀏覽至您的應用程式目錄，並使用 `dotnet run` 命令執行應用程式。

```
dotnet run
```

### <a name="linux"></a>Linux

瀏覽至您的應用程式目錄，並使用 `dotnet run` 命令執行應用程式。

```
dotnet run
```

### <a name="macos"></a>macOS

瀏覽至您的應用程式目錄，並使用 `dotnet run` 命令執行應用程式。

```
dotnet run
```

範例應用程式的輸出類似下列範例：

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>了解範例程式碼

接下來，探索範例程式碼來了解其運作方式。

### <a name="try-parsing-the-connection-string"></a>嘗試剖析連接字串

此範例會先檢查環境變數是否包含可剖析的連接字串，以建立指向儲存體帳戶的 [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) 物件。 為了檢查連接字串是否有效，此範例會使用 [TryParse](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) 方法。 如果 **TryParse** 成功，它會初始化 storageAccount 變數並傳回 **true** 。

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...    
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>建立佇列

首先，此範例會建立佇列並在其中新增訊息。 

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name 
// is unique in your storage account. 
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>新增訊息

接下來，此範例會將訊息新增至佇列後端。 

訊息的格式必須可以包含在採用 UTF-8 編碼的 XML 要求中，且大小最大可能為 64 KB。 如果訊息包含二進位資料，則建議您將訊息編碼為 Base64。

根據預設，訊息的存留時間上限會設為 7 天。 您可以指定任何正數的訊息存留時間。

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

若要新增不會過期的訊息，請在 [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) 的呼叫中使用 `Timespan.FromSeconds(-1)`。

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>查看佇列中的訊息

此範例示範如何查看佇列中的訊息。 當您查看訊息時，您可以讀取訊息的內容。 不過，其他用戶端仍可看見此訊息，以便其他用戶端接著擷取和處理該訊息。

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>清除佇列中的訊息

此範例也示範如何從佇列中清除訊息。 當您清除佇列中的訊息時，您可從佇列前端擷取訊息，並且讓其他用戶端暫時看不見該訊息。 根據預設，訊息會維持 30 秒的不可見狀態。 在此期間，您的程式碼可以處理此訊息。 若要完成清除佇列中的該訊息，您可在處理後立即刪除該訊息，其他用戶端才不會從佇列中清除相同的訊息。

如果您的程式碼因為硬體或軟體失敗而無法處理訊息，則訊息會在不可見期間後再度看得見。 另一個用戶端可以擷取相同的訊息並再試一次。

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for 
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>清除資源

此範例會藉由刪除佇列，清除它所建立的資源。 刪除佇列時，也會刪除其中包含的任何訊息。

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>可供使用佇列開發 .NET 應用程式的資源

請參閱以下可供使用 Azure 佇列進行 .NET 開發的額外資源：

### <a name="binaries-and-source-code"></a>二進位檔和原始程式碼

- 針對 Azure 儲存體最新版的 [.NET 用戶端程式庫](/dotnet/api/overview/azure/storage)下載 NuGet 套件
    - [通用](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
    - [佇列](https://www.nuget.org/packages/Azure.Storage.Queues/)
- 在 GitHub 上檢視 [.NET 用戶端程式庫原始程式碼](https://github.com/Azure/azure-storage-net)。

### <a name="client-library-reference-and-samples"></a>用戶端程式庫參考和範例

- 如需 .NET 用戶端程式庫的詳細資訊，請參閱 [.NET API 參考](/dotnet/api/overview/azure/storage)。
- 探索使用 .NET 用戶端程式庫所撰寫的[佇列儲存體範例](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues)。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將訊息新增至佇列，查看佇列中的訊息，以及使用 .NET 清除佇列和處理訊息。 

> [!div class="nextstepaction"]
> [在應用程式與 Azure 佇列儲存體之間進行通訊](/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- 若要深入了解 .NET Core，請參閱[在 10 分鐘內開始使用 .NET](https://www.microsoft.com/net/learn/get-started/)。
