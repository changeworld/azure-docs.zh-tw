---
title: 瞭解 Azure IoT 裝置配置服務 MQTT 支援 |微軟文件
description: 開發人員指南 - 支援使用 MQTT 協定連接到 Azure IoT 設備配置服務 (DPS) 面向設備的終結點。
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 213fc3412a2dfad77946e52a355a30774d6860c7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680681"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>使用 MQTT 協定與 DPS 通訊

DPS 使用裝置能使用以下方式與 DPS 裝置的執行點進行通訊:

* 連接埠 8883 上的 [MQTT v3.1.1](https://mqtt.org/)
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127)通過埠 443 上的 WebSocket。

DPS 不是功能齊全的 MQTT 代理,不支援 MQTT v3.1.1 標準中指定的所有行為。 本文介紹設備如何使用支援的 MQTT 行為與 DPS 通信。

必須使用 TLS/SSL 保護與 DPS 的所有設備通信。 因此,DPS 不支援通過埠 1883 進行不安全的連接。

 > [!NOTE] 
 > DPS 目前不支援在 MQTT 協定上使用 TPM[認證機制](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism)的設備。

## <a name="connecting-to-dps"></a>連線到 DPS

設備可以使用 MQTT 協定使用以下任一選項連接到 DPS。

* [Azure IoT 預配 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks)中的庫。
* MQTT 協定直接。

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>直接使用 MQTT 通訊協定 (作為裝置)

如果裝置無法使用裝置 SDK，它仍可使用連接埠 8883 上的 MQTT 通訊協定連線到公用裝置端點。 在**CONNECT**封包中,裝置應使用以下值:

* 對於**ClientId**欄位,請使用**註冊代碼**。

* 對於**使用者名**字段,`{idScope}/registrations/{registration_id}/api-version=2019-03-31`請使用 ,DPS 的`{idScope}`[idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)是其中的。

* 在 [Password]**** 欄位中，使用 SAS 權杖。 SAS 權杖的格式與 HTTPS 和 AMQP 通訊協定的格式相同：

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`資源的 URI`{idScope}/registrations/{registration_id}`應使用格式 。 原則名稱應為`registration`。

  > [!NOTE]
  > 如果您使用 X.509 憑證驗證，則不需要 SAS 權杖密碼。

  有關如何生成 SAS 權杖的詳細資訊,請參閱[對 DPS 的"控制"存取](how-to-control-access.md#security-tokens)的安全權杖部分。

以下是特定於 DPS 實現的行為清單:

 * DPS 不支援將**CleanSession**標誌設置為**0**的功能。

 * 當設備應用訂閱具有**QoS 2**的主題時,DPS 在**SUBACK**數據包中授予最大 QoS 級別 1。 之後,DPS 使用 QoS 1 向設備傳遞消息。

## <a name="tlsssl-configuration"></a>TLS/SSL 組態

要直接使用 MQTT 協定,客戶端*必須透過*TLS 1.2 進行連接。 嘗試略過此步驟會因連線錯誤而發生失敗。


## <a name="registering-a-device"></a>註冊裝置

要透過 DPS 註冊裝置,裝置`$dps/registrations/res/#`應使用**主題篩選器**訂閱 。 「主題篩選」中的多層級萬用字元 `#` 僅供用來允許裝置接收主題名稱中的額外屬性。 DPS 不允許使用`#``?`或通配符來篩選子主題。 由於 DPS 不是通用的 pub-子訊息傳遞代理,因此它僅支援記錄的主題名稱和主題篩選器。

設備應使用`$dps/registrations/PUT/iotdps-register/?$rid={request_id}`**主題名稱**將註冊消息發佈到 DPS。 有效負載應包含 JSON 格式[的設備註冊](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration)物件。
在成功方案中,設備將收到主題名稱上的回應,`$dps/registrations/res/202/?$rid={request_id}&retry-after=x`其中 x 是秒的重試後值。 回應的有效負載將包含 JSON 格式的[註冊操作狀態](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus)物件。

## <a name="polling-for-registration-operation-status"></a>輪詢註冊操作狀態

設備必須定期輪詢服務以接收設備註冊操作的結果。 假設設備已按上述內容訂閱`$dps/registrations/res/#`了主題,則可以將獲取操作狀態消息發佈到`$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}`主題名稱。 此消息中的操作 ID 應為上一步驟中的"註冊操作狀態"回應消息中接收的值。 在成功的情況下,服務將響應該`$dps/registrations/res/200/?$rid={request_id}`主題。 回應的有效負載將包含"註冊操作狀態"物件。 如果回應代碼在延遲等於重試后一段時間後為 202,則設備應繼續輪詢服務。 如果服務返回 200 狀態代碼,則設備註冊操作將成功。

## <a name="connecting-over-websocket"></a>透過 Web 插座連線
透過 Websocket 連線時,將`mqtt`子協定 指定為 。 遵循[RFC 6455](https://tools.ietf.org/html/rfc6455)。

## <a name="next-steps"></a>後續步驟

若要深入了解 MQTT 通訊協定，請參閱 [MQTT 文件](https://mqtt.org/documentation)。

要進一步探討 DPS 的功能,請參閱:

* [關於物聯網 DPS](about-iot-dps.md)
