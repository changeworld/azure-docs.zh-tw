---
title: 開始使用 Azure 服務匯流排佇列 (Azure.Messaging.ServiceBus)
description: 在本教學課程中，您會建立 .NET Core C# 應用程式以對服務匯流排佇列傳送和接收訊息。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec3f53e6f69614028c013efa5f0e6852cbc3f8ae
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631635"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>在 Azure 服務匯流排佇列 (.NET) 中傳送和接收訊息
在本教學課程中，您會建立 .NET Core 應用程式並使用 **Azure.Messaging.ServiceBus** 套件對服務匯流排佇列傳送和接收訊息。 

> [!Important]
> 本快速入門使用新的 Azure.Messaging.ServiceBus 套件。 如需使用舊 Microsoft.Azure.ServiceBus 套件的快速入門，請參閱[使用 Microsoft.Azure.ServiceBus 套件傳送和接收事件](service-bus-dotnet-get-started-with-queues-legacy.md)。

## <a name="prerequisites"></a>必要條件

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或是[註冊免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果您沒有可用的佇列，請執行[使用 Azure 入口網站建立服務匯流排佇列](service-bus-quickstart-portal.md)一文中的步驟，以建立佇列。 請記下服務匯流排命名空間的 **連接字串**，以及您所建立的 **佇列** 名稱。

## <a name="send-messages-to-a-queue"></a>傳送訊息至佇列
在本快速入門中，您將建立 C# .NET Core 主控台應用程式，以將訊息傳送至佇列。

### <a name="create-a-console-application"></a>建立主控台應用程式
啟動 Visual Studio 並建立適用於 C# 的新 **主控台應用程式 (.NET Core)** 專案。 

### <a name="add-the-service-bus-nuget-package"></a>新增服務匯流排 NuGet 封裝

1. 以滑鼠右鍵按一下新建立的專案，然後選取 [管理 NuGet 套件]。
1. 選取 [瀏覽]。  搜尋並選取 **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** 。
1. 選取[安裝] 以完成安裝，然後關閉 NuGet 套件管理員。

### <a name="add-code-to-send-messages-to-the-queue"></a>新增程式碼以將訊息傳送到佇列

1. 在 Program.cs 中，在命名空間定義的頂端新增下列 `using` 陳述式 (在類別宣告的前面)：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. 在 `Program` 類別中，宣告下列變數：

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    輸入命名空間的連接字串作為 `connectionString` 變數。 輸入您的佇列名稱。

1. 直接在 `Main()` 方法後面，新增下列 `SendMessagesAsync()` 方法，以執行傳送訊息的工作：

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. 新增名為 `CreateMessages` 的方法，以在 `Program` 類別中建立訊息佇列 (.NET 佇列)。 一般來說，您會從應用程式的不同部分取得這些訊息。 在這裡，我們會建立範例訊息的佇列。

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Second message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Third message in the batch"));
            return messages;
        }
    ```
1. 將名為 `SendMessageBatchAsync` 的方法新增至 `Program`，並新增下列程式碼。 這個方法會使用訊息佇列，並準備一或多個批次以傳送至服務匯流排佇列。 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }
        
                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);
        
                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. 以下列 **非同步** `Main`方法取代 `Main()` 方法。 此方法會呼叫 Send 方法，將單一訊息和一批訊息傳送至佇列。 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. 執行應用程式。 您應該會看見下列訊息。 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. 在 Azure 入口網站中遵循下列步驟：
    1. 瀏覽至您的服務匯流排命名空間。 
    1. 在 **概觀** 頁面上，選取底部靠中間窗格中的佇列。 
    1. 請注意 **基本功能** 區段中的值。

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="收到包括計數和大小的訊息" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    請注意下列值：
    - 佇列的 **使用中訊息計數** 值現在是 **4**。 每次執行傳送者應用程式而未擷取訊息時，這個值就會增加 4。
    - 每當應用程式將訊息新增到佇列時，佇列的目前大小就會讓 [基本資訊] 中的 **CURRENT** 值增加。
    - 在底部 **計量** 區段的 **訊息** 圖表中，您會看到佇列有四個傳入訊息。 

## <a name="receive-messages-from-a-queue"></a>從佇列接收訊息
在本節中，您將新增程式碼以從佇列中取出訊息。

1. 將下列方法新增至處理訊息和所有錯誤的 `Program` 類別。 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. 將名為 `ReceiveMessagesAsync` 的方法新增至 `Program`，並新增下列程式碼以接收訊息。 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. 新增從 `Main` 方法對 `ReceiveMessagesAsync` 方法的呼叫。 如果只想測試是否能接收訊息，請註解 `SendMessagesAsync` 方法。 如果沒有這麼做，則會看到傳送至佇列的另外四則訊息。 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>執行應用程式
執行應用程式。 等候一分鐘，然後按任意鍵以停止接收訊息。 您應該會看到下列輸出 (按空白鍵)。 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

再次檢查入口網站。 

- **使用中訊息計數** 和 **CURRENT** 值現在是 **0**。
- 在底部 **計量** 區段的 **訊息** 圖表中，您會看到佇列有八個傳入訊息和八個傳出訊息。 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="接收後的活動訊息和大小" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>後續步驟
請參閱下列文件和範例：

- [適用於 .NET 的 Azure 服務匯流排用戶端程式庫 - 讀我檔案](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub 範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 參考](/dotnet/api/azure.messaging.servicebus?preserve-view=true)