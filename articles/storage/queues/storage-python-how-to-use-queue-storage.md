---
title: 如何從 Python 使用 Azure 佇列儲存體 v 2.1-Azure 儲存體
description: 瞭解如何使用 Python 的 Azure 佇列服務2.1 來建立和刪除佇列，以及插入、取得和刪除訊息。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 97cb41a47b18d403881432098ecdda9bb26146d2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848174"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>如何從 Python 使用 Azure 佇列儲存體 v2.1

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

本文示範使用 Azure 佇列儲存體服務的常見案例。 涵蓋的案例包括插入、查看、取得和刪除佇列訊息，以及建立和刪除佇列。

> [!IMPORTANT]
> 本文指的是適用于 Python 的 Azure 儲存體用戶端程式庫舊版版本。 若要開始使用最新版本，請參閱[快速入門：適用于 Python 的 Azure 佇列儲存體用戶端程式庫](storage-quickstart-queues-python.md)

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

本文中的範例是以 Python 撰寫，並使用[適用于 python 的 MICROSOFT AZURE 儲存體 SDK]。 如需佇列的詳細資訊，請參閱[後續步驟](#next-steps)一節。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下載並安裝 Azure Storage SDK for Python

[適用于 python 的 AZURE 儲存體 SDK](https://github.com/azure/azure-storage-python)需要 Python 版本2.7、3.3 或更新版本。
 
### <a name="install-via-pypi"></a>透過 PyPi 安裝

若要透過 Python Package Index (PyPI) 安裝，請輸入：

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> 如果您要從 Azure Storage SDK for Python 版本 0.36 或更早版本升級，請先使用 `pip uninstall azure-storage` 解除安裝舊版的 SDK，再安裝最新的封裝。

如需替代安裝方法，請參閱 [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/) \(英文\)。

## <a name="view-the-sample-application"></a>檢視範例應用程式

若要查看並執行範例應用程式，以瞭解如何搭配使用 Python 與 Azure 佇列，請參閱[Azure 儲存體：在 python 中使用 Azure 佇列消費者入門](https://github.com/Azure-Samples/storage-queue-python-getting-started)。 

若要執行範例應用程式，請確定您已安裝 `azure-storage-queue` 和 `azure-storage-common` 封裝。

## <a name="create-a-queue"></a>建立佇列

[QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) 物件可讓您操作佇列。 下列程式碼會建立 `QueueService` 物件。 將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近：

```python
from azure.storage.queue import QueueService
```

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 `QueueService` 物件。 以您的帳戶名稱和金鑰取代 [*我的帳戶*] 和 [ *mykey* ]。

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>將訊息插入佇列

若要將訊息插入佇列，請使用[put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-)方法來建立新訊息，並將它新增至佇列。

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure 佇列訊息會儲存為文字。 如果您想要儲存二進位資料，請先在佇列服務物件上設定 Base64 編碼和解碼函式，然後再將訊息放入佇列中。

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>查看下一個訊息

藉由呼叫[peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-)方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。 根據預設，會 `peek_messages` 查看單一訊息。

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>清除佇列中的訊息

您的程式碼可以使用兩個步驟來將訊息從佇列中移除。 當您呼叫[get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)時，您預設會取得佇列中的下一個訊息。 從 `get_messages` 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言，將會是不可見的。 依預設，此訊息會維持 30 秒的不可見狀態。 若要完成從佇列中移除訊息的作業，您也必須呼叫[delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-)。 這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 您的程式碼會在 `delete_message` 處理完訊息之後立即呼叫。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

自訂從佇列中擷取訊息的方法有兩種。 首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。 下列程式碼範例會使用 `get_messages` 方法，在一次呼叫中取得16個訊息。 接著它會使用 for 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘。

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。 如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。 下列程式碼會使用[update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-)方法來更新訊息。 可見度逾時設定為 0，表示會立即顯示訊息，並且會更新內容。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>取得佇列長度

您可以取得佇列中的估計訊息數目。 [Get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-)方法會要求佇列服務傳回佇列的相關中繼資料，以及 `approximate_message_count` 。 由於佇列服務在回應您的要求之後可以新增或移除訊息，此結果僅為近似值。

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>刪除佇列

若要刪除佇列及其內含的所有訊息，請呼叫[delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-)方法。

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>後續步驟

既然您已瞭解佇列儲存體的基本概念，請參考下列連結以深入瞭解。

* [Azure 佇列 Python API 參考](/python/api/azure-storage-queue)
* [Python 開發人員中心](https://azure.microsoft.com/develop/python/)
* [Azure 儲存體服務 REST API](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
