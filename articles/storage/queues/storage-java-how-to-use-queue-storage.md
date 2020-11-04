---
title: 如何使用 JAVA 的佇列儲存體-Azure 儲存體
description: 瞭解如何使用佇列儲存體來建立和刪除佇列。 瞭解如何使用適用于 JAVA 的 Azure 儲存體用戶端程式庫來插入、查看、取得和刪除訊息。
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 08/19/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: c2ee32b3ced8fdcd5f9f889c4fd0183e46ad5d8d
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93346004"
---
# <a name="how-to-use-queue-storage-from-java"></a>如何使用 Java 的佇列儲存體

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概觀

本指南將為您示範如何使用 Azure 佇列儲存體服務撰寫一般案例的程式碼。 相關範例是以 Java 撰寫並使用 [Azure Storage SDK for Java][Azure Storage SDK for Java]。 案例包括 **插入** 、 **查看** 、 **取得** 和 **刪除** 佇列訊息。 此外也涵蓋 **建立** 和 **刪除** 佇列的程式碼。 如需佇列的詳細資訊，請參閱[後續步驟](#next-steps)一節。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>建立 Java 應用程式

# <a name="java-v12"></a>[JAVA v12](#tab/java)

首先，請確認您的開發系統符合 [Azure 佇列儲存體適用于 JAVA v12 的用戶端程式庫](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)中所列的必要條件。

若要建立名為「 *佇列-如何-v12* 」的 JAVA 應用程式：

1. 在主控台視窗中 (例如 cmd、PowerShell 或 Bash) ，請使用 Maven 來建立新的主控台應用程式，並將其命名 *為佇列-how to-v12* 。 鍵入下列 **mvn** 命令，以建立 "Hello world!" Java 專案。

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

1. 產生專案的輸出應該看起來像這樣：

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *queues-howto-v12* directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>安裝套件

在文字編輯器中開啟 *pom.xml* 檔案。 將下列相依性元素加入至相依性群組。

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

首先，請確認您的開發系統符合 [AZURE 儲存體 SDK For JAVA v8](https://github.com/azure/azure-storage-java)中所列的必要條件。 請依照指示下載及安裝適用于 JAVA 的 Azure 儲存體程式庫。 然後，您可以使用本文中的範例來建立 JAVA 應用程式。

---

## <a name="configure-your-application-to-access-queue-storage"></a>設定您的應用程式以存取佇列儲存體

將下列 import 陳述式新增到您要在其中使用 Azure 儲存體 API 存取佇列的 Java 檔案頂端：

# <a name="java-v12"></a>[JAVA v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>設定 Azure 儲存體連接字串

Azure 儲存體用戶端會使用儲存體連接字串來存取資料管理服務。 取得 [Azure 入口網站](https://portal.azure.com)中列出的儲存體帳戶名稱和主要存取金鑰。 使用它們做為連接字串中的 *AccountName* 和 *AccountKey* 值。 本範例將示範如何宣告靜態欄位來存放連接字串：

# <a name="java-v12"></a>[JAVA v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

您可以將此字串儲存在名為 *serviceconfiguration.cscfg* 的服務設定檔中。 如果是在 Microsoft Azure 角色內執行的應用程式，請呼叫 **RoleEnvironment >roleenvironment.getconfigurationsettings** 來存取連接字串。 以下是從名為 *StorageConnectionString* 的 **設定** 元素取得連接字串的範例：

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

下列範例假設您有一個包含儲存體連接字串的 **string** 物件。

## <a name="how-to-create-a-queue"></a>作法：建立佇列

# <a name="java-v12"></a>[JAVA v12](#tab/java)

**QueueClient** 物件包含與佇列互動的作業。 下列程式碼會建立 **QueueClient** 物件。 使用 **QueueClient** 物件來建立您要使用的佇列。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

**CloudQueueClient** 物件可讓您取得佇列的參照物件。 下列程式碼將建立 **CloudQueueClient** 物件。 (注意：還有其他方式可建立 **CloudStorageAccount** 物件。如需詳細資訊，請參閱 [Azure 儲存體用戶端 SDK 參考]中的 **CloudStorageAccount** 。)

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

---

## <a name="how-to-add-a-message-to-a-queue"></a>作法：將訊息新增至佇列

# <a name="java-v12"></a>[JAVA v12](#tab/java)

若要將訊息插入現有佇列中，請呼叫 **sendMessage** 方法。 訊息可以是 UTF-8 格式的字串 () 或位元組陣列。 以下是將字串訊息傳送至佇列的程式碼。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

若要將訊息插入現有佇列，請先建立新的 **CloudQueueMessage** 。 接著，呼叫 **addMessage** 方法。 您可以從 (UTF-8 格式的字串) 或位元組陣列來建立 **>cloudqueuemessage** 。 以下程式碼會建立佇列 (（如果不存在）) 並插入 "Hello，World" 訊息。

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

---

## <a name="how-to-peek-at-the-next-message"></a>作法：查看下一個訊息

透過呼叫 **peekMessage** ，您可以在佇列前面查看訊息，而無需將它從佇列中移除。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

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

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>作法：變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。 如果訊息代表工作工作，您可以使用這項功能來更新狀態。 下列程式碼會以新的內容更新佇列訊息，並將可見度 timeout 設定為延長30秒。 延伸可見度 timeout 可讓用戶端另外30秒繼續處理訊息。 您也可以保留重試計數。 如果訊息重試超過 *n* 次，您會將它刪除。 此案例會防止每次處理時都會觸發應用程式錯誤的訊息。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

下列程式碼範例會搜尋訊息的佇列、找出符合搜尋字串的第一個訊息內容、修改訊息內容，然後結束。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

下列程式碼範例會搜尋訊息的佇列、找出符合 "Hello，World" 的第一個訊息內容、修改訊息內容，然後結束。

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

---

下列程式碼範例只會更新佇列中第一個可見的訊息。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

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

---

## <a name="how-to-get-the-queue-length"></a>作法：取得佇列長度

您可以取得佇列中的估計訊息數目。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

**GetProperties** 方法會要求佇列服務多個目前的值。 其中一個值是佇列中的訊息數目的計數。 計數只是近似值，因為可以在要求之後新增或移除訊息。 **GetApproximateMessageCount** 方法會傳回 **getProperties** 呼叫所抓取的最後一個值，而不需要呼叫佇列服務。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

**DownloadAttributes** 方法會要求佇列服務多個目前的值。 其中一個值是佇列中的訊息數目的計數。 計數只是近似值，因為可以在要求之後新增或移除訊息。 **getApproximateMessageCount** 方法會傳回呼叫 **downloadAttributes** 所擷取的最後一個值，而無需呼叫佇列服務。

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

---

## <a name="how-to-dequeue-the-next-message"></a>作法：清除下一個佇列訊息

# <a name="java-v12"></a>[JAVA v12](#tab/java)

您的程式碼可以使用兩個步驟來清除佇列訊息。 當您呼叫 **receiveMessage** 時，您會取得佇列中的下一則訊息。 從這個佇列中讀取訊息的任何其他程式碼，都會無法看到從 **receiveMessage** 傳回的訊息。 依預設，此訊息會維持 30 秒的不可見狀態。 若要完成從佇列中移除訊息的作業，您也必須呼叫 **deleteMessage** 。 如果您的程式碼無法處理訊息，此兩步驟程式可確保您可以取得相同的訊息，然後再試一次。 您的程式碼會在處理完訊息之後立即呼叫 **deleteMessage** 。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

您的程式碼可以使用兩個步驟來清除佇列訊息。 呼叫 **retrieveMessage** 時，您會取得佇列中的下一個訊息。 從 **retrieveMessage** 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言將會是不可見的。 依預設，此訊息會維持 30 秒的不可見狀態。 若要完成從佇列中移除訊息的作業，您也必須呼叫 **deleteMessage** 。 如果您的程式碼無法處理訊息，此兩步驟程式可確保您可以取得相同的訊息，然後再試一次。 您的程式碼會在處理完訊息之後立即呼叫 **deleteMessage** 。

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

---

## <a name="additional-options-for-dequeuing-messages"></a>清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。 首先，取得一批訊息 (最多 32) 。 其次，設定較長或較短的隱藏 timeout，讓您的程式碼更多或更少的時間來完整處理每個訊息。

# <a name="java-v12"></a>[JAVA v12](#tab/java)

下列程式碼範例會使用 **>receivemessages** 方法，在一個呼叫中取得20個訊息。 然後，它會使用 **for** 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘 (300 秒)。 所有訊息的等候時間都是同時啟動的。 自呼叫 **>receivemessages** 後五分鐘之後，任何未刪除的訊息都會重新顯示。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

下列程式碼範例使用 **retrieveMessages** 方法，在一次呼叫中取得 20 個訊息。 然後，它會使用 **for** 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘 (300 秒)。 所有訊息的等候時間都是同時啟動的。 自呼叫 **>retrievemessages** 後五分鐘之後，任何未刪除的訊息都會重新顯示。

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

---

## <a name="how-to-list-the-queues"></a>作法：列出佇列

# <a name="java-v12"></a>[JAVA v12](#tab/java)

若要取得目前佇列的清單，請呼叫 **QueueServiceClient. listQueues ( # B1** 方法，它會傳回 **QueueItem** 物件的集合。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

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

---

## <a name="how-to-delete-a-queue"></a>作法：刪除佇列

# <a name="java-v12"></a>[JAVA v12](#tab/java)

若要刪除佇列及其內含的所有訊息，請呼叫 **QueueClient** 物件上的 **delete** 方法。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

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

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>後續步驟

了解佇列儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

- [Azure Storage SDK for Java][Azure Storage SDK for Java]
- [Azure 儲存體用戶端 SDK 參考][Azure Storage Client SDK Reference]
- [Azure 儲存體服務 REST API][Azure Storage Services REST API]
- [Azure 儲存體團隊部落格][Azure Storage Team Blog]

[Azure SDK for Java]: https://github.com/azure/azure-sdk-for-java
[Azure Storage SDK for Java]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage
[Azure 儲存體用戶端 SDK 參考]: https://azure.github.io/azure-sdk-for-java/storage.html
[Azure Storage Services REST API]: /rest/api/storageservices/
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
