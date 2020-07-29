---
title: IoT 隨插即用慣例 |Microsoft Docs
description: IoT 隨插即用在傳送遙測和屬性時預期要使用的慣例，以及處理命令和屬性更新的說明。
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b06edb83e85ef6d56cf8bf8f91551fe99535eba1
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352183"
---
# <a name="iot-plug-and-play-conventions"></a>IoT 隨插即用慣例

IoT 隨插即用預覽裝置在與 IoT 中樞交換訊息時，應遵循一組慣例。 IoT 隨插即用預覽裝置會使用 MQTT 通訊協定來與 IoT 中樞通訊。

您可以使用[數位 Twins 定義語言 v2 （DTDL）](https://github.com/Azure/opendigitaltwins-dtdl) _模型_，描述 IoT 隨插即用裝置所執行的遙測、屬性和命令。 這篇文章中所參考的模型有兩種類型：

- **無元件**-不含元件的模型。 此模型會將遙測、屬性和命令宣告為主要介面內容區段中的最上層屬性。
- **多個元件**-由兩個或多個介面組成的模型。 具有遙測、屬性和命令的主要介面。 一或多個介面宣告為具有其他遙測、屬性和命令的元件。

如需詳細資訊，請參閱[IoT 隨插即用模型中的元件](concepts-components.md)。

## <a name="model-discovery"></a>模型探索

為了宣佈它所執行的模型，IoT 隨插即用裝置藉由將加入至欄位，在 MQTT 連線封包中包含模型識別碼 `model-id` `USERNAME` 。

若要探索裝置所實行的模型，服務可以從下列來源取得模型識別碼：

- 裝置對應項 `modelId` 欄位。
- 數位對應項 `$metadata.$model` 欄位。
- 數位對應項變更通知。

## <a name="telemetry"></a>遙測

從「無」元件裝置傳送的遙測不需要任何額外的中繼資料。 系統會新增 `dt-dataschema` 屬性。

從多個元件裝置傳送的遙測必須新增 `$.sub` 為訊息屬性。 系統會新增 `dt-subject` 和 `dt-dataschema` 屬性。

## <a name="read-only-properties"></a>唯讀屬性

### <a name="sample-no-component-read-only-property"></a>範例無元件唯讀屬性

裝置可以傳送遵循 DTDL v2 規則的任何有效 JSON。

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

裝置必須加入 `{"__t": "c"}` 標記，以指出元素參考元件。

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

裝置應該藉由傳送報告屬性來確認它是否收到屬性。 報告屬性應包含：

- `value`-裝置接收的值。
- `ac`-使用 HTTP 狀態碼的認可程式碼。
- `av`-參考所 `$version` 需屬性之的認可版本。
- `ad`-選擇性的確認描述。

### <a name="sample-no-component-writable-property"></a>範例沒有可寫入的元件屬性

裝置可以傳送遵循 DTDL v2 規則的任何有效 JSON：

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

裝置必須加入 `{"__t": "c"}` 標記，以指出元素參考元件。

標記只會針對元件層級更新傳送，因此裝置會不得檢查此旗標。

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

沒有任何元件介面使用沒有前置詞的命令名稱。

在裝置上，多個元件介面會使用具有下列格式的命令名稱： `componentName*commandName` 。

## <a name="next-steps"></a>後續步驟

既然您已瞭解 IoT 隨插即用慣例，以下是一些額外的資源：

- [數位 Twins 定義語言（DTDL）](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [模型元件](./concepts-components.md)
