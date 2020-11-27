---
title: IoT 隨插即用慣例 |Microsoft Docs
description: IoT 隨插即用的慣例描述會在傳送遙測和屬性時預期裝置使用，以及處理命令和屬性更新。
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 86c6ea9dded423e7bd513faf73adfd293f2bd38f
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302606"
---
# <a name="iot-plug-and-play-conventions"></a>IoT 隨插即用慣例

IoT 隨插即用裝置與 IoT 中樞交換訊息時，應遵循一組慣例。 IoT 隨插即用裝置使用 MQTT 通訊協定與 IoT 中樞進行通訊。

裝置可以包含 [模組](../iot-hub/iot-hub-devguide-module-twins.md)，也可以在 IoT Edge 執行時間所裝載的 [IoT Edge 模組](../iot-edge/about-iot-edge.md) 中執行。

您可以使用 [數位 Twins 定義語言 v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) _模型_，來描述 IoT 隨插即用裝置所執行的遙測、屬性和命令。 這篇文章中所參考的模型類型有兩種：

- **沒有元件** -沒有元件的模型。 此模型會將遙測、屬性和命令宣告為主要介面之 [內容] 區段中的最上層屬性。 在 Azure IoT explorer 工具中，此模型會顯示為單一 _預設元件_。
- **多個元件** -由兩個或多個介面所組成的模型。 主要介面，此介面會顯示為 _預設元件_，其中包含遙測、屬性和命令。 以其他遙測、屬性和命令宣告為元件的一或多個介面。

如需詳細資訊，請參閱 [模型中的 IoT 隨插即用元件](concepts-components.md)。

## <a name="identify-the-model"></a>識別模型

為了宣佈它所實行的模型，IoT 隨插即用裝置或模組會將模型識別碼加入至欄位，以包含 MQTT 連接封包中的模型識別碼 `model-id` `USERNAME` 。

若要識別裝置或模組所執行的模型，服務可以從下列來源取得模型識別碼：

- 裝置對應項 `modelId` 欄位。
- 數位對應項 `$metadata.$model` 欄位。
- 數位對應項變更通知。

## <a name="telemetry"></a>遙測

從無元件裝置傳送的遙測資料不需要任何額外的中繼資料。 系統會加入 `dt-dataschema` 屬性。

從多個元件裝置傳送的遙測必須新增 `$.sub` 為訊息屬性。 系統會新增 `dt-subject` 和 `dt-dataschema` 屬性。

## <a name="read-only-properties"></a>唯讀屬性

### <a name="sample-no-component-read-only-property"></a>範例無元件唯讀屬性

裝置或模組可以傳送任何遵循 DTDL v2 規則的有效 JSON。

DTDL

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

報表屬性承載範例：

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>範例多個元件唯讀屬性

裝置或模組必須新增 `{"__t": "c"}` 標記，以指出元素參考元件。

參考元件的 DTDL：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

定義元件的 DTDL：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

報表屬性承載範例：

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>可寫入屬性

裝置或模組應藉由傳送報告屬性來確認它是否收到屬性。 報告的屬性應包括：

- `value` -屬性的實際值 (通常是接收的值，但裝置可能會決定報告不同的值) 。
- `ac` -使用 HTTP 狀態碼的認可程式碼。
- `av` -參考所 `$version` 需屬性之的認可版本。 您可以在所需的屬性 JSON 承載中找到此值。
- `ad` -選擇性的確認描述。

當裝置啟動時，它應該會要求裝置對應項，並檢查是否有任何可寫入的屬性更新。 如果可寫入屬性的版本在裝置離線時增加，則裝置應該傳送回報的屬性回應，以確認它已收到更新。

當裝置第一次啟動時，如果它沒有從中樞收到初始所需的屬性，它就可以傳送報告屬性的初始值。 在此情況下，裝置應該設定 `av` 為 `1` 。 例如：

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

裝置可以使用報告屬性來提供其他資訊給中樞。 例如，裝置可能會回應一連串的進行中訊息，例如：

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

當裝置達到目標溫度時，會傳送下列訊息：

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

裝置可以報告如下的錯誤：

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>範例無元件可寫入屬性

當裝置在單一承載中收到多個報告屬性時，它可以跨多個承載傳送回報的屬性回應。

裝置或模組可以傳送任何遵循 DTDL v2 規則的有效 JSON：

DTDL

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

範例所需屬性承載：

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

範例報告的屬性第一個承載：

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

範例報告屬性第二個承載：

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>範例多個元件可寫入屬性

裝置或模組必須新增 `{"__t": "c"}` 標記，以指出元素參考元件。

標記只會傳送給元件中定義的屬性更新。 更新預設元件中定義的屬性不包含標記，請參閱 [範例無元件可寫入屬性](#sample-no-component-writable-property)

當裝置在單一承載中收到多個報告屬性時，它可以跨多個承載傳送回報的屬性回應。

裝置或模組應藉由傳送報告屬性來確認它是否收到屬性：

參考元件的 DTDL：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

定義元件的 DTDL：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

範例所需屬性承載：

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

範例報告的屬性第一個承載：

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

範例報告屬性第二個承載：

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>命令

沒有任何元件介面使用命令名稱，不含前置詞。

在裝置或模組上，多個元件介面會使用具有下列格式的命令名稱： `componentName*commandName` 。

## <a name="next-steps"></a>後續步驟

現在您已瞭解 IoT 隨插即用慣例，以下是一些額外的資源：

- [數位分身定義語言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 裝置 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [模型元件](./concepts-components.md)