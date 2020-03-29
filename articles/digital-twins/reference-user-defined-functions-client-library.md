---
title: 使用者定義的函數用戶端庫引用 - Azure 數位孿生 |微軟文檔
description: Azure 數位雙月使用者定義的函數用戶端庫參考文檔。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: bd6095daca51ddca0cfb4b34ca86e763df9a3d02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276822"
---
# <a name="user-defined-functions-client-library-reference"></a>使用者定義函式用戶端程式庫參考

本文提供 Azure Digital Twins 使用者定義函式用戶端程式庫的參考資訊。

## <a name="helper-methods"></a>協助程式方法

用戶端程式庫定義常用作業的協助程式方法。

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

此函式根據指定的空間識別碼，從圖表擷取空間。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 空間識別碼 |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

此函式根據指定的感應器識別碼，從圖表擷取感應器。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 感應器識別碼 |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

此函式根據指定的裝置識別碼，從圖表擷取裝置。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | 裝置識別碼 |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

此函式根據指定的感應器識別碼和其資料類型，擷取該感應器的目前值。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| sensorId**  | `guid` | 感應器識別碼 |
| *dataType*  | `string` | 感應器資料類型 |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

此函式根據指定的空間識別碼和值的名稱，擷取該空間屬性的目前值。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| spaceId**  | `guid` | 空間識別碼 |
| *值名稱* | `string` | 空間屬性名稱 |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

此函式根據指定的感應器識別碼和其資料類型，擷取該感應器的歷史值。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| sensorId** | `guid` | 感應器識別碼 |
| *dataType* | `string` | 感應器資料類型 |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

此函式根據指定的空間識別碼和值的名稱，擷取空間上該屬性的歷史值。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| spaceId** | `guid` | 空間識別碼 |
| *值名稱* | `string` | 空間屬性名稱 |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

此函式根據指定的空間識別碼，擷取該父空間的子空間。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| spaceId** | `guid` | 空間識別碼 |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

此函式根據指定的空間識別碼，擷取該父空間的子感應器。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| spaceId** | `guid` | 空間識別碼 |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

此函式根據指定的空間識別碼，擷取該父空間的子裝置。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| spaceId** | `guid` | 空間識別碼 |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

此函式根據指定的裝置識別碼，擷取該父裝置的子感應器。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *設備 Id* | `guid` | 裝置識別碼 |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

此函式根據指定的空間識別碼，擷取其父空間。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| childSpaceId** | `guid` | 空間識別碼 |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

此函式根據指定的感應器識別碼，擷取其父空間。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| childSensorId** | `guid` | 感應器識別碼 |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

此函式根據指定的裝置識別碼，擷取其父空間。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| childDeviceId** | `guid` | 裝置識別碼 |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

此函式根據指定的感應器識別碼，擷取其父裝置。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| childSensorId** | `guid` | 感應器識別碼 |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

此函式根據指定的空間識別碼，從該空間擷取屬性和其值。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| spaceId** | `guid` | 空間識別碼 |
| *屬性名稱* | `string` | 空間屬性名稱 |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

此函式根據指定的感應器識別碼，從該感應器擷取屬性和其值。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| sensorId** | `guid` | 感應器識別碼 |
| *屬性名稱* | `string` | 感應器屬性名稱 |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

此函式根據指定的裝置識別碼，從該裝置擷取屬性和其值。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *設備 Id* | `guid` | 裝置識別碼 |
| *屬性名稱* | `string` | 裝置屬性名稱 |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

此函式在具有指定資料類型的感應器物件上設定值。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| sensorId** | `guid` | 感應器識別碼 |
| *dataType*  | `string` | 感應器資料類型 |
| *值*  | `string` | 值 |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

此函式在具有指定資料類型的空間物件上設定值。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| spaceId** | `guid` | 空間識別碼 |
| *dataType* | `string` | 資料類型 |
| *值* | `string` | 值 |

### <a name="logmessage"></a>log(message)

此函式記錄使用者定義函式中的下列訊息。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *消息* | `string` | 要記錄的訊息 |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

此函式傳送出要分派的自訂通知。

**種類**：全域函式

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| topologyObjectId**  | `guid` | 圖表物件識別碼。 範例如空間、感應器和裝置識別碼。|
| topologyObjectType**  | `string` | 範例如感應器和裝置。|
| *payload*  | `string` | 與通知一起傳送的 JSON 承載。 |

## <a name="return-types"></a>傳回類型

用戶端參考協助程式方法傳回的回應模型如下所示。

### <a name="space"></a>Space

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
}
```

### <a name="space-methods"></a>空間方法

#### <a name="parent--space"></a>Parent() ⇒ `space`

此函式傳回目前空間的父空間。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

此函式傳回目前空間的子感應器。

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

此函式傳回目前空間的子裝置。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函式傳回目前空間的擴充屬性和其值。

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *屬性名稱* | `string` | 擴充屬性的名稱 |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

此函式傳回目前空間的值。

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *值名稱* | `string` | 值的名稱 |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

此函式傳回目前空間的歷史值。

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *值名稱* | `string` | 值的名稱 |

#### <a name="notifypayload"></a>Notify(payload)

此函式傳送具有指定承載的通知。

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 要包含在通知中的 JSON 承載 |

### <a name="device"></a>裝置

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>裝置方法

#### <a name="parent--space"></a>Parent() ⇒ `space`

此函式傳回目前裝置的父空間。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

此函式傳回目前裝置的子感應器。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函式傳回目前裝置的擴充屬性和其值。

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *屬性名稱* | `string` | 擴充屬性的名稱 |

#### <a name="notifypayload"></a>Notify(payload)

此函式傳送具有指定承載的通知。

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 要包含在通知中的 JSON 承載 |

### <a name="sensor"></a>感應器

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>感應器方法

#### <a name="space--space"></a>Space() ⇒ `space`

此函式傳回目前感應器的父空間。

#### <a name="device--device"></a>Device() ⇒ `device`

此函式傳回目前感應器的父裝置。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函式傳回目前感應器的擴充屬性和其值。

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *屬性名稱* | `string` | 擴充屬性的名稱 |

#### <a name="value--value"></a>Value() ⇒ `value`

此函式傳回目前感應器的值。

#### <a name="history--value"></a>History() ⇒ `value[]`

此函式傳回目前感應器的歷史值。

#### <a name="notifypayload"></a>Notify(payload)

此函式傳送具有指定承載的通知。

| 參數  | 類型                | 描述  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 要包含在通知中的 JSON 承載 |

### <a name="value"></a>值

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>擴充的屬性

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Digital Twins 使用者定義函式](./concepts-user-defined-functions.md)。

- 了解[如何建立使用者定義函式](./how-to-user-defined-functions.md)。

- 了解[如何偵錯使用者定義函式](./how-to-diagnose-user-defined-functions.md)。
