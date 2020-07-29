---
title: 瞭解 IoT 隨插即用模型中的元件 |Microsoft Docs
description: 瞭解 IoT 隨插即用使用不使用元件之元件和模型的 DTDL 模型之間的差異。
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c41edc477460e6d239688aafe6d7219bed36cd4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352204"
---
# <a name="iot-plug-and-play-components-in-models"></a>模型中的 IoT 隨插即用元件

在 IoT 隨插即用慣例中，當裝置連線到 IoT 中樞時，它會顯示其數位 twins 定義語言（DTDL）模型識別碼，這是 IoT 隨插即用裝置。

下列程式碼片段顯示一些範例模型識別碼：

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>沒有元件

簡單的模型不會使用內嵌或串聯的元件。 其中包含標頭資訊和內容區段，以定義遙測、屬性和命令。

下列範例會顯示不使用元件之簡單模型的一部分：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

雖然模型並未明確定義元件，但其行為就像是具有所有遙測、屬性和命令定義的單一元件。

下列螢幕擷取畫面顯示模型在 Azure IoT explorer 工具中的顯示方式：

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Azure IoT explorer 中的預設元件":::

模型識別碼會儲存在裝置對應項屬性中，如下列螢幕擷取畫面所示：

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="數位對應項屬性中的模型識別碼":::

具有一組遙測、屬性和命令的裝置可簡化 DTDL 模型（不含元件）。 不使用元件的模型可讓您輕鬆地將現有的裝置遷移為 IoT 隨插即用裝置，您可以建立 DTDL 模型來描述您的實際裝置，而不需要定義任何元件。

## <a name="multiple-components"></a>多個元件

元件可讓您建立模型介面，做為其他介面的元件。

例如，[控溫器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)介面會定義為模型。 當您定義[溫度控制器模型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)時，可以將此介面併入一或多個元件。 在下列範例中，這些元件稱為 `thermostat1` 和 `thermostat2` 。

針對具有多個元件的 DTDL 模型，有兩個或多個元件區段。 每個區段都 `@type` 設定為 `Component` ，並明確參考架構，如下列程式碼片段所示：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
... 
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

此模型在 [內容] 區段中定義了三個元件-兩個 `Thermostat` 元件和一個 `DeviceInformation` 元件。 另外還有一個預設的根元件。

## <a name="next-steps"></a>後續步驟

既然您已瞭解模型元件，以下是一些額外的資源：

- [數位 Twins 定義語言 v2 （DTDL）](https://github.com/Azure/opendigitaltwins-dtdl)
- [模型存放庫](./concepts-model-repository.md)