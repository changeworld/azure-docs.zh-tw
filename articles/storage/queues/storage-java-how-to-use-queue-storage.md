---
title: 如何使用 JAVA 的佇列儲存體-Azure 儲存體
description: 瞭解如何使用佇列儲存體來建立和刪除佇列。 瞭解如何使用適用于 JAVA 的 Azure 儲存體用戶端程式庫來插入、查看、取得和刪除訊息。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 08/19/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 997a37ac4252813abf1b35877cd34e192ec3e2ae
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585712"
---
# <a name="how-to-use-queue-storage-from-java"></a>如何使用 JAVA 的佇列儲存體

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概觀

本指南將說明如何使用 Azure 佇列儲存體服務來撰寫常見案例的程式碼。 相關範例是以 Java 撰寫並使用 [Azure Storage SDK for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)。 案例包括 **插入**、 **查看**、 **取得** 和 **刪除** 佇列訊息。 此外也涵蓋 **建立** 和 **刪除** 佇列的程式碼。 如需佇列的詳細資訊，請參閱[後續步驟](#next-steps)一節。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>建立 Java 應用程式

# <a name="java-v12"></a>[JAVA v12](#tab/java)

首先，請確認您的開發系統符合 [適用于 JAVA 的 Azure 佇列儲存體用戶端程式庫 v12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)中所列的必要條件。

若要建立名為的 JAVA 應用程式 `queues-how-to-v12` ：

1. 在主控台視窗中 (例如 cmd、PowerShell 或 Bash) ，請使用 Maven 來建立名稱為的新主控台應用程式 `queues-how-to-v12` 。 輸入下列 `mvn` 命令以建立 "hello world" JAVA 專案。

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

1. Switch to the newly created `queues-howto-v12` directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>安裝套件

`pom.xml`在文字編輯器中開啟檔案。 將下列相依性元素加入至相依性群組。

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

在您想要使用 Azure 儲存體 Api 來存取佇列的 JAVA 檔案頂端，新增下列 import 語句：

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

Azure 儲存體的用戶端會使用儲存體連接字串來存取資料管理服務。 取得 [Azure 入口網站](https://portal.azure.com)中列出的儲存體帳戶名稱和主要存取金鑰。 使用它們做為 `AccountName` `AccountKey` 連接字串中的和值。 本範例將示範如何宣告靜態欄位來存放連接字串：

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

您可以將此字串儲存在稱為的服務設定檔中 `ServiceConfiguration.cscfg` 。 如果是在 Microsoft Azure 角色內執行的應用程式，請呼叫來存取連接字串 `RoleEnvironment.getConfigurationSettings` 。 以下是從名為的元素取得連接字串的範例 `Setting` `StorageConnectionString` ：

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

下列範例假設您有一個 `String` 包含儲存體連接字串的物件。

## <a name="how-to-create-a-queue"></a>作法：建立佇列

# <a name="java-v12"></a>[JAVA v12](#tab/java)

`QueueClient`物件包含與佇列互動的作業。 下列程式碼會建立 `QueueClient` 物件。 使用 `QueueClient` 物件來建立您要使用的佇列。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

`CloudQueueClient`物件可讓您取得佇列的參考物件。 下列程式碼會建立 `CloudQueueClient` 物件，以提供您想要使用之佇列的參考。 如果佇列不存在，您可以建立佇列。

> [!NOTE]
> 還有其他方法可以建立 `CloudStorageAccount` 物件。 如需詳細資訊，請參閱 `CloudStorageAccount` [Azure 儲存體用戶端 SDK 參考](https://azure.github.io/azure-sdk-for-java/storage.html)。 ) 

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

若要將訊息插入現有佇列中，請呼叫 `sendMessage` 方法。 訊息可以是 UTF-8 格式的字串 () 或位元組陣列。 以下是將字串訊息傳送至佇列的程式碼。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

若要將訊息插入現有佇列，請先建立新的 `CloudQueueMessage` 。 接下來，呼叫 `addMessage` 方法。 `CloudQueueMessage`可以從字串 (utf-8 格式) 或位元組陣列建立。 以下程式碼會建立佇列 (（如果不存在）) 並插入訊息 `Hello, World` 。

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

您可以藉由呼叫，在佇列前面查看訊息，而不需要將它從佇列中移除 `peekMessage` 。

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

下列程式碼範例會搜尋訊息的佇列、找出符合的第一個訊息內容 `Hello, world` 、修改訊息內容，然後結束。

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

方法會傳回 `getProperties` 數個值，包括目前在佇列中的訊息數目。 計數只是近似值，因為可以在要求之後新增或移除訊息。 `getApproximateMessageCount`方法會傳回呼叫所取出的最後一個值 `getProperties` ，而不會呼叫佇列儲存體。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

方法會抓取 `downloadAttributes` 數個值，包括目前在佇列中的訊息數目。 計數只是近似值，因為可以在要求之後新增或移除訊息。 `getApproximateMessageCount`方法會傳回呼叫所取出的最後一個值 `downloadAttributes` ，而不會呼叫佇列儲存體。

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

您的程式碼可以使用兩個步驟來清除佇列訊息。 當您呼叫時 `receiveMessage` ，會取得佇列中的下一則訊息。 從 `receiveMessage` 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言，將會是不可見的。 依預設，此訊息會維持 30 秒的不可見狀態。 若要完成從佇列中移除訊息，您還必須呼叫 `deleteMessage` 。 如果您的程式碼無法處理訊息，此兩步驟程式可確保您可以取得相同的訊息，然後再試一次。 您的程式碼會在 `deleteMessage` 處理完訊息之後立即呼叫。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

您的程式碼可以使用兩個步驟來清除佇列訊息。 當您呼叫時 `retrieveMessage` ，會取得佇列中的下一則訊息。 從 `retrieveMessage` 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言，將會是不可見的。 依預設，此訊息會維持 30 秒的不可見狀態。 若要完成從佇列中移除訊息，您還必須呼叫 `deleteMessage` 。 如果您的程式碼無法處理訊息，此兩步驟程式可確保您可以取得相同的訊息，然後再試一次。 您的程式碼會在 `deleteMessage` 處理完訊息之後立即呼叫。

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

下列程式碼範例將使用 `receiveMessages` 方法，在一次呼叫中取得 20 個訊息。 接著其會使用 `for` 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘 (300 秒)。 所有訊息的等候時間都是同時啟動的。 當自呼叫後經過五分鐘後 `receiveMessages` ，任何未刪除的訊息都會重新顯示。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

下列程式碼範例將使用 `retrieveMessages` 方法，在一次呼叫中取得 20 個訊息。 接著其會使用 `for` 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘 (300 秒)。 所有訊息的等候時間都是同時啟動的。 當自呼叫後經過五分鐘後 `retrieveMessages` ，任何未刪除的訊息都會重新顯示。

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

若要取得目前佇列的清單，請呼叫 `QueueServiceClient.listQueues()` 方法，此方法會傳回物件的集合 `QueueItem` 。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

若要取得目前佇列的清單，請呼叫 `CloudQueueClient.listQueues()` 方法，此方法會傳回物件的集合 `CloudQueue` 。

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

若要刪除佇列及其內含的所有訊息，請 `delete` 在物件上呼叫方法 `QueueClient` 。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[JAVA v8](#tab/java8)

若要刪除佇列及其內含的所有訊息，請 `deleteIfExists` 在物件上呼叫方法 `CloudQueue` 。

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

既然您已瞭解佇列儲存體的基本概念，請遵循下列連結以瞭解更複雜的儲存體工作。

- [Azure Storage SDK for Java](https://github.com/Azure/Azure-SDK-for-Java)
- [Azure 儲存體用戶端 SDK 參考](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)
- [Azure 儲存體服務 REST API](/rest/api/storageservices/)
- [Azure 儲存體 team blog](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)
