---
title: 快速入門：Azure 佇列儲存體程式庫 v12 - Java
description: 了解如何使用 Azure 佇列 Java v12 程式庫來建立佇列，並將訊息新增至該佇列。 接下來，您會了解如何讀取和刪除佇列中的訊息。 您也將了解如何刪除佇列。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/4/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 013281db2821c99f6942edd1322f4978e100c144
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729859"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>快速入門：適用於 Java 的 Azure 佇列儲存體用戶端程式庫 v12

開始使用適用於 Java 的 Azure 佇列儲存體用戶端程式庫 v12。 Azure 佇列儲存體是用來儲存大量訊息的服務，以便日後擷取和處理。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

使用適用於 Java 的 Azure 佇列儲存體用戶端程式庫 v12：

* 建立佇列
* 將訊息新增至佇列
* 窺視佇列中的訊息
* 更新佇列中的訊息
* 接收和刪除佇列中的訊息
* 刪除佇列

[API 參考文件](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/index.html) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue) | [套件 (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) | [範例](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Prerequisites

* [Java 開發套件 (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) 8 版或更新版本
* [Apache Maven](https://maven.apache.org/download.cgi)
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* Azure 儲存體帳戶 - [建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>設定

本節會引導您準備專案以搭配適用於 Java 的 Azure 佇列儲存體用戶端程式庫 v12 使用。

### <a name="create-the-project"></a>建立專案

建立名為 *queues-quickstart-v12* 的 Java 應用程式。

1. 在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 Maven 建立名為 *queues-quickstart-v12* 的新主控台應用程式。 鍵入下列 **mvn** 命令，以建立 "Hello world!" Java 專案。

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
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
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. 切換至新建立的 *queues-quickstart-v12* 目錄。

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>安裝套件

在文字編輯器中開啟 *pom.xml* 檔案。 將下列相依性元素加入至相依性群組。

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>設定應用程式架構

從專案目錄：

1. 瀏覽至 */src/main/java/com/queues/quickstart* 目錄
1. 在編輯器中開啟 *App.java* 檔案
1. 刪除 `System.out.println("Hello world!");` 陳述式
1. 新增 `import` 指示詞

此程式碼如下：

```java
package com.queues.quickstart;

/**
 * Azure queue storage v12 SDK quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>物件模型

Azure 佇列儲存體是用來儲存大量訊息的服務。 一則佇列訊息的大小可能高達 64 KB。 佇列可以包含數百萬則訊息，最高可達儲存體帳戶的總容量限制。 佇列通常用來建立要以非同步方式處理的待處理項目 (backlog)。 佇列儲存體提供三種類型資源：

* 儲存體帳戶
* 儲存體帳戶中的佇列
* 佇列中的訊息

下圖顯示資源之間的關係。

![佇列儲存體架構圖](./media/storage-queues-introduction/queue1.png)

使用下列 Java 類別與這些資源互動：

* [QueueClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClientBuilder.html)：`QueueClientBuilder` 類別會設定並具現化 `QueueClient` 物件。
* [QueueServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueServiceClient.html)：`QueueServiceClient` 可讓您管理儲存體帳戶中的所有佇列。
* [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html)：`QueueClient` 類別可讓您管理和操作個別佇列及其訊息。
* [QueueMessageItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/QueueMessageItem.html)：`QueueMessageItem` 類別代表在佇列上呼叫 [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-) 時所傳回的個別物件。

## <a name="code-examples"></a>程式碼範例

這些範例程式碼片段會示範如何使用適用於 Java 的 Azure 佇列儲存體用戶端程式庫執行下列動作：

* [取得連接字串](#get-the-connection-string)
* [建立佇列](#create-a-queue)
* [將訊息新增至佇列](#add-messages-to-a-queue)
* [窺視佇列中的訊息](#peek-at-messages -in-a-queue)
* [更新佇列中的訊息](#update-a-message-in-a-queue)
* [接收和刪除佇列中的訊息](#receive-and-delete-messages-from-a-queue)
* [刪除佇列](#delete-a-queue)

### <a name="get-the-connection-string"></a>取得連接字串

以下程式碼會擷取儲存體帳戶的連接字串。 連接字串會儲存在[設定儲存體連接字串](#configure-your-storage-connection-string)一節中建立的環境變數。

在 `main` 方法內新增此程式碼：

```java
System.out.println("Azure Queues storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>建立佇列

決定新佇列的名稱。 下列程式碼會將 GUID 值附加到佇列名稱，以確保它是唯一的。

> [!IMPORTANT]
> 佇列名稱只能包含小寫字母、數字和連字號，且必須以字母或數字開頭。 每個連字號前後都必須緊接非連字號的字元。 名稱長度也必須為 3 到 63 個字元。 如需為佇列命名的詳細資訊，請參閱[為佇列和中繼資料命名](/rest/api/storageservices/naming-queues-and-metadata)。


建立 [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html) 類別的執行個體。 然後，呼叫 [create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#create--) 方法，以在您的儲存體帳戶中建立佇列。

將此程式碼加入到 `main` 方法的結尾處：

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>將訊息新增至佇列

下列程式碼片段會藉由呼叫 [sendMessage](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#sendMessage-java.lang.String-) 方法，將訊息新增至佇列。 其也會儲存 `sendMessage` 呼叫所傳回的 [SendMessageResult](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/SendMessageResult.html)。 結果用於在稍後的程式中更新訊息。

將此程式碼加入到 `main` 方法的結尾處：

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>窺視佇列中的訊息

藉由呼叫 [peekMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#peekMessages-java.lang.Integer-java.time.Duration-com.azure.core.util.Context-) 方法來窺視佇列中的訊息。 `peelkMessages` 方法會從佇列前面擷取一或多則訊息，但不會更改訊息的可見性。

將此程式碼加入到 `main` 方法的結尾處：

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>更新佇列中的訊息

藉由呼叫 [updateMessage](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#updateMessage-java.lang.String-java.lang.String-java.lang.String-java.time.Duration-) 方法來更新訊息的內容。 `updateMessage` 方法可以變更訊息的可見度逾時和內容。 訊息內容必須是大小上限為 64 KB 的 UTF-8 編碼字串。 連同訊息的新內容，使用先前在程式碼中儲存的 `SendMessageResult`，傳入訊息識別碼和 pop 回條。 訊息識別碼和 pop 回條可識別要更新的訊息。

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>接收和刪除佇列中的訊息

藉由呼叫 [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-java.time.Duration-java.time.Duration-com.azure.core.util.Context-) 方法來下載先前新增的訊息。 範例程式碼也會在接收和處理訊息後，從佇列中刪除訊息。 在此情況下，處理只會在主控台上顯示訊息。

應用程式會在接收和刪除訊息之前呼叫 `System.console().readLine();`，藉以暫停使用者輸入。 在 [Azure 入口網站](https://portal.azure.com)中確認已正確建立資源，然後才予以刪除。 任何未明確刪除的訊息最後都會再次顯示在佇列中，以提供另一次進行處理的機會。

將此程式碼加入到 `main` 方法的結尾處：

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>刪除佇列

下列程式碼會使用 [delete](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#delete--) 方法刪除佇列，以清除應用程式所建立的資源。

將此程式碼加入到 `main` 方法的結尾處：

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>執行程式碼

此應用程式會建立三則訊息，並將其新增至 Azure 佇列。 程式碼會列出佇列中的訊息，然後在最後刪除佇列之前，先擷取並刪除訊息。

在您的主控台視窗中，瀏覽至您的應用程式目錄，並建置和執行應用程式。

```console
mvn compile
```

接著，建置封裝。

```console
mvn package
```

執行下列 `mvn` 命令以執行應用程式。

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

應用程式的輸出類似下列範例：

```output
Azure Queues storage v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

當應用程式在接收訊息之前暫停，請在 [Azure 入口網站](https://portal.azure.com)中檢查您的儲存體帳戶。 確認訊息在佇列中。

按下 **Enter** 鍵來接收和刪除訊息。 出現提示時，請再次按下 **Enter** 鍵，以刪除佇列並完成示範。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Java 程式碼建立佇列，並將訊息新增至其中。 然後您會了解如何窺視、擷取和刪除訊息。 最後，您會了解如何刪除訊息佇列。

如需教學課程、範例、快速入門及其他文件，請瀏覽：

> [!div class="nextstepaction"]
> [適用於 Java 雲端開發人員的 Azure](https://docs.microsoft.com/azure/developer/java/)

* 若要查看更多 Azure 佇列儲存體範例應用程式，請繼續 [Azure 佇列儲存體 SDK v12 Java 用戶端程式庫範例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)。
