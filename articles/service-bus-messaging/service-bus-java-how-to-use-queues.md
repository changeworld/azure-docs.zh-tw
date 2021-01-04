---
title: 透過 Java 使用 Azure 服務匯流排佇列 (azure-messaging-servicebus)
description: 在本教學課程中，您將了解如何使用 Java 以對 Azure 服務匯流排佇列傳送和接收訊息。 您會使用新的 azure-messaging-servicebus 套件。
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: d95c96e76a3463a77cc64234a909cc1e3d093837
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630219"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>在 Azure 服務匯流排佇列 (Java) 中傳送和接收訊息
在本快速入門中，您將建立 Java 應用程式以將訊息傳送至 Azure 服務匯流排佇列，並從中接收訊息。 

> [!IMPORTANT]
> 本快速入門使用新的 azure-messaging-servicebus 套件。 如需使用舊版 azure-servicebus 套件的快速入門，請參閱[使用 azure-servicebus 傳送和接收訊息](service-bus-java-how-to-use-queues-legacy.md)。


## <a name="prerequisites"></a>先決條件
- Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或是[註冊免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果您沒有可用的佇列，請執行[使用 Azure 入口網站建立服務匯流排佇列](service-bus-quickstart-portal.md)一文中的步驟，以建立佇列。 請記下服務匯流排命名空間的 **連接字串**，以及您所建立的 **佇列** 名稱。
- 安裝 [Azure SDK for Java][Azure SDK for Java]。 如果您使用的是 Eclipse，則可以安裝包含 Azure SDK for Java 的[適用於 Eclipse 的 Azure 工具組][Azure Toolkit for Eclipse]。 然後您可以將 **Microsoft Azure Libraries for Java** 新增至您的專案。 如果您正在使用 IntelliJ，請參閱[安裝 Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation)。 


## <a name="send-messages-to-a-queue"></a>傳送訊息至佇列
在本節中，您將建立 Java 主控台專案，並新增程式碼以將訊息傳送至您稍早建立的佇列。 

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

### <a name="add-code-to-send-messages-to-the-queue"></a>新增程式碼以將訊息傳送到佇列
1. 在 Java 檔案頂端新增下列 `import` 陳述式。 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. 在類別中，定義變數來保存連接字串和佇列名稱，如下所示： 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    將 `<NAMESPACE CONNECTION STRING>` 取代為服務匯流排命名空間的連接字串。 接著，將 `<QUEUE NAME>` 取代為佇列名稱。
3. 在類別中新增名為 `sendMessage` 的方法，以將一則訊息傳送至佇列。 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
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
1. 新增名為 `sendMessageBatch` 方法的方法，將訊息傳送至您所建立的佇列。 這個方法會建立佇列的 `ServiceBusSenderClient`，叫用 `createMessages` 方法來取得訊息清單、準備一或多個批次，並將批次傳送至佇列。 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
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
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-queue"></a>從佇列接收訊息
在本節中，您將新增程式碼以從佇列中取出訊息。 

1. 新增名為 `receiveMessages` 的方法以從佇列接收訊息。 這個方法會藉由指定處理訊息的處理常式，以及另一項處理錯誤的方式，來建立佇列的 `ServiceBusProcessorClient`。 然後方法會啟動處理器、等待數秒並列印收到的訊息，然後停止和關閉處理器。

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
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
執行應用程式時，您會在主控台視窗中看到下列訊息。 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

在 Azure 入口網站中服務匯流排命名空間的 **概觀** 頁面上，您可以看到 **傳入** 和 **傳出** 訊息計數。 請等候一分鐘左右，然後重新整理頁面以查看最新的值。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="傳入和傳出訊息計數" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

選取此 **概觀** 頁面上的佇列，瀏覽至 **服務匯流排佇列** 頁面。 您也會在此頁面上看到 **傳入** 和 **傳出** 訊息計數。 您也會看到其他資訊，例如佇列的 **目前大小**、**大小上限**、**作用中訊息計數** 等等。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="佇列詳細資料" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>後續步驟
請參閱下列文件和範例：

- [適用於 Java 的 Azure 服務匯流排用戶端程式庫 - 讀我檔案](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub 範例](https://docs.microsoft.com/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API 參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
