---
title: 將安全消息發送到 Azure 安全中心，以便進行 IoT® 服務。微軟文檔
description: 瞭解如何使用適用于 IoT 的 Azure 安全中心發送安全消息。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 8bbbd8248c7418b667e34389cb47bd3f6b4f06ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963813"
---
# <a name="send-security-messages-sdk"></a>傳送安全性訊息 SDK

本操作指南在選擇不使用 IoT 代理 Azure 安全中心收集和發送設備安全消息時，請解釋 IoT 服務 Azure 安全中心功能，並說明如何執行此操作。  

在本指南中，您將了解如何： 
> [!div class="checklist"]
> * 使用 Azure IoT C SDK 發送安全消息
> * 使用 Azure IoT C++ SDK 發送安全消息
> * 使用 Azure IoT Python SDK 發送安全消息
> * 使用 Azure IoT 節點.js SDK 發送安全消息
> * 使用 Azure IoT JAVA SDK 發送安全消息


## <a name="azure-security-center-for-iot-capabilities"></a>用於 IoT 功能的 Azure 安全中心

IoT 的 Azure 安全中心可以處理和分析任何類型的安全消息資料，只要發送的資料符合[IoT 架構的 Azure 安全中心](https://aka.ms/iot-security-schemas)，並且消息設置為安全消息。

## <a name="security-message"></a>安全消息

IoT 的 Azure 安全中心使用以下條件定義安全消息：
- 如果消息是使用 Azure IoT SDK 發送的
- 如果消息符合[安全消息架構](https://aka.ms/iot-security-schemas)
- 如果消息在發送前設置為安全消息

每個安全消息都包括寄件者的中繼資料，如`AgentId`，`AgentVersion``MessageSchemaVersion`和安全事件的清單。
架構定義安全消息的有效和必需屬性，包括事件種類。

>[!Note]
> 所傳送的訊息若不符合結構描述，則會遭到忽略。 請務必先確認結構描述再起始資料的傳送，因為系統目前並不會儲存遭到忽略的訊息。 

>[!Note]
> 未使用 Azure IoT SDK 設置為安全消息發送的郵件將不會路由到 IoT 管道的 Azure 安全中心。

## <a name="valid-message-example"></a>有效消息示例

下面的示例顯示了一個有效的安全消息物件。 該示例包含消息中繼資料和一個`ProcessCreate`安全事件。

設置為安全消息併發送後，此消息將由 IoT 的 Azure 安全中心處理。

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>傳送安全性訊息 

使用[Azure IoT C 設備 SDK、Azure](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) [IoT C++ 設備 SDK、Azure](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) [IoT Node.js SDK、Azure](https://github.com/Azure/azure-iot-sdk-node) [IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)或[Azure IoT JAVA SDK，](https://github.com/Azure/azure-iot-sdk-java)*不使用*Azure 安全中心為 IoT 代理髮送安全消息。

要從設備發送設備資料，供 Azure 安全中心進行 IoT 處理，請使用以下 API 之一標記消息，以便正確路由到 Azure 安全中心進行 IoT 處理管道。 

發送的所有資料（即使標有正確的標頭）也必須符合[IoT 消息架構的 Azure 安全中心。](https://aka.ms/iot-security-schemas) 

### <a name="send-security-message-api"></a>傳送安全性訊息 API 

**發送安全消息**API 當前在 C 和 C#、Python、Node.js 和 JAVA 中可用。  

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```
#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```
#### <a name="nodejs-api"></a>Node.js API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python API

要使用 Python API，您需要安裝包[azure-iot 設備](https://pypi.org/project/azure-iot-device/)。

使用 Python API 時，可以使用唯一的設備或模組連接字串通過模組或通過設備發送安全消息。 使用以下 Python 腳本示例時，使用設備時，請使用**IoTHubDeviceClient**，並使用模組使用**IoTHubModuleClient**。 

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```


## <a name="next-steps"></a>後續步驟
- 閱讀 Azure 安全中心，瞭解 IoT 服務[概述](overview.md)
- 瞭解有關 IoT[體系結構](architecture.md)的 Azure 安全中心
- 啟用[服務](quickstart-onboard-iot-hub.md)
- 閱讀[常見問題解答](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 了解[警示](concept-security-alerts.md)
