---
title: 使用 Azure 服務匯流排佇列搭配 Python azure-servicebus 套件版本 7.0.0
description: 本文示範如何使用 Python 傳送及接收 Azure 服務匯流排中的訊息。
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 7275e33e44c20ece6eb7d620e2c1e8032be41a7b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498654"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>在 Azure 服務匯流排佇列 (Python) 中傳送和接收訊息
本文示範如何使用 Python 傳送及接收 Azure 服務匯流排中的訊息。 

## <a name="prerequisites"></a>先決條件
- Azure 訂用帳戶。 您可以啟用自己的 [Visual Studio 或 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果您沒有可用的佇列，請執行[使用 Azure 入口網站建立服務匯流排佇列](service-bus-quickstart-portal.md)一文中的步驟，以建立佇列。 請記下服務匯流排命名空間的 **連接字串**，以及您所建立的 **佇列** 名稱。
- Python 2.7 或更高版本，已安裝 [Python Azure 服務匯流排](https://pypi.python.org/pypi/azure-servicebus)套件。 如需詳細資訊，請參閱 [Python 安裝指南](/azure/developer/python/azure-sdk-install)。 

## <a name="send-messages-to-a-queue"></a>傳送訊息至佇列

1. 新增下列 Import 陳述式。 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. 新增下列常數。 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - 將 `<NAMESPACE CONNECTION STRING>` 取代為服務匯流排命名空間的連接字串。
    > - 將 `<QUEUE NAME>` 取代為佇列名稱。 
3. 新增方法來傳送單一訊息。

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    傳送者是物件，可作為您所建立之佇列的用戶端。 您稍後會建立此項目，並以引數的形式傳送至此函式。 
4. 新增方法來傳送訊息清單。

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. 新增方法來傳送訊息批次。

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. 建立服務匯流排用戶端，然後建立傳送訊息的佇列傳送者物件。

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-queue"></a>從佇列接收訊息
在列印陳述式之後新增下列程式碼。 此程式碼會持續收到新訊息，直到 5 (`max_wait_time`) 秒未收到任何新訊息為止。 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>完整程式碼

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>執行應用程式
執行應用程式時會看到下列輸出： 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

瀏覽至 Azure 入口網站中您的服務匯流排命名空間。 在 **概觀** 頁面上，確認 **傳入** 和 **傳出** 訊息計數為 16。 如果未看到此計數，請等候幾分鐘後重新整理頁面。 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="傳入和傳出訊息計數":::

選取此 **概觀** 頁面上的佇列，瀏覽至 **服務匯流排佇列** 頁面。 您也會在此頁面上看到 **傳入** 和 **傳出** 訊息計數。 您也會看到其他資訊，例如佇列的 **目前大小** 和 **作用中訊息計數** 等等。 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="佇列詳細資料":::


## <a name="next-steps"></a>後續步驟
請參閱下列文件和範例： 

- [適用於 Python 的 Azure 服務匯流排用戶端程式庫](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples)。 
    - **sync_samples** 資料夾中有範例，示範如何以同步方式與服務匯流排互動。 在本快速入門中，您使用了這個方法。 
    - **sync_samples** 資料夾中有範例，示範如何以同步方式與服務匯流排互動。 
- [ 參考文件](/python/api/azure-servicebus/azure.servicebus?preserve-view=true&view=azure-python-preview)