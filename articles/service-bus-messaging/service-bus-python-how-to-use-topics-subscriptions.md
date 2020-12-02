---
title: 使用 Azure 服務匯流排主題和訂用帳戶搭配 Python azure-servicebus 套件版本 7.0.0
description: 本文示範如何使用 Python 將訊息傳送至主題，並從訂用帳戶接收訊息。
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: 43f633e427e20cfb0b044bd42b77f866e4cc0c61
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489406"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>將訊息傳送到 Azure 服務匯流排主題，並從訂用帳戶接收來自主題的訊息 (Python)
本文示範如何使用 Python 將訊息傳送至服務匯流排主題，並從訂用帳戶接收來自主題的訊息。 

## <a name="prerequisites"></a>先決條件
- Azure 訂用帳戶。 您可以啟用自己的 [Visual Studio 或 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 依照下列快速入門中的步驟操作：[快速入門：使用 Azure 入口網站建立服務匯流排主題和主題的訂用帳戶](service-bus-quickstart-topics-subscriptions-portal.md)。 記下連接字串、主題名稱和訂用帳戶名稱。 在本快速入門中，您只會使用一個訂用帳戶。 
- 已安裝 Python 2.7 或更高版本，以及 [Azure Python SDK][Azure Python package] 套件。 如需詳細資訊，請參閱 [Python 安裝指南](/azure/developer/python/azure-sdk-install)。

## <a name="send-messages-to-a-topic"></a>傳送訊息至主題

1. 新增下列 Import 陳述式。 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. 新增下列常數。 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - 將 `<NAMESPACE CONNECTION STRING>` 取代為命名空間的連接字串。
    > - 將 `<TOPIC NAME>` 取代為主題名稱。
    > - 將 `<SUBSCRIPTION NAME>` 取代為主題訂用帳戶的名稱 。 
3. 新增方法來傳送單一訊息。

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    傳送者是物件，可作為您所建立之主題的用戶端。 您稍後會建立此項目，並以引數的形式傳送至此函式。 
4. 新增方法來傳送訊息清單。

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
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
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. 建立服務匯流排用戶端，然後建立傳送訊息的主題傳送者物件。

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
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
 
## <a name="receive-messages-from-a-subscription"></a>自訂用帳戶接收訊息
在列印陳述式之後新增下列程式碼。 此程式碼會持續收到新訊息，直到 5 (`max_wait_time`) 秒未收到任何新訊息為止。 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>完整程式碼

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

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
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
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

選取下方窗格中的主題，以查看主題的 **服務匯流排主題** 頁面。 在此頁面上，您應該會在 **訊息** 圖表中看到三個傳入和三個傳出訊息。 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="傳入和傳出訊息":::

如果您在此頁面上選取訂用帳戶，您會進入 **服務匯流排訂用帳戶** 頁面。 您可以在此頁面上看到作用中的訊息計數、寄不出的信件訊息計數等等。 在此範例中已收到所有的訊息，因此作用中的訊息計數為零。 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="作用中訊息計數":::

如果您註解接收程式碼為，將會看到作用中的訊息計數為 16。 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="作用中訊息計數 - 未接收":::

## <a name="next-steps"></a>後續步驟
請參閱下列文件和範例： 

- [適用於 Python 的 Azure 服務匯流排用戶端程式庫](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples)。 
    - **sync_samples** 資料夾中有範例，示範如何以同步方式與服務匯流排互動。 在本快速入門中，您使用了這個方法。 
    - **sync_samples** 資料夾中有範例，示範如何以同步方式與服務匯流排互動。 
- [ 參考文件](/python/api/azure-servicebus/azure.servicebus?preserve-view=true&view=azure-python-preview)