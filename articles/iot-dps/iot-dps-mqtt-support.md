---
title: 瞭解 Azure IoT 裝置布建服務 MQTT 支援 |Microsoft Docs
description: 開發人員指南-使用 MQTT 通訊協定連接到 Azure IoT 裝置布建服務（DPS）裝置面向端點的裝置支援。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680681"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>使用 MQTT 通訊協定與您的 DPS 通訊

DPS 可讓裝置使用來與 DPS 裝置端點通訊：

* 連接埠 8883 上的 [MQTT v3.1.1](https://mqtt.org/)
* 埠443上的[MQTT v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) over WebSocket。

DPS 不是功能完整的 MQTT 訊息代理程式，而且不支援 MQTT v 3.1.1 標準中指定的所有行為。 本文說明裝置可以如何使用支援的 MQTT 行為與 DPS 通訊。

所有與 DPS 的裝置通訊都必須使用 TLS/SSL 來加以保護。 因此，DPS 不支援透過埠1883的非安全連線。

 > [!NOTE] 
 > DPS 目前不支援在 MQTT 通訊協定上使用 TPM[證明機制](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism)的裝置。

## <a name="connecting-to-dps"></a>正在連線到 DPS

裝置可以使用 MQTT 通訊協定，使用下列任何選項來連接到 DPS。

* [Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks)布建 sdk 中的程式庫。
* 直接使用 MQTT 通訊協定。

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>直接使用 MQTT 通訊協定 (作為裝置)

如果裝置無法使用裝置 SDK，它仍可使用連接埠 8883 上的 MQTT 通訊協定連線到公用裝置端點。 在 **CONNECT** 封包中，裝置應使用下列值：

* 在 [ **ClientId** ] 欄位中，使用**registrationId**。

* 在 [使用者**名稱**] 欄位中，使用 `{idScope}/registrations/{registration_id}/api-version=2019-03-31` ，其中 `{idScope}` 是 DPS 的[idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) 。

* 在 [Password] 欄位中，使用 SAS 權杖。 SAS 權杖的格式與 HTTPS 和 AMQP 通訊協定的格式相同：

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`ResourceURI 的格式應為 `{idScope}/registrations/{registration_id}` 。 原則名稱應該是 `registration` 。

  > [!NOTE]
  > 如果您使用 X.509 憑證驗證，則不需要 SAS 權杖密碼。

  如需如何產生 SAS 權杖的詳細資訊，請參閱[控制 DPS 的存取權](how-to-control-access.md#security-tokens)的安全性權杖一節。

以下是 DPS 執行特有行為的清單：

 * DPS 不支援將**CleanSession**旗標設為**0**的功能。

 * 當裝置應用程式訂閱具有**QoS 2**的主題時，DPS 會在**SUBACK**封包中授與最大 QoS 層級1。 之後，DPS 會使用 QoS 1 將訊息傳遞至裝置。

## <a name="tlsssl-configuration"></a>TLS/SSL 組態

若要直接使用 MQTT 通訊協定，您的用戶端*必須*透過 TLS 1.2 進行連接。 嘗試略過此步驟會因連線錯誤而發生失敗。


## <a name="registering-a-device"></a>註冊裝置

若要透過 DPS 註冊裝置，裝置應該使用 `$dps/registrations/res/#` 做為**主題篩選**來進行訂閱。 「主題篩選」中的多層級萬用字元 `#` 僅供用來允許裝置接收主題名稱中的額外屬性。 DPS 不允許使用 `#` 或 `?` 萬用字元來篩選子主題。 因為 DPS 不是一般用途的 pub-sub 訊息代理人，所以它只支援已記載的主題名稱和主題篩選器。

裝置應該使用 `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` 做為**主題名稱**，將註冊訊息發佈至 DPS。 承載應包含 JSON 格式的[裝置註冊](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration)物件。
在成功的案例中，裝置會收到主題名稱的回應， `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` 其中 x 是重試後的值（以秒為單位）。 回應的裝載會包含 JSON 格式的[RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus)物件。

## <a name="polling-for-registration-operation-status"></a>輪詢註冊操作狀態

裝置必須定期輪詢服務，以接收裝置註冊作業的結果。 假設裝置已如上面所述來訂閱 `$dps/registrations/res/#` 主題，它可以將 get operationstatus 訊息發佈至 `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` 主題名稱。 此訊息中的作業識別碼應該是在上一個步驟的 RegistrationOperationStatus 回應訊息中收到的值。 在成功的情況下，服務將會回應 `$dps/registrations/res/200/?$rid={request_id}` 主題。 回應的承載將包含 RegistrationOperationStatus 物件。 如果回應碼在延遲等於重試之後的時間為202，則裝置應持續輪詢服務。 如果服務傳回200狀態碼，裝置註冊作業就會成功。

## <a name="connecting-over-websocket"></a>透過 Websocket 連接
透過 Websocket 連線時，請將子通訊協定指定為 `mqtt` 。 遵循[RFC 6455](https://tools.ietf.org/html/rfc6455)。

## <a name="next-steps"></a>後續步驟

若要深入了解 MQTT 通訊協定，請參閱 [MQTT 文件](https://mqtt.org/documentation)。

若要進一步探索 DPS 的功能，請參閱：

* [關於 IoT DPS](about-iot-dps.md)
