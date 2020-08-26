---
title: IoT 隨插即用慣例 |Microsoft Docs
description: IoT 隨插即用的慣例描述會在傳送遙測和屬性時預期裝置使用，以及處理命令和屬性更新。
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef29be53e776c4c185ac8430b3340c53ca85d855
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856047"
---
# <a name="iot-plug-and-play-conventions"></a>IoT 隨插即用慣例

IoT 隨插即用預覽裝置在與 IoT 中樞交換訊息時，應遵循一組慣例。 IoT 隨插即用 Preview 裝置會使用 MQTT 通訊協定與 IoT 中樞進行通訊。

您可以使用 [數位 Twins 定義語言 v2 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl) _模型_，來描述 IoT 隨插即用裝置所執行的遙測、屬性和命令。 這篇文章中所參考的模型類型有兩種：

- **沒有元件** -沒有元件的模型。 此模型會將遙測、屬性和命令宣告為主要介面之 [內容] 區段中的最上層屬性。
- **多個元件** -由兩個或多個介面所組成的模型。 具有遙測、屬性和命令的主要介面。 以其他遙測、屬性和命令宣告為元件的一或多個介面。

如需詳細資訊，請參閱 [模型中的 IoT 隨插即用元件](concepts-components.md)。

## <a name="identify-the-model"></a>識別模型

為了宣佈它所實行的模型，IoT 隨插即用裝置會在欄位中加入 MQTT 連接封包中的模型識別碼 `model-id` `USERNAME` 。

若要識別裝置所執行的模型，服務可以從下列來源取得模型識別碼：

- 裝置對應項 `modelId` 欄位。
- 數位對應項 `$metadata.$model` 欄位。
- 數位對應項變更通知。

## <a name="telemetry"></a>遙測

從無元件裝置傳送的遙測資料不需要任何額外的中繼資料。 系統會加入 `dt-dataschema` 屬性。

從多個元件裝置傳送的遙測必須新增 `$.sub` 為訊息屬性。 系統會新增 `dt-subject` 和 `dt-dataschema` 屬性。

## <a name="read-only-properties"></a>唯讀屬性

### <a name="sample-no-component-read-only-property"></a>範例無元件唯讀屬性

裝置可以傳送任何遵循 DTDL v2 規則的有效 JSON。

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **範例承載**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>範例多個元件唯讀屬性

裝置必須新增 `{"__t": "c"}` 標記，以指出元素參考元件。

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **報告屬性**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>可寫入屬性

裝置應該藉由傳送報告屬性來確認它收到屬性。 報告的屬性應包括：

- `value` -屬性的實際值 (通常是接收的值，但裝置可能會決定報告不同的值) 。
- `ac` -使用 HTTP 狀態碼的認可程式碼。
- `av` -參考所 `$version` 需屬性之的認可版本。
- `ad` -選擇性的確認描述。

### <a name="sample-no-component-writable-property"></a>範例無元件可寫入屬性

裝置可以傳送任何遵循 DTDL v2 規則的有效 JSON：

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **所需屬性**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **報告屬性**

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
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>範例多個元件可寫入屬性

裝置必須新增 `{"__t": "c"}` 標記，以指出元素參考元件。

標記只會傳送給元件層級更新，因此裝置不得檢查此旗標。

裝置應該藉由傳送報告屬性來確認它收到屬性：

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **所需屬性**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **報告屬性**

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
   :::column-end:::
:::row-end:::

## <a name="commands"></a>命令

沒有任何元件介面使用命令名稱，不含前置詞。

在裝置上，多個元件介面會使用具有下列格式的命令名稱： `componentName*commandName` 。

## <a name="next-steps"></a>後續步驟

現在您已瞭解 IoT 隨插即用慣例，以下是一些額外的資源：

- [數位分身定義語言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [模型元件](./concepts-components.md)
