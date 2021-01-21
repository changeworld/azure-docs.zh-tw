---
title: 使用 azure-messaging-servicebus 將訊息傳送至 Azure 服務匯流排主題
description: 本快速入門說明如何使用 azure-messaging-servicebus 套件，將訊息傳送至 Azure 服務匯流排主題。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 60504bcf9e2c3f9460eee9a2e72d18767c0cfa71
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631669"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>將訊息傳送到 Azure 服務匯流排主題，並從訂用帳戶接收來自主題的訊息 (.NET)
本教學課程說明如何建立 .NET Core 主控台應用程式，以將訊息傳送至服務匯流排主題，並從主題的訂用帳戶接收訊息。 

> [!Important]
> 本快速入門使用新的 **Azure.Messaging.ServiceBus** 套件。 如需使用舊 Microsoft.Azure.ServiceBus 套件的快速入門，請參閱[使用 Microsoft.Azure.ServiceBus 套件傳送和接收訊息](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md)。

## <a name="prerequisites"></a>必要條件

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以啟用自己的 [Visual Studio 或 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 依照下列快速入門中的步驟操作：[快速入門：使用 Azure 入口網站建立服務匯流排主題和主題的訂用帳戶](service-bus-quickstart-topics-subscriptions-portal.md)。 記下連接字串、主題名稱和訂用帳戶名稱。 在本快速入門中，您只會使用一個訂用帳戶。 

## <a name="send-messages-to-a-topic"></a>傳送訊息至主題
在本節中，您將在 Visual Studio 中建立 .NET Core 主控台應用程式、新增程式碼，以將訊息傳送至您所建立的主題。 

### <a name="create-a-console-application"></a>建立主控台應用程式
啟動 Visual Studio 並建立適用於 C# 的新 **主控台應用程式 (.NET Core)** 專案。 

### <a name="add-the-service-bus-nuget-package"></a>新增服務匯流排 NuGet 封裝

1. 以滑鼠右鍵按一下新建立的專案，然後選取 [管理 NuGet 套件]。
1. 選取 [瀏覽]。  搜尋並選取 **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** 。
1. 選取[安裝] 以完成安裝，然後關閉 NuGet 套件管理員。

### <a name="add-code-to-send-messages-to-the-topic"></a>新增程式碼以將訊息傳送到主題 

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
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    取代下列值：
    - 將 `<NAMESPACE CONNECTION STRING>` 取代為服務匯流排命名空間的連接字串
    - 將 `<TOPIC NAME>` 取代為主題名稱
    - 將 `<SUBSCRIPTION NAME>` 取代為訂用帳戶的名稱
2. 新增名為 `SendMessageToTopicAsync` 的方法，將一則訊息傳送至主題。 

    ```csharp
        static async Task SendMessageToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the topic
                ServiceBusSender sender = client.CreateSender(topicName);
                await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
                Console.WriteLine($"Sent a single message to the topic: {topicName}");
            }
        }
    ```
1. 新增名為 `CreateMessages` 的方法，以在 `Program` 類別中建立訊息佇列 (.NET 佇列)。 一般來說，您會從應用程式的不同部分取得這些訊息。 在這裡，我們會建立範例訊息的佇列。

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. 將名為 `SendMessageBatchAsync` 的方法新增至 `Program`，並新增下列程式碼。 這個方法會使用訊息佇列，並準備一或多個批次以傳送至服務匯流排主題。 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```
1. 以下列 **非同步** `Main`方法取代 `Main()` 方法。 此方法會呼叫 Send 方法，將單一訊息和一批訊息傳送至主題。  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. 執行應用程式。 您應該會看見下列輸出：

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. 在 Azure 入口網站中遵循下列步驟：
    1. 瀏覽至您的服務匯流排命名空間。 
    1. 在 **概觀** 頁面的底部窗格中，切換至 **主題** 索引標籤，然後選取服務匯流排主題。 在下列範例中是 `mytopic`。
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="選取主題":::
    1. 在 **服務匯流排主題** 頁面中，您可以在底部 **計量** 一節的 **訊息** 圖表中，看到主題有四個傳入訊息。 如果您沒有看到此值，請等候幾分鐘，然後重新整理頁面，以查看更新的圖表。 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="傳送至主的訊息" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. 選取下方窗格中的訂用帳戶。 在下列範例中為 **S1**。 在 **服務匯流排訂用帳戶** 頁面上，您會看到 **作用中訊息計數** 為 **4**。 訂用帳戶已收到您傳送至主題的四則訊息，但尚未有任何接收者接收此訊息。 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="在訂用帳戶收到的訊息" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>自訂用帳戶接收訊息

1. 將下列方法新增至處理訊息和所有錯誤的 `Program` 類別。 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. 將下列 `ReceiveMessagesFromSubscriptionAsync` 方法新增至 `Program` 類別。

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

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
1. 將 `ReceiveMessagesFromSubscriptionAsync` 方法的呼叫新增至 `Main` 方法。 如果只想測試是否能接收訊息，請註解 `SendMessagesToTopicAsync` 方法。 如果沒有這麼做，則會看到傳送至主題的另外四則訊息。 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>執行應用程式
執行應用程式。 等候一分鐘，然後按任意鍵以停止接收訊息。 您應該會看到下列輸出 (按空白鍵)。 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

再次檢查入口網站。 

- 在 **服務匯流排主題** 頁面的 **訊息** 圖表中，您會看到八個傳入訊息和八個傳出訊息。 如果您沒有看到這些訊息，請等候幾分鐘，然後重新整理頁面，以查看更新的圖表。 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="已傳送及接收的訊息" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- 在 **服務匯流排訂用帳戶** 頁面上，您會看到 **作用中訊息計數** 為零。 這是因為接收者已接收來自此訂用帳戶的訊息，並已完成訊息。 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="在訂用帳戶結尾的作用中訊息計數" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>後續步驟
請參閱下列文件和範例：

- [適用於 .NET 的 Azure 服務匯流排用戶端程式庫 - 讀我檔案](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub 範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 參考](/dotnet/api/azure.messaging.servicebus?preserve-view=true)