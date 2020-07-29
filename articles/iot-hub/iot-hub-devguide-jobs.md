---
title: 了解 Azure IoT 中樞作業 | Microsoft Docs
description: 開發人員指南 - 排定在連接至 IoT 中樞的多個裝置上執行作業。 作業可以在多個裝置上更新標籤和所需的屬性，並叫用直接方法。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81730112"
---
# <a name="schedule-jobs-on-multiple-devices"></a>排程多個裝置上的作業

「Azure IoT 中樞」啟用了一些建置組塊，例如[裝置對應項屬性和標籤](iot-hub-devguide-device-twins.md)及[直接方法](iot-hub-devguide-direct-methods.md)。 一般而言，後端應用程式可讓裝置管理員和操作員在排定的時間大量更新 IoT 裝置並與之互動。 作業可在排定時間，針對一組裝置執行裝置對應項更新和直接方法。 例如，操作員會使用後端應用程式來起始和追蹤作業，在不干擾大樓運作的時段，將 43 號大樓 3 樓的一組裝置重新開機。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

當您需要排定和追蹤一組裝置的下列任何活動進度時，請考慮使用作業︰

* 更新所需屬性
* 更新標籤
* 叫用直接方法

## <a name="job-lifecycle"></a>作業生命週期

作業由解決方案後端起始，並由 IoT 中樞維護。 您可以透過面向服務的 URI (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) 起始作業，並透過面向服務的 URI (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`) 查詢執行中作業的進度。 在作業起始後，若要重新整理執行中作業的狀態，請執行作業查詢。

> [!NOTE]
> 當您起始作業時，屬性名稱和值只能包含 US-ASCII 可列印英數字元，下列集合中的任何字元除外︰`$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>用以執行直接方法的作業

以下程式碼片段顯示 HTTPS 1.1 要求詳細資料，適用於使用作業在一組裝置上執行[直接方法](iot-hub-devguide-direct-methods.md)的情況︰

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

查詢條件也可以針對單一裝置識別碼或針對裝置識別碼清單，如以下範例所示：

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IoT 中樞查詢語言](iot-hub-devguide-query-language.md)涵蓋了IoT 中樞查詢語言的其他詳細資料。

下列程式碼片段顯示已排定在 contoso--1 上的所有裝置上呼叫名為 testMethod 之直接方法的作業要求和回應：

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>用以更新裝置對應項屬性的作業

以下程式碼片段顯示 HTTPS 1.1 要求詳細資料，適用於使用作業來更新裝置對應項屬性的情況：

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> *UpdateTwin*屬性需要有效的 etag 相符;例如， `etag="*"` 。

下列程式碼片段顯示排程在 contoso--1 上更新測試裝置之裝置對應項屬性的作業的要求和回應：

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>查詢作業的進度

以下程式碼片段顯示 HTTPS 1.1 要求詳細資料，適用於查詢作業的情況：

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

ContinuationToken 會從回應來提供。

您可以使用[裝置對應項、作業和訊息路由的 IoT 中樞查詢語言](iot-hub-devguide-query-language.md)，查詢每個裝置上的作業執行狀態。

## <a name="jobs-properties"></a>作業屬性

以下清單顯示屬性和對應的描述，可在查詢作業或作業結果時使用。

| 屬性 | 說明 |
| --- | --- |
| **jobId** |應用程式所提供的作業識別碼。 |
| **時間** |應用程式所提供的作業開始時間 (ISO-8601)。 |
| **endTime** |IoT 中樞所提供的作業完成日期 (ISO-8601)。 在作業到達「已完成」狀態後才有效。 |
| **type** |作業類型： |
| | **scheduleUpdateTwin**：用來更新一組所需屬性或標記的作業。 |
| | **scheduleDeviceMethod**：用來在一組裝置 twins 上叫用裝置方法的作業。 |
| **status** |作業的目前狀態。 狀態的可能值︰ |
| | **pending**︰已排定並等候作業服務執行。 |
| | **scheduled**︰已排定未來時間。 |
| | **running**︰目前作用中的作業。 |
| | **canceled**：作業已被取消。 |
| | **failed**：作業失敗。 |
| | **completed**作業完成。 |
| **deviceJobStatistics** |作業執行的相關統計資料。 |
| | **deviceJobStatistics**屬性： |
| | **deviceJobStatistics.deviceCount**：作業中的裝置數目。 |
| | **deviceJobStatistics.failedCount**：作業失敗的裝置數目。 |
| | **deviceJobStatistics.succeededCount**：作業成功的裝置數目。 |
| | **deviceJobStatistics.runningCount**目前正在執行作業的裝置數目。 |
| | **deviceJobStatistics.pendingCount**：等待執行作業的裝置數目。 |

### <a name="additional-reference-material"></a>其他參考資料

IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點](iot-hub-devguide-endpoints.md)說明每個 IoT 中樞公開給執行階段和管理作業的各種端點。

* [節流和配額](iot-hub-devguide-quotas-throttling.md)說明適用於 IoT 中樞服務的配額，和使用服務時所預期的節流行為。

* [Azure IoT 裝置和服務 SDK](iot-hub-devguide-sdks.md) 列出各種語言 SDK，可供您在開發與「IoT 中樞」互動的裝置和服務應用程式時使用。

* [裝置對應項、作業和訊息路由的 IoT 中樞查詢語言](iot-hub-devguide-query-language.md)說明「IoT 中樞」查詢語言。 您可以使用此查詢語言從「IoT 中樞」擷取有關裝置對應項和作業的資訊。

* [IoT 中樞 MQTT 支援](iot-hub-mqtt-support.md)針對 MQTT 通訊協定提供 IoT 中樞支援的詳細資訊。

## <a name="next-steps"></a>後續步驟

若要嘗試本文所述的一些概念，請參閱下列「IoT 中樞」教學課程：

* [排程及廣播工作](iot-hub-node-node-schedule-jobs.md)