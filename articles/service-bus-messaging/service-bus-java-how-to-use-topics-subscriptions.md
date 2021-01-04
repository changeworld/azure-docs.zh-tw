---
title: 透過 Java 使用 Azure 服務匯流排主題和訂用帳戶 (azure-messaging-servicebus)
description: 在本快速入門中，您會使用 azure-messaging-servicebus 套件撰寫 Java 程式碼，以將訊息傳送至 Azure 服務匯流排主題，然後從該主題的訂用帳戶接收訊息。
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: cd95b84c4b55279b40f95eef65cb2490a55d1780
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630236"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>將訊息傳送到 Azure 服務匯流排主題，並從訂用帳戶接收來自主題的訊息 (Java)
在本快速入門中，您會使用 azure-messaging-servicebus 套件撰寫 Java 程式碼，以將訊息傳送至 Azure 服務匯流排主題，然後從該主題的訂用帳戶接收訊息。

> [!IMPORTANT]
> 本快速入門使用新的 azure-messaging-servicebus 套件。 如需使用舊版 azure-servicebus 套件的快速入門，請參閱[使用 azure-servicebus 傳送和接收訊息](service-bus-java-how-to-use-topics-subscriptions-legacy.md)。

## <a name="prerequisites"></a>先決條件

- Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以啟用自己的 [Visual Studio 或 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 依照下列快速入門中的步驟操作：[快速入門：使用 Azure 入口網站建立服務匯流排主題和主題的訂用帳戶](service-bus-quickstart-topics-subscriptions-portal.md)。 記下連接字串、主題名稱和訂用帳戶名稱。 在本快速入門中，您只會使用一個訂用帳戶。 
- 安裝 [Azure SDK for Java][Azure SDK for Java]。 如果您使用的是 Eclipse，則可以安裝包含 Azure SDK for Java 的[適用於 Eclipse 的 Azure 工具組][Azure Toolkit for Eclipse]。 然後您可以將 **Microsoft Azure Libraries for Java** 新增至您的專案。 如果您正在使用 IntelliJ，請參閱[安裝 Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation)。 


## <a name="send-messages-to-a-topic"></a>傳送訊息至主題
在本節中，您將建立 Java 主控台專案，並新增程式碼以將訊息傳送至您建立的主題。 

### <a name="create-a-java-console-project"></a>建立 Java 主控台專案
使用 Eclipse 或您選擇的工具建立 Java 專案。 

### <a name="configure-your-application-to-use-service-bus"></a>設定應用程式以使用服務匯流排
新增 Azure 服務匯流排程式庫的參考。 [Maven 中央存放庫](https://search.maven.org/search?q=a:azure-messaging-servicebus)中具有適用於服務匯流排的 Java 用戶端程式庫。 您可以在 Maven 專案檔中用下列相依性宣告來參照此程式庫：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>新增程式碼以將訊息傳送到主題
1. 在 Java 檔案頂端新增下列 `import` 陳述式。 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. 在類別中，定義變數來保存連接字串和主題名稱，如下所示： 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    將 `<NAMESPACE CONNECTION STRING>` 取代為服務匯流排命名空間的連接字串。 接著，將 `<TOPIC NAME>` 取代為主題名稱。
3. 在類別中新增名為 `sendMessage` 的方法，以將一則訊息傳送至主題。 

    ```java
        static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
    }
    ```
1. 在類別中新增名為 `createMessages` 的方法，以建立訊息清單。 一般來說，您會從應用程式的不同部分取得這些訊息。 在這裡，我們會建立範例訊息清單。

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. 新增名為 `sendMessageBatch` 方法的方法，將訊息傳送至您所建立的主題。 這個方法會建立主題的 `ServiceBusSenderClient`，叫用 `createMessages` 方法來取得訊息清單、準備一或多個批次，並將批次傳送至主題。 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-subscription"></a>自訂用帳戶接收訊息
在本節中，您將新增程式碼以從主題的訂用帳戶中取出訊息。 

1. 新增名為 `receiveMessages` 的方法以從訂用帳戶接收訊息。 這個方法會藉由指定處理訊息的處理常式，以及另一項處理錯誤的方式，來建立訂用帳戶的 `ServiceBusProcessorClient`。 然後方法會啟動處理器、等待數秒並列印收到的訊息，然後停止和關閉處理器。

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }
    ```
2. 更新 `main` 方法以叫用 `sendMessage`、`sendMessageBatch`，並更新 `receiveMessages` 方法以擲回 `InterruptedException`。     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>執行應用程式
執行程式後應該會看到類似下列的輸出：

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
```

在 Azure 入口網站中服務匯流排命名空間的 **概觀** 頁面上，您可以看到 **傳入** 和 **傳出** 訊息計數。 請等候一分鐘左右，然後重新整理頁面以查看最新的值。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="傳入和傳出訊息計數" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

切換至中間窗格中的 **主題** 索引標籤，然後選取主題以查看主題的 **服務匯流排主題** 頁面。 在此頁面上，您應該會在 **訊息** 圖表中看到四個傳入和四個傳出訊息。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="傳入和傳出訊息" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

如果您註解 `main` 方法中的 `receiveMessages` 呼叫，然後再次執行應用程式，則會在 **服務匯流排主題** 頁面上看到 8 個傳入訊息 (4 則新訊息)，但有四個傳出訊息。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="已更新的主題頁面" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

如果您在此頁面上選取訂用帳戶，您會進入 **服務匯流排訂用帳戶** 頁面。 您可以在此頁面上看到作用中的訊息計數、寄不出的信件訊息計數等等。 在此範例中，收件者尚未收到四個作用中的訊息。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="作用中訊息計數" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>後續步驟
請參閱下列文件和範例：

- [適用於 Java 的 Azure 服務匯流排用戶端程式庫 - 讀我檔案](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub 範例](https://docs.microsoft.com/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API 參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
