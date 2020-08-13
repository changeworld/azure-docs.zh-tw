---
title: 快速入門：Azure 佇列儲存體程式庫 v12 - Python
description: 了解如何使用 Azure 佇列 Python v12 程式庫來建立佇列，並將訊息新增至該佇列。 接下來，您會了解如何讀取和刪除佇列中的訊息。 您也將了解如何刪除佇列。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 12c2652b4dcef46c5affde2c3fb9ef9288176eb9
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852254"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>快速入門：適用於 Python 的 Azure 佇列儲存體用戶端程式庫 v12

開始使用適用於 Python 的 Azure 佇列儲存體用戶端程式庫 v12。 Azure 佇列儲存體是用來儲存大量訊息的服務，以便日後擷取和處理。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

使用適用於 Python 的 Azure 佇列儲存體用戶端程式庫 v12：

* 建立佇列
* 將訊息新增至佇列
* 窺視佇列中的訊息
* 更新佇列中的訊息
* 從佇列接收訊息
* 刪除佇列中的訊息
* 刪除佇列

其他資源：

* [API 參考文件](https://docs.microsoft.com/python/api/azure-storage-queue/index)
* [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
* [套件 (Python 套件索引)](https://pypi.org/project/azure-storage-queue/) \(英文\)
* [範例](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* Azure 儲存體帳戶 - [建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 適用於您作業系統的 [Python](https://www.python.org/downloads/) - 2.7、3.5 或更新版本

## <a name="setting-up"></a>設定

本節會引導您準備專案以搭配適用於 Python 的 Azure 佇列儲存體用戶端程式庫 v12 使用。

### <a name="create-the-project"></a>建立專案

建立一個名為 *queues-quickstart-v12* 的 Python 應用程式。

1. 在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為專案建立一個新目錄。

    ```console
    mkdir queues-quickstart-v12
    ```

1. 切換至新建立的 *queues-quickstart-v12* 目錄。

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>安裝套件

使用 `pip install` 命令安裝適用於 Python 套件的 Azure Blob 儲存體用戶端程式庫。

```console
pip install azure-storage-queue
```

此命令會安裝適用於 Python 套件的 Azure 佇列儲存體用戶端程式庫，以及它所相依的所有程式庫。 在此案例中，這只是適用於 Python 的 Azure 核心程式庫。

### <a name="set-up-the-app-framework"></a>設定應用程式架構

1. 在程式碼編輯器中開啟新的文字檔
1. 加入 `import` 陳述式
1. 建立程式的結構，包括非常基本的例外狀況處理

    此程式碼如下：

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. 將新檔案以 *queues-quickstart-v12.py* 儲存在 *queues-quickstart-v12* 目錄中。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>物件模型

Azure 佇列儲存體是用來儲存大量訊息的服務。 一則佇列訊息的大小可能高達 64 KB。 佇列可以包含數百萬則訊息，最高可達儲存體帳戶的總容量限制。 佇列通常用來建立要以非同步方式處理的待處理項目 (backlog)。 佇列儲存體提供三種類型資源：

* 儲存體帳戶
* 儲存體帳戶中的佇列
* 佇列中的訊息

下圖顯示資源之間的關係。

![佇列儲存體架構圖](./media/storage-queues-introduction/queue1.png)

使用下列 Python 類別與這些資源互動：

* [QueueServiceClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient)：`QueueServiceClient` 可讓您管理儲存體帳戶中的所有佇列。
* [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient)：`QueueClient` 類別可讓您管理和操作個別佇列及其訊息。
* [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage)：`QueueMessage` 類別代表在佇列上呼叫 [receive_Messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) 時所傳回的個別物件。

## <a name="code-examples"></a>程式碼範例

這些範例程式碼片段會示範如何使用適用於 Python 的 Azure 佇列儲存體用戶端程式庫執行下列動作：

* [取得連接字串](#get-the-connection-string)
* [建立佇列](#create-a-queue)
* [將訊息新增至佇列](#add-messages-to-a-queue)
* [窺視佇列中的訊息](#peek-at-messages-in-a-queue)
* [更新佇列中的訊息](#update-a-message-in-a-queue)
* [從佇列接收訊息](#receive-messages-from-a-queue)
* [刪除佇列中的訊息](#delete-messages-from-a-queue)
* [刪除佇列](#delete-a-queue)

### <a name="get-the-connection-string"></a>取得連接字串

以下程式碼會擷取儲存體帳戶的連接字串。 連接字串會儲存在[設定儲存體連接字串](#configure-your-storage-connection-string)一節中建立的環境變數。

在 `try` 區塊內加入此程式碼：

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>建立佇列

決定新佇列的名稱。 下列程式碼會將 UUID 值附加到佇列名稱，以確保它是唯一的。

> [!IMPORTANT]
> 佇列名稱只能包含小寫字母、數字和連字號，且必須以字母或數字開頭。 每個連字號前後都必須緊接非連字號的字元。 名稱長度也必須為 3 到 63 個字元。 如需為佇列命名的詳細資訊，請參閱[為佇列和中繼資料命名](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)。

建立 [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) 類別的執行個體。 然後，呼叫 [create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) 方法，以在您的儲存體帳戶中建立佇列。

將此程式碼加入到 `try` 區塊的結尾處：

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>將訊息新增至佇列

下列程式碼片段會藉由呼叫 [send_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) 方法，將訊息新增至佇列。 其也會儲存第三次 `send_message` 呼叫所傳回的 [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage)。 `saved_message` 用來在稍後的程式中更新訊息內容。

將此程式碼加入到 `try` 區塊的結尾處：

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>窺視佇列中的訊息

藉由呼叫 [peek_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) 方法來窺視佇列中的訊息。 `peek_messages` 方法會從佇列前面擷取一或多則訊息，但不會更改訊息的可見性。

將此程式碼加入到 `try` 區塊的結尾處：

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>更新佇列中的訊息

藉由呼叫 [update_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) 方法來更新訊息的內容。 `update_message` 方法可以變更訊息的可見度逾時和內容。 訊息內容必須是大小上限為 64 KB 的 UTF-8 編碼字串。 連同新內容，傳入先前在程式碼中儲存的訊息中的值。 `saved_message` 值會識別要更新的訊息。

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>從佇列接收訊息

藉由呼叫 [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) 方法來下載先前新增的訊息。

將此程式碼加入到 `try` 區塊的結尾處：

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>刪除佇列中的訊息

在接收和處理訊息後，從佇列中刪除訊息。 在此情況下，處理只會在主控台上顯示訊息。

應用程式會在處理和刪除訊息之前呼叫 `input`，藉以暫停使用者輸入。 在 [Azure 入口網站](https://portal.azure.com)中確認已正確建立資源，然後才予以刪除。 任何未明確刪除的訊息最後都會再次顯示在佇列中，以提供另一次進行處理的機會。

將此程式碼加入到 `try` 區塊的結尾處：

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>刪除佇列

下列程式碼會使用 [delete_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) 方法刪除佇列，以清除應用程式所建立的資源。

將此程式碼新增到 `try` 區塊的結尾並儲存檔案：

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>執行程式碼

此應用程式會建立三則訊息，並將其新增至 Azure 佇列。 程式碼會列出佇列中的訊息，然後在最後刪除佇列之前，先擷取並刪除訊息。

在主控台視窗中，瀏覽至包含 *queues-quickstart-v12.py* 檔案的目錄，然後執行下列 `python` 命令來執行應用程式。

```console
python queues-quickstart-v12.py
```

應用程式的輸出類似下列範例：

```output
Azure Queue storage v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

當應用程式在接收訊息之前暫停，請在 [Azure 入口網站](https://portal.azure.com)中檢查您的儲存體帳戶。 確認訊息在佇列中。

按下 **Enter** 鍵來接收和刪除訊息。 出現提示時，請再次按下 **Enter** 鍵，以刪除佇列並完成示範。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Python 程式碼建立佇列，並將訊息新增至其中。 然後您會了解如何窺視、擷取和刪除訊息。 最後，您會了解如何刪除訊息佇列。

如需教學課程、範例、快速入門及其他文件，請瀏覽：

> [!div class="nextstepaction"]
> [適用於 Python 開發人員的 Azure](https://docs.microsoft.com/azure/python/)

* 若要深入了解，請參閱[適用於 Python 的 Azure 儲存體程式庫](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)。
* 若要查看更多 Azure 佇列儲存體範例應用程式，請繼續 [Azure 佇列儲存體 v12 Python 用戶端程式庫範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)。
