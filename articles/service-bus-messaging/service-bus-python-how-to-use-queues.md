---
title: 快速入門：透過 Python 使用 Azure 服務匯流排佇列
description: 本文示範如何使用 Python 建立、傳送及接收 Azure 服務匯流排中的訊息。
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: a09f20b2c392dbf219750a76e9570239227dc865
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89458556"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>快速入門：透過 Python 使用 Azure 服務匯流排佇列

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本文示範如何使用 Python 建立、傳送及接收 Azure 服務匯流排中的訊息。 

如需有關 Python Azure 服務匯流排程式庫的詳細資訊，請參閱 [適用於 Python 的服務匯流排程式庫](/python/api/overview/azure/servicebus?view=azure-python)。

## <a name="prerequisites"></a>Prerequisites
- Azure 訂用帳戶。 您可以啟用自己的 [Visual Studio 或 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 服務匯流排命名空間，遵循[快速入門：使用 Azure 入口網站建立服務匯流排主題和訂用帳戶](service-bus-quickstart-topics-subscriptions-portal.md)中的步驟加以建立。 從 [共用存取原則]  畫面複製主要連接字串，以供本文稍後使用。 
- Python 3.4x 或更新版本，已安裝 [Python Azure 服務匯流排][Python Azure Service Bus package]套件。 如需詳細資訊，請參閱 [Python 安裝指南](/azure/developer/python/azure-sdk-install)。 

## <a name="create-a-queue"></a>建立佇列

**ServiceBusClient** 物件可讓您操作佇列。 若要以程式設計方式存取服務匯流排，請在 Python 檔案的頂端附近新增下列程式碼行：

```python
from azure.servicebus import ServiceBusClient
```

新增下列程式碼以建立 **ServiceBusClient** 物件。 使用您的服務匯流排主要連接字串值取代 `<connectionstring>`。 您可以在 [Azure 入口網站][Azure portal]中，於服務匯流排命名空間的 [共用存取原則]  之下找到此值。

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

下列程式碼會使用 **ServiceBusClient** 的 `create_queue` 方法，以預設設定建立名為 `taskqueue` 的佇列：

```python
sb_client.create_queue("taskqueue")
```

您可以使用選項來覆寫預設佇列設定，例如訊息存留時間 (TTL) 或主題大小上限。 下列程式碼會建立名為 `taskqueue` 的佇列，其佇列大小上限為 5 GB 且 TTL 值為 1 分鐘：

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>傳送訊息至佇列

為了將訊息傳送至服務匯流排佇列，應用程式會在 **ServiceBusService** 物件上呼叫 `send` 方法。 下列程式碼範例會建立佇列用戶端，並將測試訊息傳送至 `taskqueue` 佇列。 使用您的服務匯流排主要連接字串值取代 `<connectionstring>`。 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>訊息大小限制和配額

服務匯流排佇列支援的訊息大小上限：在[標準層](service-bus-premium-messaging.md)中為 256 KB 以及在[進階層](service-bus-premium-messaging.md)中為 1 MB。 標頭 (包含標準和自訂應用程式屬性) 可以容納 64 KB 的大小上限。 佇列可保存的訊息數目沒有限制，但佇列所保存的總訊息大小有最高限制。 您可以在建立佇列時定義其大小，其上限為 5 GB。 

如需有關配額的詳細資訊，請參閱 [服務匯流排配額][Service Bus quotas]。

## <a name="receive-messages-from-a-queue"></a>從佇列接收訊息

佇列用戶端會在 **ServiceBusClient** 物件上使用 `get_receiver` 方法，接收來自佇列的訊息。 下列程式碼範例會建立佇列用戶端，並接收來自 `taskqueue` 佇列的訊息。 使用您的服務匯流排主要連接字串值取代 `<connectionstring>`。 

```python
from azure.servicebus import QueueClient

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>使用 peek_lock 參數

`get_receiver` 的選擇性 `peek_lock` 參數會決定在讀取訊息後，服務匯流排是否會從佇列中刪除訊息。 預設的訊息接收模式為 *PeekLock*，或設為 **True** 的 `peek_lock`，其會讀取 (預覽) 並鎖定訊息，而不需從佇列中刪除訊息。 接著，必須明確地完成每則訊息，才能將它從佇列中移除。

若要在讀取訊息後將其從佇列中刪除，您可將 `get_receiver` 的 `peek_lock` 參數設定為 **False**。 在接收作業中刪除訊息是最簡單的模型，但只有應用程式可在發生失敗時容忍遺失訊息才能運作。 若要了解此行為，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 如果訊息在收到時遭到刪除，當應用程式重新啟動並再度開始取用訊息時，其會在當機前遺失所收到的訊息。

如果應用程式無法容忍遺失訊息，則接收會是兩階段的作業。 PeekLock 會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回到應用程式。 在處理或儲存此訊息之後，應用程式會在 **Message** 物件上呼叫 `complete` 方法，以完成接收程序的第二個階段。  `complete` 方法會將訊息標示為已取用，並將其從佇列中移除。

## <a name="handle-application-crashes-and-unreadable-messages"></a>處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式因為某些原因而無法處理訊息，其可在 **Message** 物件上呼叫 `unlock` 方法。 服務匯流排會將佇列中的訊息解除鎖定，讓此訊息可由相同或其他取用應用程式重新接收。

佇列內鎖定的訊息也會逾時。 如果應用程式無法在鎖定逾時到期前處理訊息 (例如，若應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓系統可以重新接收訊息。

如果應用程式在處理訊息後但呼叫 `complete` 方法前當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。 這種行為通常稱為「至少處理一次」  。 每則訊息至少會處理一次；但在特定狀況下，可能會重新傳遞相同訊息。 如果您的案例無法容許重複處理，您可以使用訊息的 **MessageId** 屬性，其會在各傳遞嘗試中保持不變，以處理重複的訊息傳遞。 

> [!TIP]
> 您可以使用[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer/)來管理服務匯流排資源。 服務匯流排總管可讓您連線到服務匯流排命名空間，並輕鬆地管理傳訊實體。 此工具提供進階的功能 (例如匯入/匯出功能)，以及測試主題、佇列、訂用帳戶、轉送服務、通知中樞和事件中樞的能力。

## <a name="next-steps"></a>後續步驟

您現已了解服務匯流排佇列的基本概念，請參閱[佇列、主題和訂用帳戶][Queues, topics, and subscriptions]以取得詳細資訊。

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
