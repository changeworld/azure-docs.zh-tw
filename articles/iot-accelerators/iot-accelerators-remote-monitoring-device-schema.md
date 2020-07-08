---
title: 遠端監視解決方案中的裝置結構描述 - Azure | Microsoft Docs
description: 本文說明 JSON 結構描述，它會定義遠端監視解決方案中的模擬裝置。
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ac681bb13ccea49c7a2f566a6fcdb6adb8cec5bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81683736"
---
# <a name="understand-the-device-model-schema"></a>了解裝置機型結構描述

您可以在遠端監視解決方案中使用模擬裝置以測試其行為。 遠端監視解決方案包含用以執行模擬裝置的裝置模擬服務。 當您部署遠端監視解決方案時，會自動佈建模擬裝置集合。 您可以自訂現有的模擬裝置，或建立您自己的裝置。

本文說明裝置機型結構描述，它會指定模擬裝置的功能和行為。 裝置機型會儲存在 JSON 檔案中。

> [!NOTE]
> 此裝置模型結構描述只適用於裝載於裝置模擬服務中的模擬裝置。 如果您想要建立真實裝置，請參閱[將裝置連線到遠端監視解決方案加速器](iot-accelerators-connecting-devices.md)。

下列文章與目前的文章相關：

* [實作裝置機型行為](iot-accelerators-remote-monitoring-device-behavior.md)描述您用來實作模擬裝置行為的 JavaScript 檔案。
* [建立新的模擬裝置](iot-accelerators-remote-monitoring-create-simulated-device.md)會統整並示範如何將新的模擬裝置類型部署到您的解決方案。

在本文中，您將學會如何：

>[!div class="checklist"]
> * 使用 JSON 檔案以定義模擬裝置機型
> * 指定模擬裝置的屬性
> * 指定模擬裝置傳送的遙測
> * 指定裝置回應的雲端到裝置方法

## <a name="the-parts-of-the-device-model-schema"></a>裝置機型結構描述的部分

每個裝置機型 (例如 chiller 或 truck) 都會定義模擬服務可模擬的裝置類型。 每個裝置機型是以下列最上層結構描述儲存在 JSON 檔案中：

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

您可以在 GitHub 上的 [devicemodels 資料夾](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels)中檢視預設模擬裝置的結構描述檔案。

下表描述最上層結構描述項目：

| 結構描述項目 | Description |
| -- | --- |
| `SchemaVersion` | 結構描述版本一律為 `1.0.0` 且為此檔案格式專屬。 |
| `Id` | 此裝置機型的唯一 ID。 |
| `Version` | 識別裝置機型的版本。 |
| `Name` | 裝置機型的易記名稱。 |
| `Description` | 裝置機型的說明。 |
| `Protocol` | 裝置使用的連線通訊協定。 可以是 `AMQP`、`MQTT` 和 `HTTP` 其中一個。 |

下列章節說明 JSON 結構描述中的其他區段：

## <a name="simulation"></a>模擬

在 `Simulation` 區段中，您定義模擬裝置的內部狀態。 裝置傳送的任何遙測值都必須是此裝置狀態的一部分。

裝置狀態的定義具有兩個元素：

* `InitialState` 會針對裝置狀態物件的所有屬性，定義初始值。
* `Script` 會識別排程執行以更新裝置狀態的 JavaScript 檔案。 您可以使用此指令碼檔案讓裝置傳送的遙測值隨機。

若要了解有關更新裝置狀態物件之 JavaScript 檔案的詳細資訊，請參閱[了解裝置機型行為](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)。

下列範例會顯示模擬 Chiller 裝置之裝置狀態物件的定義：

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

模擬服務每 5 秒執行 **chiller-01-state.js** 檔案以更新裝置狀態。 您可以在 GitHub 上的 [scripts 資料夾](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts)中查看預設模擬裝置的 JavaScript 檔案。 依照慣例，這些 JavaScript 檔案具有尾碼 **-state**，以便與實作方法行為的檔案有所區別。

## <a name="properties"></a>屬性

結構描述的 `Properties` 區段會定義裝置回報給解決方案的屬性值。 例如：

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

解決方案啟動時，它會查詢所有模擬裝置，以建置一份在 UI 中使用的 `Type` 值清單。 解決方案會使用 `Latitude` 和 `Longitude` 屬性，以將裝置的位置新增至儀表板上的地圖。

## <a name="telemetry"></a>遙測

`Telemetry` 陣列會列出模擬裝置傳送至解決方案的所有遙測類型。

下列範例會每 10 秒傳送具有電梯感應器之 `floor`、`vibration` 和 `temperature` 資料的 JSON 遙測訊息：

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` 會定義模擬裝置所傳送之 JSON 訊息的結構。 `MessageTemplate` 中的預留位置會使用語法 `${NAME}`，其中 `NAME` 是來自[裝置狀態物件](#simulation)的索引鍵。 字串應該加上引號，數字則不應該。

`MessageSchema` 會定義模擬裝置所傳送之訊息的結構描述。 訊息結構描述也會發行到 IoT 中樞，讓後端應用程式重複使用資訊以解譯內送遙測。

目前，您只能使用 JSON 訊息結構描述。 結構描述中所列的欄位可以是下列類型：

* 物件 - 使用 JSON 序列化
* 二進位 - 使用 base64 序列化
* Text
* Boolean
* 整數
* Double
* Datetime

若要在不同的間隔傳送遙測訊息，請將多個遙測類型新增至 `Telemetry` 陣列。 下列範例會每 10 秒傳送溫度和溼度資料，每一分鐘傳送光線狀態：

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

模擬裝置可以回應從 IoT 中樞呼叫的雲端到裝置方法。 裝置機型結構描述檔案中的 `CloudToDeviceMethods` 區段：

* 定義模擬裝置可以回應的方法。
* 識別 JavaScript 檔案，其中包含要執行的邏輯。

模擬裝置會將其支援的方法清單傳送給所連線的 IoT 中樞。

若要了解有關實作裝置行為之 JavaScript 檔案的詳細資訊，請參閱[了解裝置機型行為](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)。

下列範例會指定三個支援的方法和實作這些方法的 JavaScript 檔案：

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

您可以在 GitHub 上的 [scripts 資料夾](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts)中查看預設模擬裝置的 JavaScript 檔案。 依照慣例，這些 JavaScript 檔案具有尾碼 **-method**，以便與實作狀態行為的檔案有所區別。

## <a name="next-steps"></a>後續步驟

本文說明如何建立您自己的自訂模擬裝置機型。 本文示範如何：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 使用 JSON 檔案以定義模擬裝置機型
> * 指定模擬裝置的屬性
> * 指定模擬裝置傳送的遙測
> * 指定裝置回應的雲端到裝置方法

現在您已經了解 JSON 結構描述，建議的下一個步驟是了解如何[實作模擬裝置的行為](iot-accelerators-remote-monitoring-device-behavior.md)。

如需關於遠端監視解決方案的開發人員詳細資訊，請參閱：

* [開發人員參考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開發人員疑難排解指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
