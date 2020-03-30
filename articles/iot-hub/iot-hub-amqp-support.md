---
title: 瞭解 Azure IoT 中心 AMQP 支援 |微軟文檔
description: 開發人員指南 - 支援使用 AMQP 協定連接到面向 IoT 中心的設備端點和麵向服務的終結點。 包括有關 Azure IoT 設備 SDK 中內置 AMQP 支援的資訊。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: 7f7e957502419b766f7da63048e8168192ea20da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284781"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>使用 AMQP 協定與 IoT 中心通信

Azure IoT 中心支援[OASIS 高級訊息佇列協定 （AMQP） 版本 1.0，](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)通過面向設備和麵向服務的終結點提供各種功能。 本文檔介紹使用 AMQP 用戶端連接到 IoT 中心以使用 IoT 中心功能。

## <a name="service-client"></a>服務用戶端

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>連接到 IoT 中心（服務用戶端）並進行身份驗證

要使用 AMQP 連接到 IoT 中心，用戶端可以使用[基於聲明的安全 （CBS）](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc)或[簡單的身份驗證和安全層 （SASL） 身份驗證](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)。

服務用戶端需要以下資訊：

| 資訊 | 值 |
|-------------|--------------|
| IoT 中心主機名稱 | `<iot-hub-name>.azure-devices.net` |
| 機碼名稱 | `service` |
| 存取金鑰 | 與服務關聯的主金鑰或輔助金鑰 |
| 共用存取簽章 | 以以下格式進行的短期共用訪問簽名： `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. 要獲取生成此簽名的代碼，請參閱對[IoT 中心的控制項訪問](./iot-hub-devguide-security.md#security-token-structure)。

以下程式碼片段使用[Python 中的 uAMQP 庫](https://github.com/Azure/azure-uamqp-python)通過寄件者連結連接到 IoT 中心。

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>調用雲到設備消息（服務用戶端）

要瞭解服務與 IoT 中心之間以及設備和 IoT 中心之間的雲到設備消息交換，請參閱[從 IoT 中心發送雲到設備的消息](iot-hub-devguide-messages-c2d.md)。 服務用戶端使用兩個連結發送消息和接收以前從設備發送的消息的回饋，如下表所述：

| 建立者 | 連結類型 | 連結路徑 | 描述 |
|------------|-----------|-----------|-------------|
| 服務 | 寄件者連結 | `/messages/devicebound` | 發送到設備的雲到設備消息由服務發送到此連結。 通過此連結發送的消息將其`To`屬性設置為目標設備的接收方連結路徑 。 `/devices/<deviceID>/messages/devicebound` |
| 服務 | 接收器連結 | `/messages/serviceBound/feedback` | 來自此連結上收到的設備（按服務接收的）的完成、拒絕和放棄回饋消息。 有關回饋消息的詳細資訊，請參閱從[IoT 中心發送雲到設備的消息](./iot-hub-devguide-messages-c2d.md#message-feedback)。 |

以下程式碼片段演示如何創建雲到設備的消息，以及如何使用[Python 中的 uAMQP 庫](https://github.com/Azure/azure-uamqp-python)將其發送到設備。

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

要接收回饋，服務用戶端將創建一個接收方連結。 以下程式碼片段演示如何使用[Python 中的 uAMQP 庫](https://github.com/Azure/azure-uamqp-python)創建連結：

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

如前面的代碼所示，雲到設備的回饋消息具有*應用程式/vnd.microsoft.iothub.feedback.json*的內容類型。 您可以使用郵件的 JSON 正文中的屬性來推斷原始郵件的傳遞狀態：

* 回饋`statusCode`正文中的鍵具有以下值之一：*成功*、*過期*、*交貨計數超出*、*已拒絕*或*清除*。

* 回饋`deviceId`正文中的鍵具有目標設備的 ID。

* 回饋`originalMessageId`正文中的鍵具有服務發送的原始雲到設備消息的 ID。 您可以使用此傳遞狀態將回饋與雲到設備消息相關聯。

### <a name="receive-telemetry-messages-service-client"></a>接收遙測消息（服務用戶端）

預設情況下，IoT 中心將引入的設備遙測消息存儲在內置事件中心中。 您的服務用戶端可以使用 AMQP 協定接收存儲的事件。

為此，服務用戶端首先需要連接到 IoT 中心終結點，並接收重定向位址到內置事件中心。 然後，服務用戶端使用提供的位址連接到內置事件中心。

在每個步驟中，用戶端都需要提供以下資訊：

* 有效的服務憑據（服務共用訪問簽名權杖）。

* 一個格式良好的消費者組分區的路徑，它打算從中檢索消息。 對於給定消費者組和分區 ID，路徑具有以下格式：（`/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>`預設消費者組為`$Default`）。

* 用於指定分區中起始點的可選篩選謂詞。 此謂詞可以是序號、偏移量或排隊時間戳記的形式。

以下程式碼片段使用[Python 中的 uAMQP 庫](https://github.com/Azure/azure-uamqp-python)來演示以下步驟：

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

對於給定的裝置識別碼，IoT 中心使用裝置識別碼 的雜湊來確定要將其消息存儲在哪個分區。 前面的程式碼片段演示如何從單個此類分區接收事件。 但是，請注意，典型的應用程式通常需要檢索存儲在所有事件中心分區中的事件。

## <a name="device-client"></a>設備用戶端

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>連接到 IoT 中心（設備用戶端）並進行身份驗證

要使用 AMQP 連接到 IoT 中心，設備可以使用[基於聲明的安全 （CBS）](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc)或[簡單的身份驗證和安全層 （SASL）](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)身份驗證。

設備用戶端需要以下資訊：

| 資訊 | 值 |
|-------------|--------------|
| IoT 中心主機名稱 | `<iot-hub-name>.azure-devices.net` |
| 存取金鑰 | 與設備關聯的主金鑰或輔助金鑰 |
| 共用存取簽章 | 以以下格式進行的短期共用訪問簽名： `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. 要獲取生成此簽名的代碼，請參閱對[IoT 中心的控制項訪問](./iot-hub-devguide-security.md#security-token-structure)。

以下程式碼片段使用[Python 中的 uAMQP 庫](https://github.com/Azure/azure-uamqp-python)通過寄件者連結連接到 IoT 中心。

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

以下連結路徑支援為設備操作：

| 建立者 | 連結類型 | 連結路徑 | 描述 |
|------------|-----------|-----------|-------------|
| 裝置 | 接收器連結 | `/devices/<deviceID>/messages/devicebound` | 每個目標設備都會在此鏈路上接收發送到設備的雲到設備消息。 |
| 裝置 | 寄件者連結 | `/devices/<deviceID>/messages/events` | 從設備發送的設備到雲的消息通過此連結發送。 |
| 裝置 | 寄件者連結 | `/messages/serviceBound/feedback` | 設備通過此連結發送到服務的雲到設備消息回饋。 |

### <a name="receive-cloud-to-device-commands-device-client"></a>接收雲到設備命令（設備用戶端）

發送到設備的雲到設備命令到達鏈路`/devices/<deviceID>/messages/devicebound`上。 設備可以分批接收這些消息，並根據需要在消息中使用消息資料負載、消息屬性、注釋或應用程式屬性。

以下程式碼片段使用[Python 中的 uAMQP 庫](https://github.com/Azure/azure-uamqp-python)通過設備接收雲到設備消息。

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>發送遙測消息（設備用戶端）

您還可以使用 AMQP 從設備發送遙測消息。 設備可以選擇提供應用程式屬性的字典或各種消息屬性，如消息 ID。

以下程式碼片段使用[Python 中的 uAMQP 庫](https://github.com/Azure/azure-uamqp-python)從設備發送設備到雲的消息。

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>其他注意事項

* 由於網路故障或身份驗證權杖過期（在代碼中生成），AMQP 連接可能會中斷。 服務用戶端必須處理這些情況，並根據需要重新建立連接和連結。 如果身份驗證權杖過期，用戶端可以通過在權杖過期之前主動續訂權杖來避免連接中斷。

* 您的客戶必須偶爾能夠正確處理鏈路重定向。 要瞭解此類操作，請參閱 AMQP 用戶端文檔。

## <a name="next-steps"></a>後續步驟

要瞭解有關 AMQP 協定的更多，請參閱[AMQP v1.0 規範](https://www.amqp.org/sites/amqp.org/files/amqp.pdf)。

要瞭解有關 IoT 中心消息傳遞的更多內容，請參閱：

* [雲端到裝置的訊息](./iot-hub-devguide-messages-c2d.md)
* [支援附加協定](iot-hub-protocol-gateway.md)
* [支援訊息佇列遙測傳輸 （MQTT） 協定](./iot-hub-mqtt-support.md)
