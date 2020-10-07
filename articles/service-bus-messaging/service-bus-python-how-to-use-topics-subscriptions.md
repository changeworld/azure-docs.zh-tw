---
title: 快速入門：透過 Python 使用 Azure 服務匯流排主題和訂用帳戶
description: 本文示範如何建立 Azure 服務匯流排主題、訂用帳戶、將訊息傳送到主題及接收來自訂用帳戶的訊息。
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: f6d1b25cb502b8cb208ba5b59c91667e03c77778
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88064378"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>快速入門：透過 Python 使用服務匯流排主題和訂用帳戶

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文說明如何使用 Python 搭配服務匯流排主題和訂用帳戶。 這些範例會使用 [Azure Python SDK][Azure Python package] 套件來執行下列動作： 

- 在主題中建立主題和訂用帳戶
- 建立訂用帳戶篩選條件和規則
- 將訊息傳送至主題 
- 接收來自訂用帳戶的訊息
- 刪除主題和訂用帳戶

## <a name="prerequisites"></a>Prerequisites
- Azure 訂用帳戶。 您可以啟用自己的 [Visual Studio 或 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 服務匯流排命名空間，遵循[快速入門：使用 Azure 入口網站建立服務匯流排主題和訂用帳戶](service-bus-quickstart-topics-subscriptions-portal.md)中的步驟加以建立。 從 [共用存取原則]  畫面複製命名空間名稱、共用存取金鑰名稱和主要金鑰值，以便稍後在本快速入門中使用。 
- Python 3.4x 或更新版本，已安裝 [Python SDK][Azure Python package] 套件。 如需詳細資訊，請參閱 [Python 安裝指南](/azure/developer/python/azure-sdk-install)。

## <a name="create-a-servicebusservice-object"></a>建立 ServiceBusService 物件

**ServiceBusService** 物件可讓您使用主題及其訂用帳戶。 若要以程式設計方式存取服務匯流排，請在 Python 檔案的頂端附近新增下列程式碼行：

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

新增下列程式碼以建立 **ServiceBusService** 物件。 請使用您的服務匯流排命名空間名稱、共用存取簽章 (SAS) 金鑰名稱和主要金鑰值來取代 `<namespace>`、`<sharedaccesskeyname>` 和 `<sharedaccesskeyvalue>`。 您可以在 [Azure 入口網站][Azure portal]中，於服務匯流排命名空間的 [共用存取原則]  之下找到這些值。

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>建立主題

下列程式碼會使用 `create_topic` 方法，以預設設定建立名為 `mytopic` 的服務匯流排主題：

```python
bus_service.create_topic('mytopic')
```

您可以使用主題選項來覆寫預設主題設定，例如訊息存留時間 (TTL) 或主題大小上限。 下列範例會建立名為 `mytopic` 的主題，其主題大小上限為 5 GB 且預設訊息 TTL 為一分鐘：

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>建立訂用帳戶

您也可以使用 **ServiceBusService** 物件來建立主題的訂用帳戶。 訂用帳戶可使用篩選條件來限制傳遞至其虛擬佇列的訊息集。 如果您未指定篩選條件，新的訂用帳戶會使用預設 **MatchAll** 篩選條件，其會將所有發佈至主題的訊息放入訂用帳戶的虛擬佇列中。 下列範例會對名為 `AllMessages` 的 `mytopic` 建立訂用帳戶，其使用 **MatchAll** 篩選條件：

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>將篩選條件使用於訂用帳戶

使用 **ServiceBusService** 物件的 `create_rule` 方法，篩選出現在訂用帳戶中的訊息。 您可以在建立訂用帳戶時指定規則，或將規則新增至現有的訂用帳戶。

最具彈性的篩選條件類型是 **SqlFilter**，其使用 SQL-92 的子集。 SQL 篩選條件會根據發佈至主題的訊息屬性運作。 如需可與 SQL 篩選條件搭配使用的運算式詳細資訊，請參閱 [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 語法。

由於 **MatchAll** 預設篩選條件會自動套用至所有新的訂用帳戶，因此您必須先從您要篩選的訂用帳戶中移除該篩選條件，否則 **MatchAll** 會覆寫您指定的任何其他篩選條件。 您可以使用 **ServiceBusService** 物件的 `delete_rule` 方法移除預設規則。

下列範例會對名為 `HighMessages` 的 `mytopic` 建立訂用帳戶，其使用名為 `HighMessageFilter` 的 **SqlFilter** 規則。 `HighMessageFilter` 規則只會選取自訂 `messageposition` 屬性大於 3 的訊息：

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

下列範例會對名為 `LowMessages` 的 `mytopic` 建立訂用帳戶，其使用名為 `LowMessageFilter` 的 **SqlFilter** 規則。 `LowMessageFilter` 規則只會選取 `messageposition` 屬性小於或等於 3 的訊息：

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

`AllMessages`、`HighMessages` 和 `LowMessages` 全部生效後，傳送至 `mytopic` 的訊息一律會傳遞給 `AllMessages` 訂用帳戶的接收者。 視訊息的 `messageposition` 屬性值而定，訊息也可選擇性地傳遞至 `HighMessages` 或 `LowMessages` 訂用帳戶。 

## <a name="send-messages-to-a-topic"></a>傳送訊息至主題

應用程式會使用 **ServiceBusService** 物件的 `send_topic_message` 方法，將訊息傳送至服務匯流排主題。

下列範例會將五則測試訊息傳送至 `mytopic` 主題。 自訂 `messageposition` 屬性值取決於迴圈的反覆運算，可決定哪些訂用帳戶會接收訊息。 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>訊息大小限制和配額

服務匯流排主題支援的訊息大小上限：在[標準層](service-bus-premium-messaging.md)中為 256 KB 以及在[進階層](service-bus-premium-messaging.md)中為 1 MB。 標頭 (包含標準和自訂應用程式屬性) 可以容納 64 KB 的大小上限。 主題可保存的訊息數目沒有限制，但主題所保存的總訊息大小有最高限制。 您可以在建立主題時定義其大小，其上限為 5 GB。 

如需有關配額的詳細資訊，請參閱 [服務匯流排配額][Service Bus quotas]。

## <a name="receive-messages-from-a-subscription"></a>自訂用帳戶接收訊息

應用程式會在 **ServiceBusService** 物件上使用 `receive_subscription_message` 方法，以接收來自訂用帳戶的訊息。 下列範例會從 `LowMessages` 的訂用帳戶接收訊息，並在讀取後將其刪除：

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

`receive_subscription_message` 的選擇性 `peek_lock` 參數會決定在讀取訊息後，服務匯流排是否會從訂用帳戶中刪除訊息。 預設的訊息接收模式為 *PeekLock*，或設為 **True** 的 `peek_lock`，其會讀取 (預覽) 並鎖定訊息，而不需從訂用帳戶中刪除訊息。 接著，必須明確地完成每則訊息，才能將它從訂用帳戶中移除。

若要在讀取訊息後從訂用帳戶中刪除訊息，您可以將 `peek_lock` 參數設定為 **False**，如上述範例所示。 在接收作業中刪除訊息是最簡單的模型，且應用程式可在發生失敗時容忍遺失訊息，才能運作良好。 若要了解此行為，請考慮應用程式發出接收要求，接著在處理此要求之前當機的案例。 如果訊息在收到時遭到刪除，當應用程式重新啟動並再度開始取用訊息時，其會在當機前遺失所收到的訊息。

如果應用程式無法容忍遺失訊息，則接收會成為兩階段的作業。 PeekLock 會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回到應用程式。 在處理或儲存此訊息之後，應用程式會在 **Message** 物件上呼叫 `complete` 方法，以完成接收程序的第二個階段。  `complete` 方法會將訊息標示為已取用，並將其從訂用帳戶中移除。

下列範例示範 peek lock 案例：

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式因為某些原因而無法處理訊息，其可在 **Message** 物件上呼叫 `unlock` 方法。 服務匯流排會將訂用帳戶中的訊息解除鎖定，讓此訊息可由相同或其他取用應用程式重新接收。

訂用帳戶內鎖定的訊息也會逾時。 如果應用程式無法在鎖定逾時到期前處理訊息 (例如，若應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓系統可以重新接收訊息。

如果應用程式在處理訊息後但呼叫 `complete` 方法前當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。 這種行為通常稱為「至少處理一次」  。 每則訊息至少會處理一次；但在特定狀況下，可能會重新傳遞相同訊息。 如果您的案例無法容許重複處理，您可以使用訊息的 **MessageId** 屬性，其會在各傳遞嘗試中保持不變，以處理重複的訊息傳遞。 

## <a name="delete-topics-and-subscriptions"></a>刪除主題和訂用帳戶

若要刪除主題和訂用帳戶，請使用 [Azure 入口網站][Azure portal]或 `delete_topic` 方法。 下列程式碼會刪除名為 `mytopic` 的主題：

```python
bus_service.delete_topic('mytopic')
```

刪除主題同時會刪除主題的所有訂用帳戶。 您也可以單獨刪除訂用帳戶。 下列程式碼會從 `mytopic` 主題刪除名為 `HighMessages` 的訂用帳戶：

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

根據預設，主題和訂用帳戶會持續存在，直到您將其刪除為止。 若要在經過特定一段時間之後自動刪除訂用帳戶，您可以在訂用帳戶上設定 [auto_delete_on_idle](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) 參數。 

> [!TIP]
> 您可以使用[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer/)來管理服務匯流排資源。 服務匯流排總管可讓您連線到服務匯流排命名空間，並輕鬆地管理傳訊實體。 此工具提供進階的功能 (例如匯入/匯出功能)，以及測試主題、佇列、訂用帳戶、轉送服務、通知中樞和事件中樞的能力。 

## <a name="next-steps"></a>後續步驟

了解基本的服務匯流排主題之後，請參考下列連結以取得更多資訊：

* [佇列、主題和訂用帳戶][Queues, topics, and subscriptions]
* [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 參考

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md