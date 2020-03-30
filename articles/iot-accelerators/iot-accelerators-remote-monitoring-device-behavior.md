---
title: 遠端監視解決方案中的類比設備 - Azure |微軟文檔
description: 本文說明如何使用 JavaScript 定義模擬裝置在遠端監視解決方案中的行為。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890853"
---
# <a name="implement-the-device-model-behavior"></a>實作裝置模型行為

[了解裝置模型結構描述](iot-accelerators-remote-monitoring-device-schema.md)一文說明了定義模擬裝置模型的結構描述。 該文參考兩種類型的 JavaScript 檔案，這兩種類型都可以實作模擬裝置的行為：

- **州**以固定間隔運行以更新設備內部狀態的 JavaScript 檔。
- **方法**JavaScript 檔，在解決方案調用設備上的方法時運行。

> [!NOTE]
> 裝置模型行為只適用於裝載於裝置模擬服務中的模擬裝置。 如果您想要建立真實裝置，請參閱[將裝置連線到遠端監視解決方案加速器](iot-accelerators-connecting-devices.md)。

在本文中，您將學會如何：

>[!div class="checklist"]
> * 控制模擬裝置的狀態
> * 定義模擬裝置從遠端監視解決方案回應方法呼叫的方式
> * 為指令碼偵錯

## <a name="state-behavior"></a>狀態行為

裝置模型結構描述的 [模擬](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) 區段會定義模擬裝置的內部狀態：

- `InitialState` 會針對裝置狀態物件的所有屬性，定義初始值。
- `Script` 會識別排程執行以更新裝置狀態的 JavaScript 檔案。

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
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

模擬裝置的狀態 (如 `InitialState` 區段中所定義) 會由模擬服務保留在記憶體中。 狀態資訊會當作輸入，傳遞至 **chiller-01-state.js** 中定義的 `main` 函式。 在此範例中，模擬服務會每 5 秒執行 **chiller-01-state.js** 檔案一次。 指令碼可以修改模擬裝置的狀態。

以下示範典型 `main` 函式的外框：

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context` 參數具有下列屬性：

- `currentTime`，作為格式為 `yyyy-MM-dd'T'HH:mm:sszzz` 的字串
- `deviceId`，例如 `Simulated.Chiller.123`
- `deviceModel`，例如 `Chiller`

`state` 參數包含裝置的狀態，如裝置模擬服務中所維護。 此值是先前對 `main` 的呼叫所傳回的 `state` 物件。

下列範例示範 `main` 方法的典型實作，以處理模擬服務所維護的裝置狀態：

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

下列範例會示範 `main` 方法如何模擬隨著時間而變化的遙測值：

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

您可以在 GitHub 上檢視完整的 [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js)。

## <a name="method-behavior"></a>方法行為

裝置模型結構描述的 [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) 區段會定義模擬裝置回應的方法。

下列範例會示範模擬的 Chiller 裝置所支援的方法清單：

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
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

每個方法都有實作方法行為的相關聯 JavaScript 檔案。

模擬裝置的狀態 (如結構描述的 `InitialState` 區段中所定義) 會由模擬服務保留在記憶體中。 呼叫方法時，狀態資訊會當作輸入，傳遞至 JavaScript 檔案中定義的 `main` 函式。 指令碼可以修改模擬裝置的狀態。

以下示範典型 `main` 函式的外框：

```javascript
function main(context, previousState, previousProperties) {

}
```

`context` 參數具有下列屬性：

- `currentTime`，作為格式為 `yyyy-MM-dd'T'HH:mm:sszzz` 的字串
- `deviceId`，例如 `Simulated.Chiller.123`
- `deviceModel`，例如 `Chiller`

`state` 參數包含裝置的狀態，如裝置模擬服務中所維護。

`properties` 參數包含會以回報屬性的形式寫入至「IoT 中樞」裝置對應項的裝置屬性。

有三個全域函式可以用來協助實作此方法的行為：

- `updateState` 可更新模擬服務所保留的狀態。
- `updateProperty` 可更新單一裝置屬性。
- `sleep` 可暫停執行，以模擬長時間執行的工作。

下列範例顯示模擬的 Chiller 裝置所使用的縮寫版 **IncreasePressure method.js** 指令碼：

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>為指令碼檔案偵錯

您無法將偵錯工具附加至裝置模擬服務所使用的 Javascript 解譯器以執行狀態和方法指令碼。 不過，您可以在服務記錄檔中記錄資訊。 內建的 `log()` 函式可讓您儲存資訊以追蹤並偵錯函式的執行。

如果有語法錯誤，則解譯器會失敗，並將 `Jint.Runtime.JavaScriptException` 項目寫入至服務記錄檔。

GitHub 上的[在本機執行服務](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) \(英文\) 一文說明如何在本機執行裝置模擬服務。 在本機執行服務可以更輕鬆地為您模擬的裝置偵錯，再將其部署至雲端。

## <a name="next-steps"></a>後續步驟

本文說明如何定義您自己的自訂模擬裝置模型行為。 本文示範如何：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 控制模擬裝置的狀態
> * 定義模擬裝置從遠端監視解決方案回應方法呼叫的方式
> * 為指令碼偵錯

現在您已經學會如何指定模擬裝置的行為，建議的下一個步驟是了解如何[建立模擬的裝置](iot-accelerators-remote-monitoring-create-simulated-device.md)。

如需關於遠端監視解決方案的開發人員詳細資訊，請參閱：

* [開發人員參考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開發人員疑難排解指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
