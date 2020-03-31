---
title: 如何使用 Python 中的 Azure 佇列存儲 v2.1 - Azure 存儲
description: 瞭解如何使用 Python 中的 Azure 佇列服務 v2.1 創建和刪除佇列，以及插入、獲取和刪除消息。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: ca0831fd7554058d21e315b67d6965579af1d38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060907"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>如何使用 Python 中的 Azure 佇列存儲 v2.1

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

本文演示了使用 Azure 佇列存儲服務的常見方案。 涵蓋的方案包括插入、查看、獲取和刪除佇列消息，以及創建和刪除佇列。

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>總覽

本文中的示例用 Python 編寫，並使用 Python 的[Microsoft Azure 存儲 SDK。] 如需佇列的詳細資訊，請參閱[後續步驟](#next-steps)一節。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下載並安裝 Azure Storage SDK for Python

[Python 的 Azure 存儲 SDK](https://github.com/azure/azure-storage-python)需要 Python 版本 2.7、3.3 或更高版本。
 
### <a name="install-via-pypi"></a>透過 PyPi 安裝

若要透過 Python Package Index (PyPI) 安裝，請輸入：

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> 如果您要從 Azure Storage SDK for Python 版本 0.36 或更早版本升級，請先使用 `pip uninstall azure-storage` 解除安裝舊版的 SDK，再安裝最新的封裝。

如需替代安裝方法，請參閱 [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/) \(英文\)。

## <a name="view-the-sample-application"></a>檢視範例應用程式

要查看和運行演示如何將 Python 與 Azure 佇列配合使用的應用程式範例，請參閱[Azure 存儲：在 Python 中使用 Azure 佇列入門](https://github.com/Azure-Samples/storage-queue-python-getting-started)。 

若要執行範例應用程式，請確定您已安裝 `azure-storage-queue` 和 `azure-storage-common` 封裝。

## <a name="create-a-queue"></a>建立佇列

[QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) 物件可讓您操作佇列。 以下代碼創建一個`QueueService`物件。 將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近：

```python
from azure.storage.queue import QueueService
```

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 `QueueService` 物件。 將*我的帳戶*和*我的金鑰*替換為您的帳戶名稱和金鑰。

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>將訊息插入佇列

若要將消息插入佇列，請使用[put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-)方法創建新消息並將其添加到佇列中。

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure 佇列消息存儲為文本。 如果要存儲二進位資料，在將消息放入佇列之前，在佇列服務物件上設置 Base64 編碼和解碼功能。

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>查看下一個訊息

您可以通過調用[peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-)方法查看佇列前面的消息，而無需將其從佇列中刪除。 預設情況下，`peek_messages`查看單個消息。

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>清除佇列中的訊息

您的程式碼可以使用兩個步驟來將訊息從佇列中移除。 調用[get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)時，預設情況下會收到佇列中的下一條消息。 從 `get_messages` 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言，將會是不可見的。 依預設，此訊息會維持 30 秒的不可見狀態。 要完成從佇列中刪除消息，還必須調用[delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-)。 這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 處理消息後`delete_message`，您的代碼調用。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

自訂從佇列中擷取訊息的方法有兩種。 首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。 以下代碼示例使用 方法`get_messages`在一次調用中獲取 16 條消息。 接著它會使用 for 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘。

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。 如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。 下面的代碼使用[update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-)方法更新消息。 可見度逾時設定為 0，表示會立即顯示訊息，並且會更新內容。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>取得佇列長度

您可以取得佇列中的估計訊息數目。 [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-)方法要求佇列服務返回有關佇列和 的`approximate_message_count`中繼資料。 由於佇列服務在回應您的要求之後可以新增或移除訊息，此結果僅為近似值。

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>刪除佇列

要刪除佇列及其中包含的所有消息，請調用[delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-)方法。

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>後續步驟

現在您已經瞭解了佇列存儲的基礎知識，請按照這些連結瞭解更多資訊。

* [Azure 佇列 Python API 引用](/python/api/azure-storage-queue)
* [Python 開發人員中心](https://azure.microsoft.com/develop/python/)
* [Azure 存儲服務 REST API](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
