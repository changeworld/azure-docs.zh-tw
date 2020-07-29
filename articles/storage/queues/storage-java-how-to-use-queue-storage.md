---
title: 如何使用 JAVA 的佇列儲存體-Azure 儲存體
description: 瞭解如何使用佇列儲存體來建立和刪除佇列，以及使用適用于 JAVA 的 Azure 儲存體用戶端程式庫來插入、取得和刪除訊息。
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 17e5a542bc951df5b40144490f05e41aec1a09e8
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319417"
---
# <a name="how-to-use-queue-storage-from-java"></a>如何使用 Java 的佇列儲存體

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

本指南將示範如何使用 Azure 佇列儲存體服務執行一般案例。 相關範例是以 Java 撰寫並使用 [Azure Storage SDK for Java][Azure Storage SDK for Java]。 涵蓋的案例包括**插入**、**查看**、**取得**和**刪除**佇列訊息，以及**建立**和**刪除**佇列。 如需佇列的詳細資訊，請參閱[後續步驟](#next-steps)一節。

> [!IMPORTANT]
> 本文指的是舊版的 Azure 儲存體用戶端程式庫（適用于 JAVA）。 若要開始使用最新版本，請參閱[快速入門：適用于 JAVA 的 Azure 佇列儲存體用戶端程式庫](storage-quickstart-queues-java.md)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>建立 Java 應用程式

在本指南中，您將使用可在本機 JAVA 應用程式中執行的儲存體功能，或在 Azure 中的 web 應用程式內執行的程式碼中。

若要這樣做，您需要安裝 Java Development Kit (JDK)，並在 Azure 訂用帳戶中建立 Azure 儲存體帳戶。 完成這項操作之後，您必須確認您的開發系統符合 GitHub 上的[AZURE 儲存體 SDK For JAVA][Azure Storage SDK for Java]儲存機制中所列的最低需求和相依性。 如果系統符合這些需求，則您可以依照指示，從該儲存機制中下載 Azure Storage Libraries for Java 並安裝在系統上。 完成這些工作之後，您就可以建立使用本文中範例的 JAVA 應用程式。

## <a name="configure-your-application-to-access-queue-storage"></a>設定您的應用程式以存取佇列儲存體

將下列 import 陳述式新增到您要在其中使用 Azure 儲存體 API 存取佇列的 Java 檔案頂端：

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>設定 Azure 儲存體連接字串

Azure 儲存體用戶端會使用儲存體連接字串來儲存存取資料管理服務時所用的端點與認證。 在用戶端應用程式中執行時，您必須以下列格式提供儲存體連接字串 (其中的 AccountName** 和 AccountKey** 值要使用您儲存體帳戶的名稱，以及在 [Azure 入口網站](https://portal.azure.com)中針對該儲存體帳戶而列出的主要存取金鑰)。 本範例將示範如何宣告靜態欄位來存放連接字串：

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

在 Microsoft Azure 的角色內執行的應用程式中，此字串可以儲存在服務組態檔 *ServiceConfiguration.cscfg* 裡，且可以藉由呼叫 **RoleEnvironment.getConfigurationSettings** 方法來存取。 以下是從服務組態檔中名為 **StorageConnectionString** 的 *Setting* 元素取得連接字串的範例：

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

下列範例假設您已經使用這兩個方法之一來取得儲存體連接字串。

## <a name="how-to-create-a-queue"></a>作法：建立佇列
**CloudQueueClient** 物件可讓您取得佇列的參照物件。 下列程式碼將建立 **CloudQueueClient** 物件。 (注意：還有其他方式可建立 **CloudStorageAccount** 物件。如需詳細資訊，請參閱 [Azure 儲存體用戶端 SDK 參考]中的 **CloudStorageAccount**。)

使用 **CloudQueueClient** 物件來取得想要使用佇列的參照。 如果佇列不存在，您可以建立佇列。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-a-message-to-a-queue"></a>作法：將訊息新增至佇列
若要將訊息插入現有佇列，請先建立新的 **CloudQueueMessage**。 接著，呼叫 **addMessage** 方法。 您可以從字串（採用 UTF-8 格式）或位元組陣列建立**CloudQueueMessage** 。 以下程式碼會建立佇列（如果它不存在），並插入訊息 "Hello，World"。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-peek-at-the-next-message"></a>作法：查看下一個訊息
透過呼叫 **peekMessage**，您可以在佇列前面查看訊息，而無需將它從佇列中移除。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>作法：變更佇列訊息的內容
您可以在佇列中就地變更訊息內容。 如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。 下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。 延伸可見度超時會儲存與訊息相關聯的工作狀態，並讓用戶端另一分鐘繼續處理訊息。 您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。 一般來說，您也會保留重試計數，如果訊息重試超過*n*次，您就會刪除它。 這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

下列程式碼範例會在訊息佇列中搜尋，找出內容符合 "Hello, World" 的第一個訊息，然後修改訊息內容並結束。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

另外，下列程式碼範例只更新佇列上第一個可見的訊息。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-get-the-queue-length"></a>作法：取得佇列長度
您可以取得佇列中的估計訊息數目。 **downloadAttributes** 方法會向佇列服務要求數個目前值，包括計算佇列中的訊息數。 此計數只是一個約略值，因為在佇列服務回應您的要求之後，仍有新增或移除訊息的可能。 **getApproximateMessageCount** 方法會傳回呼叫 **downloadAttributes** 所擷取的最後一個值，而無需呼叫佇列服務。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-dequeue-the-next-message"></a>作法：清除下一個佇列訊息
您的程式碼可以使用兩個步驟來清除佇列訊息。 呼叫 **retrieveMessage**時，您會取得佇列中的下一個訊息。 從 **retrieveMessage** 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言將會是不可見的。 依預設，此訊息會維持 30 秒的不可見狀態。 若要完成從佇列中移除訊息的作業，您還必須呼叫**deleteMessage**。 這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 您的程式碼會在處理完訊息之後立即呼叫**deleteMessage** 。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="additional-options-for-dequeuing-messages"></a>清除佇列訊息的其他選項
自訂從佇列中擷取訊息的方法有兩種。 首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。

下列程式碼範例使用 **retrieveMessages** 方法，在一次呼叫中取得 20 個訊息。 然後，它會使用**for**迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘 (300 秒)。 這五分鐘會同時開始所有的訊息，因此，當呼叫**retrieveMessages**之後，經過五分鐘後，任何尚未刪除的訊息都會再次顯示。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-queues"></a>作法：列出佇列
若要取得目前佇列的清單，請呼叫 **CloudQueueClient.listQueues()** 方法，此方法會傳回 **CloudQueue** 物件的集合。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-queue"></a>作法：刪除佇列
若要刪除佇列及其內含的所有訊息，請在 **CloudQueue** 物件上呼叫 **deleteIfExists** 方法。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

> [!NOTE]
> 有一套 SDK 可供在 Android 裝置上使用 Azure 儲存體的開發人員使用。 如需詳細資訊，請參閱 [Azure Storage SDK for Android][Azure Storage SDK for Android]。

## <a name="next-steps"></a>後續步驟
了解佇列儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

* [適用于 JAVA 的 Azure 儲存體 SDK][Azure Storage SDK for Java]
* [Azure 儲存體用戶端 SDK 參考][Azure Storage Client SDK Reference]
* [Azure 儲存體服務 REST API][Azure Storage Services REST API]
* [Azure 儲存體團隊部落格][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure 儲存體用戶端 SDK 參考]: https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
