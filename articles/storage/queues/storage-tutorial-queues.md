---
title: 教學課程 - 使用 Azure 儲存體佇列工作 - Azure 儲存體
description: 本教學課程說明如何使用 Azure 佇列服務來建立佇列，以及插入、取得和刪除訊息。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75968204"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>教學課程：使用 Azure 儲存體佇列

Azure 佇列儲存體可實作雲端式佇列，使分散式應用程式的元件之間可進行通訊。 每個佇列都會維護一份可由傳送端元件新增、且可由接收端元件處理的訊息清單。 透過佇列，您的應用程式將可立即進行調整以因應需求。 本文說明使用 Azure 儲存體佇列的基本步驟。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> - 建立 Azure 儲存體帳戶
> - 建立應用程式
> - 新增非同步程式碼的支援
> - 建立佇列
> - 將訊息插入佇列中
> - 清除佇列中的訊息
> - 刪除空的佇列
> - 檢查命令列引數
> - 建置並執行應用程式

## <a name="prerequisites"></a>Prerequisites

- 取得免費的跨平台 [Visual Studio Code](https://code.visualstudio.com/download) 編輯器。
- 下載並安裝 [.NET Core SDK](https://dotnet.microsoft.com/download)。
- 如果您還沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

首先，請建立 Azure 儲存體帳戶。 如需建立儲存體帳戶的逐步指南，請參閱[建立儲存體帳戶](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json)快速入門。

## <a name="create-the-app"></a>建立應用程式

建立名為 **QueueApp** 的 .NET Core 應用程式。 為了方便說明，此應用程式將可透過佇列傳送和接收訊息。

1. 在主控台視窗中 (例如 CMD、PowerShell 或 Azure CLI)，使用 `dotnet new` 命令建立名為 **QueueApp** 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：**Program.cs**。

   ```console
   dotnet new console -n QueueApp
   ```

2. 切換至新建的 **QueueApp** 資料夾，然後建置應用程式以確認一切都正常運作。

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   您應該會看到如下的結果：

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>新增非同步程式碼的支援

由於應用程式使用雲端資源，因此程式碼會以非同步方式執行。 但在 C#7.1 之前，C# 的 **async** 和 **await** 在 **Main** 方法中並非有效的關鍵字。 您可以透過 **csproj** 檔案中的旗標輕鬆地切換至該編譯器。

1. 從專案目錄中的命令列輸入 `code .`，以在目前的目錄中開啟 Visual Studio Code。 將命令列視窗保持開啟。 稍後將會執行其他命令。 如果系統提示您新增建置和偵錯所需的 C# 資產，請按一下 [是]  按鈕。

2. 在編輯器中開啟 **QueueApp.csproj** 檔案。

3. 將 `<LangVersion>7.1</LangVersion>` 新增至組建檔案中的第一個 **PropertyGroup**。 請確定您僅新增了 **LangVersion** 標記，因為您的 **TargetFramework** 可能會隨著您安裝的 .NET 版本而有所不同。

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. 儲存 **QueueApp.csproj** 檔案。

5. 開啟 **Program.cs** 原始程式檔，並更新 **Main** 方法，以採用非同步方式執行。 將 **void** 取代為**非同步工作**傳回值。

   ```csharp
   static async Task Main(string[] args)
   ```

6. 儲存 **Program.cs** 檔案。

## <a name="create-a-queue"></a>建立佇列

1. 使用 `dotnet add package` 命令，將 **Microsoft.Azure.Storage.Common** 和 **Microsoft.Azure.Storage.Queue** 套件安裝到專案。 從主控台視窗中的專案資料夾執行下列 dotnet 命令。

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. 在 **Program.cs** 檔案頂端，緊接在 `using System;` 陳述式後面新增下列命名空間。 此應用程式會使用這些命名空間中的類型連線至 Azure 儲存體，並使用佇列。

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. 儲存 **Program.cs** 檔案。

### <a name="get-your-connection-string"></a>取得您的連接字串

用戶端程式庫會使用連接字串建立您的連線。 您可以在 Azure 入口網站中從您儲存體帳戶的 [設定]  區段取得連接字串。

1. 在網頁瀏覽器中，登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在 Azure 入口網站中巡覽至您的儲存體帳戶。

3. 選取 [存取金鑰]  。

4. 按一下 [連接字串]  欄位右側的 [複製]  按鈕。

![連接字串](media/storage-tutorial-queues/get-connection-string.png)

連接字串的格式如下：

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>將連接字串新增至應用程式

將連接字串新增至應用程式，使其能夠存取儲存體帳戶。

1. 切換回 Visual Studio Code。

2. 在 [程式]  類別中，新增用來存放連接字串的 `private const string connectionString =` 成員。

3. 在等號後面，貼上您先前在 Azure 入口網站中複製的字串值。 **ConnectionString** 值將是您的帳戶專用的值。

4. 從 **Main** 中移除 "Hello World" 程式碼。 您的程式碼應會顯示如下，但具有您唯一的連接字串值。

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. 更新 **Main** 以建立 **CloudQueue** 物件，此物件稍後會傳入傳送和接收方法中。

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. 儲存檔案。

## <a name="insert-messages-into-the-queue"></a>將訊息插入佇列中

建立將訊息傳送至佇列中的新方法。 將下列方法新增至您的**程式**類別。 此方法會取得佇列參考，然後藉由呼叫 [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) 建立新的佇列 (如果還不存在)。 接著，它會藉由呼叫 [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) 將訊息新增至佇列。

1. 將下列 **SendMessageAsync** 方法新增至您的**程式**類別。

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. 儲存檔案。

訊息的格式必須可以包含在採用 UTF-8 編碼的 XML 要求中，且大小最大可能為 64 KB。 如果訊息包含二進位資料，則建議您將訊息編碼為 Base64。

根據預設，訊息的存留時間上限會設為 7 天。 您可以指定任何正數的訊息存留時間。 若要新增不會過期的訊息，請在 **AddMessageAsync** 的呼叫中使用 `Timespan.FromSeconds(-1)`。

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>清除佇列中的訊息

建立名為 **ReceiveMessageAsync** 的新方法。 此方法會藉由呼叫 [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) 接收來自佇列的訊息。 在成功接收訊息後，務必要從佇列中刪除該訊息，以免受到多次處理。 收到訊息後，請呼叫 [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) 以從佇列中刪除該訊息。

1. 將下列 **ReceiveMessageAsync** 方法新增至您的**程式**類別。

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. 儲存檔案。

## <a name="delete-an-empty-queue"></a>刪除空的佇列

在專案結束後確認您是否還需要您建立的資源，是最佳做法。 讓資源繼續執行可能會產生費用。 如果佇列存在，但是空的，請詢問使用者是否要加以刪除。

1. 展開 **ReceiveMessageAsync** 方法，以納入刪除空佇列的提示。

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. 儲存檔案。

## <a name="check-for-command-line-arguments"></a>檢查命令列引數

如果在應用程式中傳入了任何命令列引數，請假設它們是要新增至佇列的訊息。 引數聯結在一起，組成字串。 藉由呼叫我們先前新增的 **SendMessageAsync** 方法，將此字串新增至訊息佇列。

如果沒有任何命令列引數，請執行擷取作業。 呼叫 **ReceiveMessageAsync** 方法以擷取佇列中的第一個訊息。

最後，等到使用者輸入後，再呼叫 **Console.ReadLine** 結束作業。

1. 展開 **Main** 方法以檢查命令列引數，並等候使用者輸入。

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. 儲存檔案。

## <a name="complete-code"></a>完整程式碼

以下列出此專案的完整程式碼。

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 從專案目錄中的命令列執行下列 dotnet 命令，以建置專案。

   ```console
   dotnet build
   ```

2. 專案成功建置後，請執行下列命令，將第一個訊息新增至佇列。

   ```console
   dotnet run First queue message
   ```

您應該會看見此輸出：

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. 執行未使用命令列引數的應用程式，以接收和移除佇列中的第一個訊息。

   ```console
   dotnet run
   ```

4. 繼續執行應用程式，直到所有訊息皆移除。 如果您再執行一次，應該會出現佇列空白的訊息，以及刪除佇列的提示。

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

1. 建立佇列
2. 從佇列新增和移除訊息
3. 刪除 Azure 儲存體佇列

如需詳細資訊，請參閱 Azure 佇列快速入門。

> [!div class="nextstepaction"]
> [佇列快速入門](storage-quickstart-queues-portal.md)
