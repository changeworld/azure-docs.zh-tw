---
title: 建立進階的模擬裝置型號 - Azure |Microsoft Docs
description: 在本操作指南中，您會了解如何建立進階的裝置型號，以搭配裝置模擬解決方案加速器使用。
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: c568dddcbbf57ebd6ed5906bb83af01a84dafa41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683834"
---
# <a name="create-an-advanced-device-model"></a>建立進階裝置型號

本操作指南說明定義自訂裝置型號的 JSON 和 JavaScript 檔案。 本文包含一些範例裝置型號定義檔案，並示範如何將它們上傳至裝置模擬執行個體。 您可以建立進階裝置型號，以模擬更真實的裝置行為進行測試。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

若要依照本操作指南中的步驟進行，您在 Azure 訂用帳戶中必須要有一個已部署的裝置模擬執行個體。

如果您尚未部署裝置模擬，您應先完成[在 Azure 中部署和執行 IoT 裝置模擬](quickstart-device-simulation-deploy.md)快速入門。

### <a name="open-device-simulation"></a>開啟裝置模擬

若要在您的瀏覽器中執行裝置模擬，請先瀏覽至 [Microsoft Azure IoT 解決方案加速器](https://www.azureiotsolutions.com)。

系統可能會要求您使用 Azure 訂用帳戶認證來登入。

接著，請針對[在 Azure 中部署和執行 IoT 裝置模擬](quickstart-device-simulation-deploy.md)快速入門中所部署的裝置模擬，按一下其圖格上的 [啟動]。

## <a name="device-models"></a>裝置型號

每個模擬裝置所屬的特定裝置型號都會定義模擬行為。 此行為包括傳送遙測資料的頻率、傳送何種訊息，以及支援的方法。

您可以使用 JSON 裝置定義檔案和一組 JavaScript 檔案，定義裝置型號。 這些 JavaScript 檔案會定義模擬行為，例如隨機的遙測資料與方法邏輯。

一般裝置型號會有：

* 每個裝置型號都有一個 JSON 檔案 (例如，elevator.json)。
* 每個裝置型號都有一個 JavaScript 行為指令碼檔案 (例如，elevator-state.js)
* 每個裝置方法都有一個 JavaScript 方法指令碼檔案 (例如，elevator-go-down.js)

> [!NOTE]
> 並非所有裝置型號都會定義方法。 因此裝置型號不一定會有方法指令碼。 不過，所有裝置型號一定會有行為指令碼。

## <a name="device-definition-file"></a>裝置定義檔案

每個裝置定義檔案包含模擬裝置型號的詳細資訊，包括下列資訊：

* 裝置型號名稱：字串。
* 通訊協定：AMQP | MQTT | HTTP。
* 初始裝置狀態。
* 重新整理裝置狀態的頻率。
* 要用來重新整理裝置狀態的 JavaScript 檔案。
* 要傳送的遙測訊息清單，各訊息都有特定的頻率。
* 遙測訊息的結構描述，後端應用程式會用來剖析所接收的遙測資料。
* 支援的方法清單和用以模擬每個方法的 JavaScript 檔案。

### <a name="file-schema"></a>檔案結構描述

結構描述版本一律為 "1.0.0" 且為此檔案格式專屬：

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>裝置型號描述

下列屬性會描述裝置型號。 每個類型都有唯一識別碼、語意版本、名稱及描述：

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT 通訊協定

IoT 裝置可以使用不同的通訊協定來連線。 模擬可讓您使用 **AMQP**、**MQTT** 或 **HTTP**：

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>模擬裝置狀態

每個模擬裝置都必須定義內部的狀態。 該狀態也會定義可在遙測資料中報告的屬性。 例如，冰水主機可能會有如下的初始狀態：

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

有數個感應器的移動裝置可能會有更多屬性，例如：

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

模擬服務會將裝置狀態保留在記憶體中，並提供為 JavaScript 函式的輸入。 JavaScript 函式可決定要：

* 略過狀態並產生一些隨機資料。
* 以特定案例的真實方式更新狀態狀態。

產生狀態的函式也會接收以下輸入：

* 裝置識別碼。
* 裝置型號。
* 目前的時間。 此值可以依據裝置和時間產生不同的資料。

### <a name="generating-telemetry-messages"></a>產生遙測訊息

模擬服務可為每部裝置傳送數種遙測類型。 一般而言，遙測資料包含裝置狀態的資料。 例如，模擬的空間可能會每隔 10 秒傳送有關溫度和溼度的資訊。 請注意，下列程式碼片段中的預留位置會自動以裝置狀態的值取代：

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
    }
],
```

預留位置使用特殊語法 **${NAME}** ，其中 **NAME** 是 JavaScript **main** 函式所傳回裝置狀態物件的金鑰。 字串應該加上引號，而數字則不應該。

#### <a name="message-schema"></a>訊息結構描述

每個訊息類型都必須要有定義完善的結構描述。 訊息結構描述也會發行到 IoT 中樞，以讓後端應用程式能重複使用資訊以解譯內送遙測。

結構描述支援 JSON 格式，其能跨數個系統和服務輕鬆進行剖析、轉換及分析。

結構描述中所列的欄位可以是下列類型：

* 物件 - 使用 JSON 序列化
* 二進位 - 使用 base64 序列化
* Text
* Boolean
* 整數
* Double
* Datetime

### <a name="supported-methods"></a>支援的方法

模擬裝置還可因應方法呼叫，執行某些邏輯並提供某些回應。 類似於模擬，方法邏輯儲存在 JavaScript 檔案中，並可以與裝置狀態互動。 例如：

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>建立裝置定義檔案

在此操作指南中，您會了解如何建立無人機的裝置型號。 無人機會變更位置和高度，在初始座標附近隨意飛行。

將下列 JSON 複製到文字編輯器，並儲存為 **drone.json**。

### <a name="device-definition-json-example"></a>裝置定義 JSON 範例

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>行為指令碼檔案

行為指令碼檔案中可移動無人機的程式碼。 指令碼會操縱記憶體狀態中裝置的高度和位置，來改變無人機的高度和位置。

JavaScript 檔案必須要有 **elevation** 函式，接受以下兩個參數：

* **context** 物件，其包含下列三個屬性：
    * **currentTime**，其為 **yyyy-MM-dd'T'HH:mm:sszzz** 格式的字串。
    * **deviceId**。 例如，**Simulated.Elevator.123**。
    * **deviceModel**。 例如，**電梯**。
* **state** 物件，其為先前呼叫中的函式所傳回的值。 模擬服務會維護此裝置狀態，並用來產生遙測訊息。

**main** 函式傳回新的裝置狀態。 例如：

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>建立行為指令碼檔案

將下列 JavaScript 複製到文字編輯器，並儲存為 **drone-state.js**。

### <a name="device-model-javascript-simulation-example"></a>裝置型號 JavaScript 模擬範例

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>建立方法指令碼檔案

方法指令碼類似於行為指令碼。 定義在呼叫特定雲端到裝置方法時的裝置行為。

無人機重新叫用指令碼會將無人機的座標設定在固定點，以模擬無人機返航。

將下列 JavaScript 複製到文字編輯器，並儲存為 **droneRecall-method.js**。

### <a name="device-model-javascript-simulation-example"></a>裝置型號 JavaScript 模擬範例

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>為指令碼檔案偵錯

雖然您無法將偵錯工具附加到執行中的行為檔案，但可以使用 **log** 函式將資訊寫入服務記錄檔。 如有語法錯誤，解譯器就會失敗，並將例外狀況的相關資訊寫入記錄檔中。

記錄範例：

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>部署進階裝置型號

若要部署進階裝置型號，您可將檔案上傳到裝置模擬執行個體：

選取功能表列中的 [裝置型號]。 [裝置型號] 頁面會列出此裝置模擬執行個體中可用的裝置型號：

![裝置型號](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

按一下頁面右上角的 [+ 新增裝置型號]：

![新增裝置型號](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

按一下 [進階]，開啟進階裝置型號索引標籤：

![進階索引標籤](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

按一下 [瀏覽]，然後選取您建立的 JSON 與 JavaScript 檔案。 請務必三個檔案全選。 如果遺漏任一個檔案，驗證都會失敗：

![瀏覽檔案](media/iot-accelerators-device-simulation-advanced-device/browse.png)

如果檔案通過驗證，按一下 [儲存]，即準備好可在模擬中使用您的裝置型號。 否則，請修正任何錯誤，然後重新上傳檔案：

![儲存](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>後續步驟

在本操作指南中，您會了解在裝置模擬中使用的裝置型號檔案，以如何建立進階裝置型號。 接下來，您可能想要探索如何[使用時間序列深入分析將從裝置模擬解決方案加速器傳送的遙測資料視覺化](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights)。
