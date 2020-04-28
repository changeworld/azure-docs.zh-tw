---
title: 裝置連線能力和遙測輸入-Azure 數位 Twins |Microsoft Docs
description: 瞭解如何從 Azure 數位 Twins 中的 IoT 裝置連接、上線及傳送遙測。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5c2c519ece9806b92c3e455d5f550bc2abfc9f3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75862470"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>裝置連線能力與遙測輸入

裝置和感應器所傳送的遙測資料能組成任何 IoT 解決方案的骨幹。 如何在一個位置內容中呈現這些不同的資源並加以管理，是 IoT 應用程式開發的重要課題。 Azure Digital Twins 會使用空間智慧圖表來整合裝置與感應器，藉以簡化 IoT 解決方案的開發程序。

若要開始使用，請在空間圖形的根位置建立 Azure IoT 中樞資源。 IoT 中樞資源會允許根空間下的所有裝置傳送訊息。 建立 IoT 中樞後，請向 Digital Twins 執行個體內的感應器註冊裝置。 裝置可透過 [Azure IoT 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 將資料傳送到 Digital Twins 服務。

如需如何讓裝置上線的逐步指南，請閱讀[部署和設定數位 Twins 的教學](tutorial-facilities-setup.md)課程。 快速瀏覽後，步驟如下：

- 從 [Azure 入口網站](https://portal.azure.com)部署 Digital Twins 執行個體。
- 在圖表中建立空間。
- 建立 IoT 中樞資源，並將其指派給圖表中的空間。
- 在圖表中建立裝置和感應器，並將它們指派給前述步驟中建立的空間。
- 建立比對器，以根據條件篩選遙測訊息。
- 建立[使用者定義函數](concepts-user-defined-functions.md)，並將它指派給圖形中的空間，以自訂遙測訊息的處理。
- 指派角色以允許使用者定義函式存取圖表資料。
- 從 Digital Twins 管理 API 中取得 IoT 中樞裝置連接字串。
- 使用 Azure IoT 裝置 SDK 在裝置上設定裝置連接字串。

在以下幾節中，您將了解如何從 Digital Twins 管理 API 中取得 IoT 中樞裝置連接字串。 您也將了解如何使用 IoT 中樞遙測訊息格式來傳送以感應器為基礎的遙測。 Digital Twins 會要求所收到的每個遙測片段，都要與空間圖表內的感應器相關聯。 此要求可確保資料會在適當的空間內容中進行處理和路由。

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>從管理 API 中取得 IoT 中樞裝置連接字串

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

使用 `includes=ConnectionString` 參數在裝置 API 上執行 GET 呼叫，以取得 IoT 中樞裝置連接字串。 以裝置 GUID 或硬體識別碼進行篩選，以找出指定的裝置。

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| 參數 | 更換為 |
| --- | --- |
| *YOUR_DEVICE_GUID* | 裝置識別碼 |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| 參數值 | 更換為 |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | 裝置硬體識別碼 |

在回應承載中，複製裝置的 **connectionString** 屬性。 您在呼叫 Azure IoT 裝置 SDK 以將資料傳送至 Digital Twins 時，必須用到此屬性。

## <a name="device-to-cloud-message"></a>裝置到雲端的訊息

您可以自訂裝置的訊息格式和承載，以符合您解決方案的需求。 請使用任何可序列化為位元組陣列或資料流，並且由 [Azure IoT 裝置用戶端訊息類別 Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___) 所支援的資料合約。 訊息可以是自訂的二進位格式，但您必須在對應的使用者定義函式中解碼資料合約。 裝置到雲端的訊息只有一項需求。 維護一組屬性，以確保您的訊息會正確地路由傳送至處理引擎。

### <a name="telemetry-properties"></a>遙測屬性

 **Message** 的承載內容可以是大小上限為 256 KB 的任意資料。 [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet)類型的屬性需要一些需求。 下表顯示系統支援的必要和選擇性屬性。

| 屬性名稱 | 值 | 必要 | 描述 |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | 是 | 識別系統訊息的常數值。 |
| **DigitalTwins-SensorHardwareId** | `string(72)` | 是 | 傳送 **Message** 之感應器的唯一識別碼。 此值必須符合物件的 **HardwareId** 屬性，才能讓系統進行處理。 例如： `00FF0643BE88-CO2` 。 |
| **CreationTimeUtc** | `string` | 否 | [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 格式的日期字串，用來識別承載的取樣時間。 例如： `2018-09-20T07:35:00.8587882-07:00` 。 |
| **CorrelationId** | `string` | 否 | 用來追蹤系統上各個事件的 UUID。 例如： `cec16751-ab27-405d-8fe6-c68e1412ce1f` 。

### <a name="send-your-message-to-digital-twins"></a>將訊息傳送至 Digital Twins

使用 DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) 或 [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) 呼叫，將訊息傳送至 Digital Twins。

## <a name="next-steps"></a>後續步驟

- 若要瞭解 Azure 數位 Twins 資料處理和使用者定義函式的功能，請閱讀[Azure 數位 Twins 資料處理和使用者定義函數](concepts-user-defined-functions.md)。
