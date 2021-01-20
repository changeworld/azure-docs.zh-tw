---
title: 如何從 Python 使用 Azure 佇列儲存體-Azure 儲存體
description: 瞭解如何使用 Python 中的 Azure 佇列儲存體來建立和刪除佇列，以及插入、取得和刪除訊息。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/19/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 82d18fd79b10a8500cfd9191f143438d69fda401
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600790"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>如何從 Python 使用 Azure 佇列儲存體

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概觀

本文示範使用 Azure 佇列儲存體服務的常見案例。 涵蓋的案例包括插入、查看、取得和刪除佇列訊息。 此外也涵蓋建立和刪除佇列的程式碼。

本文中的範例是以 Python 撰寫，並使用 [適用于 python 的 Azure 佇列儲存體用戶端程式庫](https://github.com/Azure/Azure-SDK-for-Python/tree/master/sdk/storage/azure-storage-queue)。 如需佇列的詳細資訊，請參閱[後續步驟](#next-steps)一節。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下載並安裝 Azure Storage SDK for Python

[適用于 python 的 AZURE 儲存體 SDK](https://github.com/azure/azure-storage-python)需要 python 2.7、3.3 版或更新版本。

### <a name="install-via-pypi"></a>透過 PyPI 安裝

若要透過 Python Package Index (PyPI) 安裝，請輸入：

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> 如果您要從 Azure 儲存體 SDK for Python v 0.36 或更早版本升級，請 `pip uninstall azure-storage` 在安裝最新封裝之前，先使用舊版 sdk 卸載。

如需替代安裝方法，請參閱 [適用于 Python 的 AZURE SDK](https://github.com/Azure/Azure-SDK-for-Python)。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>設定您的應用程式以存取佇列儲存體

# <a name="python-v12"></a>[Python v12](#tab/python)

[`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient)物件可讓您使用佇列。 將下列程式碼新增至您想要以程式設計方式存取 Azure 佇列之任何 Python 檔案的頂端附近：

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true)物件可讓您使用佇列。 下列程式碼會建立 `QueueService` 物件。 將下列程式碼新增至您想要以程式設計方式存取 Azure 儲存體之任何 Python 檔案的頂端附近：

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

`os`封裝會提供取得環境變數的支援。 `uuid`封裝可支援產生佇列名稱的唯一識別碼。

## <a name="create-a-queue"></a>建立佇列

從稍早設定的環境變數中取出連接字串 `AZURE_STORAGE_CONNECTION_STRING` 。

# <a name="python-v12"></a>[Python v12](#tab/python)

下列程式碼會 `QueueClient` 使用儲存體連接字串來建立物件。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

下列程式碼會 `QueueService` 使用儲存體連接字串來建立物件。

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

## <a name="insert-a-message-into-a-queue"></a>將訊息插入佇列

# <a name="python-v12"></a>[Python v12](#tab/python)

若要將訊息插入佇列中，請使用 [`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) 方法。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

若要將訊息插入佇列中，請使用 [`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) 方法來建立新訊息，並將它新增至佇列。

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

Azure 佇列訊息會儲存為文字。 如果您想要儲存二進位資料，請在將訊息放入佇列之前，先設定 Base64 編碼和解碼函數。

# <a name="python-v12"></a>[Python v12](#tab/python)

設定佇列用戶端物件上的 Base64 編碼和解碼函數。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

在佇列儲存體物件上設定 Base64 編碼和解碼函式。

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="peek-at-messages"></a>查看訊息

# <a name="python-v12"></a>[Python v12](#tab/python)

您可以藉由呼叫方法來查看訊息，而不需要從佇列中移除訊息 [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) 。 根據預設，這個方法會查看單一訊息。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

您可以藉由呼叫方法來查看訊息，而不需要從佇列中移除訊息 [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#peek-messages-queue-name--num-messages-none--timeout-none-) 。 根據預設，這個方法會查看單一訊息。

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。 如果訊息代表工作，您可以使用這項功能來更新工作的狀態。

# <a name="python-v12"></a>[Python v12](#tab/python)

下列程式碼會使用 [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) 方法來更新訊息。 可見度逾時設定為 0，表示會立即顯示訊息，並且會更新內容。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

下列程式碼會使用 [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) 方法來更新訊息。 可見度逾時設定為 0，表示會立即顯示訊息，並且會更新內容。

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>取得佇列長度

您可以取得佇列中的估計訊息數目。

# <a name="python-v12"></a>[Python v12](#tab/python)

[Get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-)方法會傳回佇列屬性，包括 `approximate_message_count` 。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

方法會傳回 [`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-queue-metadata-queue-name--timeout-none-) 佇列屬性，包括 `approximate_message_count` 。

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

結果只是近似值，因為在服務回應您的要求之後，就可以新增或移除訊息。

## <a name="dequeue-messages"></a>清除佇列中的訊息

透過兩個步驟將訊息從佇列中移除。 如果您的程式碼無法處理訊息，此兩步驟程式可確保您可以取得相同的訊息，然後再試一次。 `delete_message`在成功處理訊息之後呼叫。

# <a name="python-v12"></a>[Python v12](#tab/python)

當您呼叫 [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-)時，您預設會取得佇列中的下一則訊息。 從 `receive_messages` 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言，將會是不可見的。 依預設，此訊息會維持 30 秒的不可見狀態。 若要完成從佇列中移除訊息的作業，您也必須呼叫 [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-)。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

當您呼叫 [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)時，您預設會取得佇列中的下一則訊息。 從 `get_messages` 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言，將會是不可見的。 依預設，此訊息會維持 30 秒的不可見狀態。 若要完成從佇列中移除訊息的作業，您也必須呼叫 [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-message-queue-name--message-id--pop-receipt--timeout-none-)。

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

自訂從佇列中擷取訊息的方法有兩種。 首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。

# <a name="python-v12"></a>[Python v12](#tab/python)

下列程式碼範例會使用 [`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) 方法，以批次方式取得訊息。 然後，它會使用嵌套迴圈處理每個批次中的每個訊息 `for` 。 它也會將可見度逾時設定為每個訊息五分鐘。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

下列程式碼範例會使用 [`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) 方法，在一個呼叫中取得16個訊息。 接著其會使用 `for` 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘。

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>刪除佇列

# <a name="python-v12"></a>[Python v12](#tab/python)

若要刪除佇列及其內含的所有訊息，請呼叫 [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) 方法。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

若要刪除佇列及其內含的所有訊息，請呼叫 [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-queue-queue-name--fail-not-exist-false--timeout-none-) 方法。

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>後續步驟

既然您已瞭解佇列儲存體的基本概念，請參考下列連結以深入瞭解。

- [Azure 佇列儲存體 Python API 參考](/python/api/azure-storage-queue)
- [Python 開發人員中心](https://azure.microsoft.com/develop/python/)
- [Azure 儲存體 REST API 參考](/rest/api/storageservices/)
