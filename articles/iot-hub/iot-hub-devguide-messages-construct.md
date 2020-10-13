---
title: 了解 Azure IoT 中樞訊息格式 | Microsoft Docs
description: 開發人員指南 - 說明 IoT 中樞訊息的格式和預期內容。
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 06db7b46d095a79177b80bba6df3d5e9aadad16b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766977"
---
# <a name="create-and-read-iot-hub-messages"></a>建立及讀取 IoT 中樞訊息

為了支援完美的跨通訊協定互通性，IoT 中樞定義了適用於所有裝置面向通訊協定的通用訊息格式。 此訊息格式可用於[裝置到雲端的路由](iot-hub-devguide-messages-d2c.md)和[雲端到裝置](iot-hub-devguide-messages-c2d.md)的訊息。 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT 中樞使用串流訊息模式實作裝置到雲端傳訊。 IoT 中樞的裝置到雲端訊息與[事件中樞](/azure/event-hubs/)事件較為相似 (而不像[服務匯流排](/azure/service-bus-messaging/)訊息) 因為會有大量的事件傳遞給該服務，並可由多個讀取器讀取。

IoT 中樞訊息包含：

* 預先決定的*系統屬性*集如下所示。

* 一組 *應用程式屬性*。 應用程式可以定義的字串屬性字典，而且不需將訊息本文還原序列化即可加以存取。 IoT 中樞不會修改這些屬性。

* 不透明的二進位主體。

上述屬性名稱和值只能包含 ASCII 英數字元，而且 ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` 在您使用 HTTPS 通訊協定傳送裝置到雲端或雲端到裝置的訊息時也是如此。

IoT 中樞的裝置對雲端傳訊具有下列特性：

* 裝置到雲端訊息會長期保留在 IoT 中樞的預設 **messages/events** 端點，最多達七天。

* 裝置到雲端訊息最大可能為 256 KB，而且可分成數個批次以最佳化傳送。 批次最多可達 256 KB。

* IoT 中樞不允許任意進行資料分割。 裝置到雲端訊息會根據其原始的 **deviceId**進行分割。

* 如[控制 IoT 中樞的存取權](iot-hub-devguide-security.md)所述，IoT 中樞會啟用每一裝置的驗證和存取控制。

* 您可以使用進入應用程式屬性的資訊來為訊息加上戳記。 如需詳細資訊，請參閱[訊息擴充](iot-hub-message-enrichments-overview.md)。

了解如何使用不同通訊協定傳送的訊息編碼和解碼，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。

## <a name="system-properties-of-d2c-iot-hub-messages"></a>**D2C** IoT 中樞訊息的系統屬性

| 屬性 | 描述  |使用者可設定？|路由查詢的 </br>關鍵字|
| --- | --- | --- | --- |
| message-id |使用者可設定的訊息識別碼，用於「要求-回覆」模式。 格式：區分大小寫的字串，最長為 128 個字元，可使用 ASCII 7 位元英數字元和 `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。  | 是 | messageId |
| iothub-enqueuedtime |IoT 中樞收到[裝置到雲端](iot-hub-devguide-d2c-guidance.md)訊息的日期和時間。 | 否 | enqueuedTime |
| user-id |用來指定訊息來源的識別碼。 當 IoT 中樞產生訊息時，它會設定為 `{iot hub name}`。 | 是 | userId |
| iothub-connection-device-id |由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 它包含傳送訊息之裝置的 **deviceId** 。 | 否 | connectionDeviceId |
| iothub-connection-module-id |由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 包含傳送訊息之裝置的 **moduleId** 。 | 否 | connectionModuleId |
| iothub-connection-auth-generation-id |由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 包含傳送訊息之裝置的 **connectionDeviceGenerationId** (依據[裝置身分識別屬性](iot-hub-devguide-identity-registry.md#device-identity-properties))。 | 否 |connectionDeviceGenerationId |
| iothub-connection-auth-method |由 IoT 中樞在裝置到雲端訊息上設定的驗證方法。 這個屬性包含用來驗證傳送訊息之裝置的驗證方法的相關資訊。| 否 | connectionAuthMethod |
| dt-dataschema | 此值是由 IoT 中樞在裝置到雲端訊息上設定的。 它包含裝置連線中所設定的裝置型號識別碼。 | 否 | N/A |
| dt-subject | 傳送裝置到雲端訊息的元件名稱。 | 是 | N/A |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>**C2D** IoT 中樞訊息的系統屬性

| 屬性 | 描述  |使用者可設定？|
| --- | --- | --- |
| message-id |使用者可設定的訊息識別碼，用於「要求-回覆」模式。 格式：區分大小寫的字串，最長為 128 個字元，可使用 ASCII 7 位元英數字元和 `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。  |是|
| sequence-number |IoT 中樞指派給每則雲端到裝置訊息的數字 (對每個裝置佇列而言都是唯一的)。 |否|
| to |[雲端到裝置](iot-hub-devguide-c2d-guidance.md) 訊息中指定的目的地。 |否|
| absolute-expiry-time |訊息到期的日期和時間。 |否|   |
| correlation-id |回應訊息中的字串屬性，通常包含採用「要求-回覆」模式之要求的 MessageId。 |是|
| user-id |用來指定訊息來源的識別碼。 當 IoT 中樞產生訊息時，它會設定為 `{iot hub name}`。 |是|
| iothub-ack |意見反應訊息產生器。 這個屬性是在雲端到裝置訊息中使用，可要求 IoT 中樞因為裝置取用訊息而產生意見反應訊息。 可能的值︰**none** (預設值)︰不會產生任何意見反應訊息；**positive**︰如果訊息已完成，則會收到意見反應訊息；**negative**︰如果訊息未由裝置完成就到期 (或已達到最大傳遞計數) 則會收到意見反應訊息；或者 **full**︰positive 和 negative。 |是|

### <a name="system-property-names"></a>系統屬性名稱

系統屬性名稱會根據路由傳送訊息的目標端點而有所不同。 如需這些名稱的詳細資訊，請參閱下表。

|系統屬性名稱|事件中樞|Azure 儲存體|服務匯流排|Event Grid|
|--------------------|----------|-------------|-----------|----------|
|訊息 ID|message-id|messageId|MessageId|message-id|
|User id|user-id|userId|UserId|user-id|
|Connection device id|iothub-connection-device-id| connectionDeviceId|iothub-connection-device-id|iothub-connection-device-id|
|Connection module id|iothub-connection-module-id|connectionModuleId|iothub-connection-module-id|iothub-connection-module-id|
|Connection auth generation id|iothub-connection-auth-generation-id|connectionDeviceGenerationId| iothub-connection-auth-generation-id|iothub-connection-auth-generation-id|
|Connection auth method|iothub-connection-auth-method|connectionAuthMethod|iothub-connection-auth-method|iothub-connection-auth-method|
|ContentType|Content-Type|ContentType|ContentType|iothub-content-type|
|contentEncoding|content-encoding|contentEncoding|ContentEncoding|iothub-content-encoding|
|iothub-enqueuedtime|iothub-enqueuedtime|enqueuedTime| N/A |iothub-enqueuedtime|
|CorrelationId|correlation-id|correlationId|CorrelationId|correlation-id|
|dt-dataschema|dt-dataschema|dt-dataschema|dt-dataschema|dt-dataschema|
|dt-subject|dt-subject|dt-subject|dt-subject|dt-subject|

## <a name="message-size"></a>訊息大小

IoT 中樞會以不限通訊協定的方式 (只考慮實際的承載) 測量郵件大小。 大小 (以位元組為單位) 的計算方式為下列值的總和︰

* 內文大小 (以位元組為單位)。
* 訊息系統屬性所有值的大小 (以位元組為單位)。
* 所有使用者屬性名稱和值的大小 (以位元組為單位)。

屬性名稱和值僅限使用 ASCII 字元，因此字串長度會等於大小 (以位元組為單位)。

## <a name="anti-spoofing-properties"></a>防詐騙屬性

為了避免裝置到雲端的訊息中出現裝置詐騙，IoT 中樞使用下列屬性在所有訊息上加上戳記：

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

前兩個屬性包含來源裝置的 **deviceId** 和 **generationId** (依據[裝置身分識別屬性](iot-hub-devguide-identity-registry.md#device-identity-properties))。

**iothub-connection-auth-method** 屬性包含具有下列屬性的 JSON 序列化物件︰

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>後續步驟

* 如需 IoT 中樞訊息大小限制的資訊，請參閱 [IoT 中樞配額和節流](iot-hub-devguide-quotas-throttling.md)。

* 若要了解如何以各種程式設計語言建立及讀取 IoT 中樞訊息，請參閱[快速入門](quickstart-send-telemetry-node.md)。
