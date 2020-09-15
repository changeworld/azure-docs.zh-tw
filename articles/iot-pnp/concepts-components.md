---
title: 瞭解 IoT 隨插即用模型中的元件 |Microsoft Docs
description: 瞭解使用未使用元件的元件和模型 IoT 隨插即用 DTDL 模型之間的差異。
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c4a32a5c929e74332e85ceb6f4cff787e237e385
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069640"
---
# <a name="iot-plug-and-play-components-in-models"></a>模型中的 IoT 隨插即用元件

在 IoT 隨插即用慣例中，如果裝置在連線到 IoT 中樞時，會顯示其數位 twins 定義語言 (DTDL) 模型識別碼，則裝置會是 IoT 隨插即用裝置。

下列程式碼片段顯示一些範例模型識別碼：

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>沒有元件

簡單的模型不會使用內嵌或串聯的元件。 它包含用來定義遙測、屬性和命令的標頭資訊和內容區段。

下列範例顯示不使用元件之簡單模型的一部分：

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

雖然模型並未明確定義元件，但它的行為就像是具有所有遙測、屬性和命令定義的單一元件。

下列螢幕擷取畫面顯示如何在 Azure IoT explorer 工具中顯示模型：

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Azure IoT explorer 中的預設元件":::

模型識別碼會儲存在裝置對應項屬性中，如下列螢幕擷取畫面所示：

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="數位對應項屬性中的模型識別碼":::

沒有元件的 DTDL 模型對於具有單一遙測、屬性和命令集的裝置而言，是很有用的簡化方式。 不使用元件的模型可讓您輕鬆地將現有的裝置遷移成為 IoT 隨插即用裝置-您可以建立 DTDL 模型來描述實際的裝置，而不需要定義任何元件。

## <a name="multiple-components"></a>多個元件

元件可讓您將模型介面建立成其他介面的元件。

例如， [控溫器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 介面會定義為模型。 當您定義 [溫度控制器模型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)時，您可以將此介面併入一或多個元件。 在下列範例中，這些元件稱為 `thermostat1` 和 `thermostat2` 。

針對具有多個元件的 DTDL 模型，有兩個或多個元件區段。 每個區段都已 `@type` 設定為 `Component` 並明確參考架構，如下列程式碼片段所示：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
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

此模型有三個定義于 [內容] 區段中的元件：兩個 `Thermostat` 元件和一個 `DeviceInformation` 元件。 另外還有一個預設的根元件。

## <a name="next-steps"></a>後續步驟

現在您已瞭解模型元件，以下是一些額外的資源：

- [數位 Twins 定義語言 v2 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl)
- [模型存放庫](./concepts-model-repository.md)