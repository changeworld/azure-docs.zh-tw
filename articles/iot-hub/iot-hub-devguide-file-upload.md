---
title: 了解 Azure IoT 中樞檔案上傳 | Microsoft Docs
description: 開發人員指南 - 使用 IoT 中樞的檔案上傳功能，管理將檔案從裝置上傳至 Azure 儲存體 blob 容器。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 35e10c0f9babca7719ff496e7068ad1564670fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209149"
---
# <a name="upload-files-with-iot-hub"></a>透過 IoT 中樞上傳檔案

如 [IoT 中樞端點](iot-hub-devguide-endpoints.md)一文所詳述，裝置可以透過面向裝置的端點 (**/devices/{deviceId}/files**) 傳送通知，藉此開始檔案上傳。 當裝置向 IoT 中樞告知上傳作業完成時，IoT 中樞會透過 **/messages/servicebound/filenotifications** 面向服務的端點傳送檔案上傳通知訊息。

不是透過 IoT 中樞本身的代理訊息，IoT 中樞會做為相關聯 Azure 儲存體帳戶的發送器。 裝置會向 IoT 中樞要求儲存體權杖，這是裝置想要上傳的檔案的特定權杖。 裝置會使用 SAS URI，將檔案上傳至儲存體，上傳完成時，裝置會將完成的通知傳送到 IoT 中樞。 IoT 中樞會在確認檔案上傳已完成後，再將檔案上傳通知訊息新增至服務面向檔案通知端點。

將檔案從裝置上傳到 IoT 中樞之前，您必須先對中樞[關聯 Azure 儲存體](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub)帳戶以設定中樞。

接著，您的裝置就可以[初始化上傳](iot-hub-devguide-file-upload.md#initialize-a-file-upload)，然後在上傳完成時[通知 IoT 中樞](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)。 (選擇性) 當裝置將上傳已完成的狀態通知 IoT 中樞時，服務就會產生[通知訊息](iot-hub-devguide-file-upload.md#file-upload-notifications)。

### <a name="when-to-use"></a>使用時機

使用檔案上傳來傳送間歇性連線裝置所上傳或為了節省頻寬而壓縮的媒體檔案和大型遙測批次。

如果不確定要使用報告屬性、裝置對雲端訊息或檔案上傳，請參閱[裝置到雲端通訊指引](iot-hub-devguide-d2c-guidance.md)。

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>讓 Azure 儲存體帳戶與 IoT 中樞產生關聯

若要使用檔案上傳功能，您必須先將 Azure 儲存體帳戶連結至 IoT 中樞。 您可以通過 Azure 門戶或通過[IoT 中心資來源提供者 REST API](/rest/api/iothub/iothubresource)以程式設計方式完成此任務。 一旦您將 Azure 儲存體帳戶與 IoT 中樞產生關聯之後，服務會在裝置開始檔案上傳要求時，將 SAS URI 傳回至裝置。

[使用 IoT 中樞將檔案從裝置上傳至雲端](iot-hub-csharp-csharp-file-upload.md)操作指南提供檔案上傳程序的完整逐步解說。 這些操作指南會示範如何使用 Azure 入口網站建立儲存體帳戶與 IoT 中樞的關聯。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 會自動處理擷取 SAS URI、上傳檔案，並且通知 IoT 中樞已完成上傳。

## <a name="initialize-a-file-upload"></a>初始化檔案上傳
IoT 中樞擁有專供裝置用來要求儲存體 SAS URI 以便上傳檔案的端點。 若要開始檔案上傳程序，裝置會將含有下列 JSON 主體的 POST 要求傳送到 `{iot hub}.azure-devices.net/devices/{deviceId}/files`：

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

「IoT 中樞」會傳回下列資料，供裝置用來上傳檔案︰

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>已被取代︰使用 GET 初始化檔案上傳

> [!NOTE]
> 本節說明如何從 IoT 中樞接收 SAS URI 的功能，但此功能已被取代。 請使用先前所述的 POST 方法。

IoT 中樞有兩個 REST 端點可以支援檔案上傳，一個用來取得儲存體的 SAS URI，另一個用來通知 IoT 中樞已完成上傳。 裝置會開始檔案上傳程序，方法是將 GET 傳送至 IoT 中樞的 `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`。 IoT 中樞會傳回：

* 要上傳之檔案專屬的 SAS URI。

* 要在上傳完成後使用的相互關聯識別碼。

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>通知 IoT 中樞已完成檔案上傳

裝置會使用 Azure 儲存體 SDK 將檔案上傳至儲存體。 上傳完成時，裝置會將具有下列 JSON 主體的 POST 要求傳送至 `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications`︰

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

`isSuccess` 的值是一個布林值，可指出是否已成功上傳檔案。 `statusCode` 的狀態碼是檔案上傳至儲存體的狀態，而且 `statusDescription` 會對應至 `statusCode`。

## <a name="reference-topics"></a>參考主題：

下列參考主題會提供您關於從裝置上傳檔案的詳細資訊。

## <a name="file-upload-notifications"></a>檔案上傳通知

(選擇性) 當裝置將上傳已完成的狀態通知 IoT 中樞時，IoT 中樞就會產生通知訊息。 此訊息包含檔案的名稱和儲存位置。

如[端點](iot-hub-devguide-endpoints.md)中所述，IoT 中樞會透過面向服務的端點 (**/messages/servicebound/fileuploadnotifications**) 利用訊息來傳遞檔案上傳通知。 檔上載通知的接收語義與雲到設備消息的接收語義相同，並且具有相同的[消息生命週期](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)。 從檔案上傳通知端點擷取的每則訊息是具有下列屬性的 JSON 記錄：

| 屬性 | 描述 |
| --- | --- |
| EnqueuedTimeUtc |指出通知建立時間的時間戳記。 |
| deviceId |**DeviceId** 。 |
| BlobUri |上傳檔案的 URI。 |
| BlobName |上傳檔案的名稱。 |
| LastUpdatedTime |指出上次更新檔案的時間戳記。 |
| BlobSizeInBytes |上傳檔案的大小。 |

**示例**。 此範例顯示檔案上傳通知訊息的本文。

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>檔案上傳通知組態選項

每個 IoT 中樞都具備下列適用於檔案上傳通知的組態選項：

| 屬性 | 描述 | 範圍和預設值 |
| --- | --- | --- |
| **enableFileUploadNotifications** |控制是否將檔案上傳通知寫入檔案通知端點。 |布林 預設值：True。 |
| **fileNotifications.ttlAsIso8601** |檔案上傳通知的預設 TTL。 |ISO_8601 間隔高達 48H (最小為 1 分鐘)。 預設值︰1 小時。 |
| **fileNotifications.lockDuration** |檔案上傳通知佇列的鎖定持續時間。 |5 到 300 秒 (最小值 5 秒)。 預設值：60 秒。 |
| **fileNotifications.maxDeliveryCount** |檔案上傳通知佇列的傳遞計數上限。 |1 到 100。 預設值 = 100。 |

您可以使用 Azure 門戶、Azure CLI 或 PowerShell 在 IoT 中心設置這些屬性。 要瞭解如何，請參閱[設定檔上載](iot-hub-configure-file-upload.md)下的主題。

## <a name="additional-reference-material"></a>其他參考資料

IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點](iot-hub-devguide-endpoints.md)說明執行階段和管理作業的各種 IoT 中樞端點。

* [節流和配額](iot-hub-devguide-quotas-throttling.md)描述適用於 IoT 中樞服務的配額和節流行為。

* [Azure IoT 裝置和服務 SDK](iot-hub-devguide-sdks.md) 列出各種語言 SDK，可供您在開發與「IoT 中樞」互動的裝置和服務應用程式時使用。

* [IoT 中樞查詢語言](iot-hub-devguide-query-language.md)描述可用來從 IoT 中樞擷取有關裝置對應項和作業之資訊的查詢語言。

* [IoT 中樞 MQTT 支援](iot-hub-mqtt-support.md)針對 MQTT 通訊協定提供 IoT 中樞支援的詳細資訊。

## <a name="next-steps"></a>後續步驟

現在您已了解如何使用 IoT 中樞從裝置上傳檔案，接下來您可能對下列 IoT 中樞開發人員指南主題感興趣︰

* [在 IoT 中樞管理裝置身分識別](iot-hub-devguide-identity-registry.md)

* [控制 IoT 中樞的存取權](iot-hub-devguide-security.md)

* [使用裝置對應項同步處理狀態和組態](iot-hub-devguide-device-twins.md)

* [在裝置上叫用直接方法](iot-hub-devguide-direct-methods.md)

* [排程多個裝置上的作業](iot-hub-devguide-jobs.md)

若要嘗試本文所述的一些概念，請參閱下列「IoT 中樞」教學課程：

* [如何使用 IoT 中樞從裝置將檔案上傳至雲端](iot-hub-csharp-csharp-file-upload.md)