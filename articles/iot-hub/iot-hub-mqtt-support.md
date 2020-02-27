---
title: 了解 Azure IoT 中樞 MQTT 支援 | Microsoft Docs
description: 開發人員指南 - 支援裝置使用 MQTT 通訊協定連接至 IoT 中樞對向端點。 包含 Azure IoT 裝置 SDK 內建的 MQTT 支援的相關資訊。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 2b200692610302bb135982e5419dcda36d5cfe60
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648490"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>使用 MQTT 通訊協定來與 IoT 中樞通訊

IoT 中樞可使用下列項目讓裝置與 IoT 中樞裝置端點進行通訊：

* 埠8883上的[MQTT v 3.1.1](https://mqtt.org/)
* 連接埠 443 上使用 WebSocket 的 MQTT v3.1.1。

IoT 中樞不是功能完整的 MQTT 訊息代理程式，而且不支援 MQTT v3.1.1 標準中所指定的所有行為。 本文說明裝置如何使用受支援的 MQTT 行為來與 IoT 中樞通訊。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

與 IoT 中樞通訊的所有裝置皆必須使用 TLS/SSL 加以保護。 因此，IoT 中樞不支援透過連接埠 1883 的不安全連線。

## <a name="connecting-to-iot-hub"></a>連接到 IoT 中樞

裝置可以使用 MQTT 通訊協定，使用下列任何選項來連接到 IoT 中樞。

* [Azure IoT sdk](https://github.com/Azure/azure-iot-sdks)中的程式庫。
* 直接 MQTT 通訊協定。

在許多公司和教育網路環境中，會封鎖 MQTT 埠（8883）。 如果您無法在防火牆中開啟埠8883，建議您透過 Web 通訊端使用 MQTT。 透過 Web 通訊端的 MQTT 會透過埠443進行通訊，這幾乎一律會在網路環境中開啟。 若要瞭解如何在使用 Azure IoT Sdk 時，透過 Web 通訊端通訊協定指定 MQTT 和 MQTT，請參閱[使用裝置 sdk](#using-the-device-sdks)。

## <a name="using-the-device-sdks"></a>使用裝置 SDK

支援 MQTT 通訊協定的[裝置 sdk](https://github.com/Azure/azure-iot-sdks)適用于 JAVA、Node.js、C、 C#和 Python。 裝置 SDK 會使用標準的 IoT 中樞連接字串來連接到 IoT 中樞。 若要使用 MQTT 通訊協定，用戶端通訊協定參數必須設定為 **MQTT**。 您也可以在用戶端通訊協定參數中，透過 Web 通訊端指定 MQTT。 根據預設，裝置 SDK 會連接到 **CleanSession** 旗標設為 **0** 的 IoT 中樞，並使用 **QoS 1** 來與 IoT 中樞交換訊息。

當裝置連線到 IoT 中樞時，裝置 SDK 會提供方法讓裝置使用 IoT 中樞交換訊息。

下表包含每個支援語言之程式碼範例的連結，並指定使用 MQTT 或 MQTT over Web 通訊端通訊協定，來建立 IoT 中樞連接的參數。

| Language | MQTT 通訊協定參數 | 透過 Web 通訊端通訊協定參數的 MQTT
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-裝置-mqtt。Mqtt | azure-iot-裝置-mqtt。MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable)。MQTT | IotHubClientProtocol。 MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet)。Mqtt | TransportType，Mqtt 會在 MQTT 失敗時，透過 Web 通訊端切換回 MQTT。 若只要透過 Web 通訊端指定 MQTT，請使用 TransportType。 Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | 預設支援 MQTT | 在建立用戶端的呼叫中新增 `websockets=True` |

下列片段示範如何在使用 Azure IoT node.js SDK 時，透過 Web 通訊端通訊協定指定 MQTT：

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

下列片段示範如何在使用 Azure IoT Python SDK 時，透過 Web 通訊端通訊協定指定 MQTT：

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>預設 keep-alive timeout

為了確保用戶端/IoT 中樞連線保持運作，服務和用戶端會定期傳送*keep-alive ping。* 使用 IoT SDK 的用戶端會在下表所定義的間隔傳送 keep-alive：

|Language  |預設保持連線間隔  |可設定  |
|---------|---------|---------|
|Node.js     |   180秒      |     否    |
|Java     |    230秒     |     否    |
|C     | 240秒 |  [是](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 秒 |  [是](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python （V2）   | 60 秒 |  否   |

遵循[MQTT 規格](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)，IoT 中樞的保持運作 ping 間隔是用戶端保持運作的值1.5 倍。 不過，IoT 中樞會將伺服器端的最大超時時間限制為29.45 分鐘（1767秒），因為所有 Azure 服務都會系結至 Azure 負載平衡器 TCP 閒置超時，這是29.45 分鐘。 

例如，使用 JAVA SDK 的裝置會傳送 keep-alive ping，然後中斷網路連線。 230秒之後，裝置會遺漏 keep-alive ping，因為它已離線。 不過，IoT 中樞不會立即關閉連線，它會等待另一個 `(230 * 1.5) - 230 = 115` 秒，再中斷裝置與錯誤[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)的連接。 

您可以設定的最大用戶端 keep-alive 值是 `1767 / 1.5 = 1177` 秒數。 任何流量都會重設 keep-alive。 例如，成功的 SAS 權杖重新整理會重設 keep-alive。

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>將裝置應用程式從 AMQP 移轉至 MQTT

如果您使用[裝置 sdk](https://github.com/Azure/azure-iot-sdks)，從使用 AMQP 切換至 MQTT 時，必須如先前所述，在用戶端初始化中變更通訊協定參數。

這麼做時，請務必檢查下列項目︰

* AMQP 在許多情況下會傳回錯誤，而 MQTT 會終止連線。 因此，可能需要稍微變更您的例外狀況處理邏輯。

* MQTT 不支援接收[雲端到裝置訊息](iot-hub-devguide-messaging.md)時的*拒絕*作業。 如果您的後端應用程式需要接收來自裝置應用程式的回應，請考慮使用[直接方法](iot-hub-devguide-direct-methods.md)。

* Python SDK 不支援 AMQP

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>直接使用 MQTT 通訊協定 (作為裝置)

如果裝置無法使用裝置 SDK，它仍可使用連接埠 8883 上的 MQTT 通訊協定連線到公用裝置端點。 在**CONNECT**封包中，裝置應使用下列值：

* 在 [ClientId] 欄位中，使用 **deviceId**。

* 在 [Username] 欄位中，使用 `{iothubhostname}/{device_id}/?api-version=2018-06-30`，其中 `{iothubhostname}` 是 IoT 中樞的完整 CName。

    例如，如果您的 IoT 中樞名稱是 **contoso.azure-devices.net** ，而且如果您的裝置名稱是 **MyDevice01**，則完整的 [Username] 欄位應包含：

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* 在 [Password] 欄位中，使用 SAS 權杖。 SAS 權杖的格式與 HTTPS 和 AMQP 通訊協定的格式相同：

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > 如果您使用 X.509 憑證驗證，則不需要 SAS 權杖密碼。 如需詳細資訊，請參閱[在 Azure IoT 中樞中設定 x.509 安全性](iot-hub-security-x509-get-started.md)，並遵循[下面](#tlsssl-configuration)的程式碼指示。

  如需如何產生 SAS 權杖的詳細資訊，請參閱[使用 IoT 中樞安全性權杖](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)的裝置一節。

  測試時，您也可以使用跨平臺[Azure IoT Tools Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)或[Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)工具來快速產生 SAS 權杖，讓您可以複製並貼到您自己的程式碼中：

### <a name="for-azure-iot-tools"></a>針對 Azure IoT Tools

1. 展開 Visual Studio Code 左下角的 [AZURE IOT 中樞裝置] 索引標籤。
  
2. 以滑鼠右鍵按一下您的裝置，然後選取 [產生裝置的 SAS 權杖]。
  
3. 設定 [到期時間]，然後按 'Enter' 鍵。
  
4. SAS 權杖已建立並複製到剪貼簿。

### <a name="for-device-explorer"></a>針對 Device Explorer

1. 移至 [裝置總管] 中的 [管理] 索引標籤。

2. 按一下 [SAS 權杖] \(右上角)。

3. 在 [SASTokenForm] 的 [DeviceID] 下拉式清單中，選取您的裝置。 設定您的 **TTL**。

4. 按一下 [產生] 來建立您的權杖。

   產生的 SAS 權杖具有下列結構：

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   使用 MQTT 連線時，此權杖中作為 [Password] 欄位的部分是︰

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

對於 MQTT 的連接和中斷連接封包，IoT 中樞會對 **作業監視** 通道發出事件。 此事件具有其他資訊，可協助您對連線問題進行疑難排解。

裝置應用程式可以在 **CONNECT** 封包中指定 **Will** 訊息。 裝置應用程式應該使用 `devices/{device_id}/messages/events/` 或 `devices/{device_id}/messages/events/{property_bag}` 作為 **Will** 主題名稱，以定義要當作遙測訊息轉送的 **Will** 訊息。 在此情況下，如果網路連線已關閉，但先前並未接收到來自裝置的 **DISCONNECT** 封包，則 IoT 中樞會將 **CONNECT** 封包中提供的 **Will** 訊息傳送到遙測通道。 遙測通道可以是預設的**事件**端點，或是 IoT 中樞路由所定義的自訂端點。 訊息具有 **iothub-MessageType** 屬性，且已為它指派 **Will** 值。

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>使用 MQTT 而不搭配 Azure IoT C SDK 的 C 程式碼範例
在此存放[庫](https://github.com/Azure-Samples/IoTMQTTSample)中，您會發現幾個 CC++ /示範專案，其中顯示如何傳送遙測訊息、在不使用 Azure iot C SDK 的情況下接收 IoT 中樞的事件。 

這些範例會使用 Eclipse Mosquitto 程式庫，將訊息傳送至 IoT 中樞內所實作為 MQTT 代理人。

此存放庫包含：

**若為 Windows：**

* TelemetryMQTTWin32：包含程式碼，可將遙測訊息傳送至 Azure IoT 中樞，並在 Windows 電腦上建立和執行。

* SubscribeMQTTWin32：包含用來訂閱 Windows 電腦上特定 IoT 中樞之事件的程式碼。

* DeviceTwinMQTTWin32：包含用來查詢和訂閱 Windows 電腦上 Azure IoT 中樞裝置之裝置對應項事件的程式碼。

* PnPMQTTWin32：包含 & 用來將遙測訊息傳送至 Azure IoT 中樞的程式碼，並在 Windows 電腦上建立並執行該功能。 深入瞭解 IoT 外掛程式 & 在[此](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)播放

**若為 Linux：**

* MQTTLinux：包含要在 Linux 上執行的程式碼和組建腳本（WSL、Ubuntu 和 Raspbian 已于目前為止進行過測試）。

* LinuxConsoleVS2019：包含相同的程式碼，但位於以 WSL 為目標的 VS2019 專案中（Windows Linux 子系統系統）。 此專案可讓您從 Visual Studio，在 Linux 上逐步調試執行的程式碼。

**針對 mosquitto_pub：**

此資料夾包含兩個範例命令，用於 Mosquitto.org 所提供的 mosquitto_pub 公用程式工具。

* Mosquitto_sendmessage：將簡單的文字訊息傳送至作為裝置的 Azure IoT 中樞。

* Mosquitto_subscribe：查看 Azure IoT 中樞發生的事件。

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>直接使用 MQTT 通訊協定 (作為模組)

使用模組身分識別透過 MQTT 連線至 IoT 中樞的方式與裝置類似 (如[上述](#using-the-mqtt-protocol-directly-as-a-device))，但您需要使用下列命令：

* 將 [用戶端識別碼] 設定為 `{device_id}/{module_id}`。

* 如果以使用者名稱和密碼來進行驗證，請將使用者名稱設定為 `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30`，並使用與模組身分識別相關聯的 SAS 權杖來作為密碼。

* 使用 `devices/{device_id}/modules/{module_id}/messages/events/` 作為用來發佈遙測的主題。

* 使用 `devices/{device_id}/modules/{module_id}/messages/events/` 作為 WILL 主題。

* GET 和 PATCH 這對主題在模組和裝置中都一樣。

* 這對狀態主題在模組和裝置中都一樣。

## <a name="tlsssl-configuration"></a>TLS/SSL 組態

若要直接使用 MQTT 通訊協定，您的用戶端「必須」透過 TLS/SSL 進行連線。 嘗試略過此步驟會因連線錯誤而發生失敗。

為了建立 TLS 連線，您可能必須下載並參考「DigiCert Baltimore 根憑證」。 此憑證是 Azure 用來保護連線的唯一憑證。 您可以在[Azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c)存放庫中找到此憑證。 如需這些憑證的詳細資訊，請參閱[Digicert 網站](https://www.digicert.com/digicert-root-certificates.htm)。

如需如何使用[PAHO MQTT 程式庫](https://pypi.python.org/pypi/paho-mqtt)Python 版本來執行此程式的範例，請參閱 Eclipse Foundation，如下所示。

首先，從您的命令列環境安裝 Paho 程式庫：

```cmd/sh
pip install paho-mqtt
```

接著，以 Python 指令碼實作用戶端。 取代下列各項的預留位置：

* `<local path to digicert.cer>` 是包含 DigiCert Baltimore 根憑證的本機檔案路徑。 您可以在「適用 C 的 Azure IoT SDK」中，從 [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) 複製憑證資訊來建立此檔案。包含 `-----BEGIN CERTIFICATE-----` 和 `-----END CERTIFICATE-----` 這兩行、移除每一行開頭和結尾的 `"` 標記，以及移除每一行結尾的 `\r\n` 字元。

* `<device id from device registry>` 是您新增至 IoT 中樞的裝置識別碼。

* `<generated SAS token>` 是所建立裝置的 SAS 權杖，如本文前面所述。

* `<iot hub name>` 是 IoT 中樞的名稱。

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

若要使用裝置憑證進行驗證，請使用下列變更來更新上述程式碼片段（請參閱如何針對以憑證為基礎的驗證進行準備的[X.509 CA 憑證](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate)）：

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>傳送裝置到雲端訊息

成功連線之後，裝置可以使用 `devices/{device_id}/messages/events/` 或 `devices/{device_id}/messages/events/{property_bag}` 作為**主題名稱**，將訊息傳送至 IoT 中樞。 `{property_bag}` 項目可讓裝置以 URL 編碼格式傳送具有其他屬性的訊息。 例如：

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> 這個 `{property_bag}` 元素會使用與 HTTPS 通訊協定中的查詢字串相同的編碼方式。

以下為 IoT 中樞實作特有的行為清單：

* 「IoT 中樞」不支援 QoS 2 訊息。 如果裝置應用程式發佈 **QoS 2** 的訊息，IoT 中樞會關閉網路連接。

* 「IoT 中樞」不會保存「保留」訊息。 如果裝置傳送 **RETAIN** 旗標設定為 1 的訊息，「IoT 中樞」會在訊息中加入 **x-opt-retain** 應用程式屬性。 在此情況下，「IoT 中樞」不會保存保留訊息，而是會傳遞給後端應用程式。

* IoT 中樞僅支援每個裝置有一個作用中 MQTT 連接。 代表相同裝置識別碼的任何新的 MQTT 連接都會導致 IoT 中樞卸除現有的連接。

如需詳細資訊，請參閱[訊息開發人員指南](iot-hub-devguide-messaging.md)。

## <a name="receiving-cloud-to-device-messages"></a>接收雲端到裝置訊息

若要從 IoT 中樞接收訊息，裝置應該使用 `devices/{device_id}/messages/devicebound/#` 做為**主題篩選**來進行訂閱。 「主題篩選」中的多層級萬用字元 `#` 僅供用來允許裝置接收主題名稱中的額外屬性。 IoT 中樞不允許使用 `#` 或 `?` 萬用字元來篩選子主題。 由於「IoT 中樞」不是一般用途的發行/訂閱傳訊訊息代理程式，因此它只支援已記載的主題名稱和主題篩選。

裝置在成功訂閱 IoT 中樞的裝置特定端點 (由 `devices/{device_id}/messages/devicebound/#` 主題篩選代表) 之後，才會收到來自 IoT 中樞的訊息。 建立訂閱之後，裝置將會接收在訂閱之後傳送給它的雲端到裝置訊息。 如果裝置是在 **CleanSession** 旗標設定為 **0** 的情況下連線，訂閱將會跨不同的工作階段持續保留。 在此情況下，下次裝置以 **CleanSession 0** 進行連線時，就會收到中斷連線時傳送給它的任何未送訊息。 如果裝置使用設定為 **1** 的 **CleanSession** 旗標，則必須等到訂閱 IoT 中樞的裝置端點之後，才會收到來自 IoT 中樞的訊息。

IoT 中樞會在有訊息屬性時，傳遞**主題名稱**`devices/{device_id}/messages/devicebound/`或 `devices/{device_id}/messages/devicebound/{property_bag}` 的訊息。 `{property_bag}` 包含訊息屬性的 url 編碼索引鍵/值組。 屬性包中只會包含應用程式屬性和使用者可設定的系統屬性 (例如 **messageId** 或 **correlationId**)。 系統屬性名稱具有前置詞 **$** ，但應用程式屬性則會使用沒有前置詞的原始屬性名稱。

當裝置應用程式訂閱具有 **QoS 2** 的主題時，IoT 中樞會在 **SUBACK** 封包中授與最大 QoS 層級 1。 之後，IoT 中樞會使用 QoS 1 將訊息傳遞給裝置。

## <a name="retrieving-a-device-twins-properties"></a>擷取裝置對應項屬性

首先，裝置會訂閱 `$iothub/twin/res/#`，以接收作業的回應。 然後，它會傳送空白訊息給主題 `$iothub/twin/GET/?$rid={request id}`，其中已填入**要求 ID** 的值。 服務接著會使用和要求相同的`$iothub/twin/res/{status}/?$rid={request id}`要求 ID **，傳送內含關於**  主題之裝置對應項資料的回應訊息。

[要求識別碼] 可以是訊息屬性值的任何有效值（根據[IoT 中樞訊息開發人員指南](iot-hub-devguide-messaging.md)），而狀態會驗證為整數。

回應本文包含裝置對應項的 properties 區段，如以下回應範例所示：

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

可能的狀態碼如下︰

|狀態 | 描述 |
| ----- | ----------- |
| 200 | Success |
| 429 | 太多要求（節流），依據[IoT 中樞節流](iot-hub-devguide-quotas-throttling.md) |
| 5** | 伺服器錯誤 |

如需詳細資訊，請參閱[裝置 twins 開發人員指南](iot-hub-devguide-device-twins.md)。

## <a name="update-device-twins-reported-properties"></a>更新裝置對應項的報告屬性

為了更新所報告的屬性，裝置會透過對指定 MQTT 主題進行發佈，將要求發給 IoT 中樞。 處理該要求之後，IoT 中樞會透過對另一個主題進行發佈，來回應更新作業的成功或失敗狀態。 裝置可以訂閱本主題，以收到其對應項更新要求結果的通知。 為了在 MQTT 中執行這種類型的要求/回應互動，我們會利用裝置一開始在其更新要求中提供的要求識別碼（`$rid`）概念。 此要求識別碼也會包含在 IoT 中樞的回應中，以允許裝置將回應與特定先前的要求相互關聯。

下列順序說明在 IoT 中樞的裝置對應項中，裝置如何更新報告的屬性︰

1. 裝置必須訂閱 `$iothub/twin/res/#` 主題，才能從 IoT 中樞接收作業的回應。

2. 裝置會將包含裝置對應項新的訊息傳送至 `$iothub/twin/PATCH/properties/reported/?$rid={request id}` 主題。 此訊息包含**要求 ID** 值。

3. 服務接著會傳送回應訊息，其中包含`$iothub/twin/res/{status}/?$rid={request id}` 主題上報告之屬性集合的新 ETag 值。 這個回應訊息使用和要求相同的**要求 ID**。

要求訊息本文會包含 JSON 文件，其包含已報告屬性的新值。 JSON 文件中的每個成員會在裝置對應項的文件中更新或新增對應的成員。 設定為 `null` 的成員會從包含的物件中刪除成員。 例如：

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

可能的狀態碼如下︰

|狀態 | 描述 |
| ----- | ----------- |
| 204 | 成功 (不會傳回任何內容) |
| 400 | 不正確的要求。 JSON 格式錯誤 |
| 429 | 太多要求（節流），依據[IoT 中樞節流](iot-hub-devguide-quotas-throttling.md) |
| 5** | 伺服器錯誤 |

下列 Python 程式碼片段示範透過 MQTT (使用 Paho MQTT 用戶端) 來進行的對應項報告屬性更新程序：

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

在上述對應項報告屬性更新作業成功時，來自 IoT 中樞的發佈訊息會有下列主題：`$iothub/twin/res/204/?$rid=1&$version=6`，其中 `204` 是表示成功的狀態碼、`$rid=1` 對應至裝置在程式碼中提供的要求識別碼，`$version` 則對應至更新之後裝置對應項報告屬性區段的版本。

如需詳細資訊，請參閱[裝置 twins 開發人員指南](iot-hub-devguide-device-twins.md)。

## <a name="receiving-desired-properties-update-notifications"></a>接收所需屬性更新通知

當連接裝置時，IoT 中樞傳送通知給主題 `$iothub/twin/PATCH/properties/desired/?$version={new version}`，其中包含解決方案後端所執行的更新內容。 例如：

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

就屬性更新而言，`null` 值表示正在刪除 JSON 物件成員。 另請注意，`$version` 指出對應項所需屬性區段的新版本。

> [!IMPORTANT]
> IoT 中樞只會在連接裝置時產生變更通知。 請務必執行裝置重新連線[流程](iot-hub-devguide-device-twins.md#device-reconnection-flow)，讓所需的屬性在 IoT 中樞和裝置應用程式之間保持同步。

如需詳細資訊，請參閱[裝置 twins 開發人員指南](iot-hub-devguide-device-twins.md)。

## <a name="respond-to-a-direct-method"></a>回應直接方法

首先，裝置必須訂閱 `$iothub/methods/POST/#`。 IoT 中樞會將方法要求傳送至主題 `$iothub/methods/POST/{method name}/?$rid={request id}`，其中含有有效的 JSON 或空白本文。

若要回應，裝置會將具有有效 JSON 的或內文空白的訊息傳送至 `$iothub/methods/res/{status}/?$rid={request id}` 主題。 在此訊息中，**要求識別碼**必須與要求訊息中的相符，且**狀態**必須是整數。

如需詳細資訊，請參閱[直接方法開發人員指南](iot-hub-devguide-direct-methods.md)。

## <a name="additional-considerations"></a>其他考量

最後要考慮的是，如果您需要在雲端端自訂 MQTT 通訊協定行為，則應該查看[Azure IoT 通訊協定閘道](iot-hub-protocol-gateway.md)。 此軟體可讓您部署高效能的自訂通訊協定閘道，而且可直接與 IoT 中樞連接。 Azure IoT 通訊協定閘道器可讓您自訂裝置通訊協定，以順應要重建的 MQTT 部署或其他自訂通訊協定。 不過，這種方法會要求您執行及操作自訂通訊協定閘道。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 MQTT 通訊協定，請參閱[MQTT 檔](https://mqtt.org/documentation)。

若要深入了解如何規劃 IoT 中樞部署，請參閱：

* [Azure IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/)
* [支援其他通訊協定](iot-hub-protocol-gateway.md)
* [與事件中樞比較](iot-hub-compare-event-hubs.md)
* [調整、HA 和 DR](iot-hub-scaling.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)
