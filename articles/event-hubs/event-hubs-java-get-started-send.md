---
title: 使用 Java 從 Azure 事件中樞傳送或接收事件 (舊版)
description: 本文提供的逐步解說說明如何建立 Java 應用程式，以使用舊有的 azure-eventhubs 套件，從 Azure 事件中樞傳送/接收事件。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: a2cce90b5aa28dac6ff945ac48f70bfd319683b9
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029889"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>使用 Java 將事件傳送至 Azure 事件中樞或從中接收事件 (azure-eventhubs)

本教學課程說明如何建立 Java 應用程式，以將事件傳送至 Azure 事件中樞或從中接收事件。

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱事件中樞概觀和事件中樞功能。

> [!WARNING]
> 本快速入門使用舊版的 **azure-eventhubs** 和 **azure-eventhubs-eph** 套件。 建議您[遷移](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)程式碼，以使用最新的 [azure-messaging-eventhubs](get-started-java-send-v2.md) 套件。 


## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要下列必要條件：

- 使用中的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Java 開發環境。 本教學課程使用 [Eclipse](https://www.eclipse.org/)。
- **建立事件中樞命名空間和事件中樞**。 第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和事件中樞，請依照[這篇文章](event-hubs-create.md)中的程序操作。 然後，依照以下文章中的指示，取得事件中樞的存取金鑰值：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 您可以在您於本教學課程稍後撰寫的程式碼中，使用此存取金鑰。 預設的金鑰名稱是：**RootManageSharedAccessKey**。

## <a name="send-events"></a>傳送事件 
本節說明如何建立可將事件傳送至事件中樞的 Java 應用程式。 

> [!NOTE]
> 您可以從 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) 下載此快速入門來作為範例，並以您事件中樞的值取代 `EventHubConnectionString` 和 `EventHubName` 字串，然後執行。 或者，您可以遵循本教學課程中的步驟，來建立自己的解決方案。

### <a name="add-reference-to-azure-event-hubs-library"></a>加入 Azure 事件中樞程式庫的參考

[Maven 中央儲存機制](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)中的 Maven 專案可使用事件中樞的 Java 用戶端程式庫。 您可以在 Maven 專案檔中用下列相依性宣告來參照此程式庫：

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

對於不同類型的組建環境，您可以明確從 [Maven 中央儲存機制](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)取得最新發佈的 JAR 檔案。  

如果是簡單事件發行者，請針對事件中樞用戶端類別匯入 *com.microsoft.azure.eventhubs* 套件，以及針對公用程式類別匯入 *com.microsoft.azure.servicebus* 套件，例如，與 Azure 服務匯流排訊息用戶端共用的常見例外狀況。 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>撰寫程式碼以將訊息傳送到事件中樞

針對下列範例，在您最喜愛的 Java 開發環境中，先為主控台/殼層應用程式建立新的 Maven 專案。 新增名為 `SimpleSend` 的類別，並在此類別中新增下列程式碼：

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>建構連接字串

使用 ConnectionStringBuilder 類別以建構要傳遞至事件中樞用戶端執行個體的連接字串值。 將預留位置取代為您在建立命名空間和事件中樞時所取得的值：

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>撰寫用來傳送事件的程式碼

將字串轉換為 UTF-8 位元組編碼，藉以建立單一事件。 然後從連接字串建立新的事件中樞用戶端執行個體，並傳送訊息：   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

建置並執行程式，然後確定沒有任何錯誤。

恭喜！ 您現在已將傳送訊息到事件中樞。

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>附錄：如何將訊息路由傳送至事件中樞分割區

在取用者擷取訊息之前，發行者必須先將這些訊息發佈至分割區。 在 com.microsoft.azure.eventhubs.EventHubClient 物件上使用 sendSync() 方法將訊息同步發佈到事件中樞後，可以根據是否指定分割索引鍵，將訊息傳送到特定分割區或以循環配置方式分散到所有可用的分割區。

指定代表分割索引鍵的字串後，將會雜湊處理此索引鍵來判斷事件要傳送到哪個分割區。

若未設定分割區索引鍵，則訊息會循環配置到所有可用的分割區

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>接收事件
本教學課程中的程式碼是根據 [GitHub 上的 EventProcessorSample 程式碼](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)，您可以檢查該程式碼以查看完整的運作中應用程式。

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>在 Java 中使用 EventProcessorHost 接收訊息

**EventProcessorHost** 是一個 Java 類別，透過管理持續檢查點以及來自事件中樞的平行接收，簡化來自事件中樞之事件的接收作業。 使用 EventProcessorHost，您可以將事件分割到多個接收者，即使裝載於不同的節點時也是一樣。 這個範例顯示單一接收者如何使用 EventProcessorHost。

### <a name="create-a-storage-account"></a>建立儲存體帳戶

若要使用 EventProcessorHost，您必須擁有 [Azure 儲存體帳戶][Azure 儲存體帳戶]：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取畫面左上方的 [建立資源]  。
2. 選取 [儲存體]  ，然後選取 [儲存體帳戶]  。 在 [建立儲存體帳戶]  視窗中，輸入儲存體帳戶名稱。 完成其餘欄位，選取您想要的區域，然後選取 [建立]  。
   
    ![在 Azure 入口網站中建立儲存體帳戶](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. 選取新建立的儲存體帳戶，然後選取 [存取金鑰]  ：
   
    ![在 Azure 入口網站中取得您的存取金鑰](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    將 key1 值複製到暫存位置。 您將在本教學課程稍後使用它。

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>使用 EventProcessor 主機建立 Java 專案

適用於事件中樞的 Java 用戶端程式庫可以在來自 [Maven 中央儲存機制](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)的 Maven 專案中使用，而且可在您的 Maven 專案檔內使用下列相依性宣告來參考： 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

對於不同類型的組建環境，您可以明確從 [Maven 中央儲存機制](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)取得最新發佈的 JAR 檔案。

1. 針對下列範例，在您最喜愛的 Java 開發環境中，先為主控台/殼層應用程式建立新的 Maven 專案。 類別稱為 `ErrorNotificationHandler`。     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. 使用下列程式碼，建立名為 `EventProcessorSample`的新類別。 使用您建立事件中樞和儲存體帳戶時所用的值，取代預留位置：
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. 使用下列程式碼，再建立一個名為 `EventProcessor` 的類別：
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

本教學課程使用單一 EventProcessorHost 執行個體。 若要增加輸送量，我們建議您執行多個 EventProcessorHost 執行個體 (最好能在個別的機器上)。  這麼做也會提供備援。 在這些情況下，各種執行個體會自動彼此協調以對已接收的事件進行負載平衡。 如果您想要多個接收者都處理 *所有* 事件，則必須使用 **ConsumerGroup** 概念。 收到來自不同電腦的事件時，根據在其中執行 EventProcessorHost 執行個體的電腦 (或角色) 來指定名稱可能十分有用。

### <a name="publishing-messages-to-eventhub"></a>將訊息發佈至 EventHub

在取用者擷取訊息之前，發行者必須先將這些訊息發佈至分割區。 需注意的是，在 com.microsoft.azure.eventhubs.EventHubClient 物件上使用 sendSync() 方法將訊息同步發佈到事件中樞後，可以根據是否指定分割區索引鍵，將訊息傳送到特定分割區或以循環配置方式分散到所有可用的分割區。

指定代表分割區索引鍵的字串之後，就會對此索引鍵進行雜湊處理，以判斷要將事件傳送到哪個分割區。

若未設定分割區索引鍵，則會將訊息循環配置到所有可用的分割區

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>實作 EventProcessorHost (EPH) 的自訂 CheckpointManager

此 API 所提供的機制，可讓您在預設的實作方式與您的使用案例不相容時實作自訂檢查點管理程式。

預設的檢查點管理程式會使用 Blob 儲存體，但如果您以自己的實作來覆寫 EPH 所使用的檢查點管理程式，您可以使用任何所需的存放區來支援檢查點管理程式實作。

建立要實作 com.microsoft.azure.eventprocessorhost.ICheckpointManager 介面的類別

請使用您自訂的檢查點管理程式實作 (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

在您的實作中，您可以覆寫預設的檢查點機制，並根據您自己的資料存放區 (例如 SQL Server、CosmosDB 和 Azure Cache for Redis) 實作自己的檢查點。 我們建議用來支援檢查點管理程式實作的存放區，可供正在處理取用者群組事件的所有 EPH 執行個體存取。

您可以使用環境中可用的任何資料存放區。

com.microsoft.azure.eventprocessorhost.EventProcessorHost 類別可為您提供兩個建構函式，讓您能夠覆寫 EventProcessorHost 的檢查點管理程式。


## <a name="next-steps"></a>後續步驟
請閱讀下列文章： 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中樞的功能與術語](event-hubs-features.md)
- [事件中樞常見問題集](event-hubs-faq.md)

