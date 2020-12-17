---
title: 如何管理 IoT 隨插即用數位 twins
description: 如何使用數位對應項 Api 管理 IoT 隨插即用裝置
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ddb8027c145f6a38bfcd953be66dae2943a20c3a
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654603"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>管理 IoT 隨插即用數位 twins

IoT 隨插即用支援 **取得數位** 對應項和 **更新數位** 對應項作業，以管理數位 twins。 您可以使用 [REST api](/rest/api/iothub/service/digitaltwin) 或其中一個 [服務 sdk](libraries-sdks.md)。

在撰寫本文時，數位對應項 API 版本是 `2020-09-30` 。

## <a name="update-a-digital-twin"></a>更新數位分身

IoT 隨插即用裝置會執行 [數位 Twins 定義語言 v2 ](https://github.com/Azure/opendigitaltwins-dtdl)所描述的模型， (DTDL) 。 解決方案開發人員可以使用 **更新數位** 對應項 API 來更新元件的狀態和數位對應項的屬性。

本文章中用來作為範例的 IoT 隨插即用裝置，會使用[控溫器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)元件來實行[溫度控制器模型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)。

下列程式碼片段顯示格式化為 JSON 物件之「 **取得數位** 對應項」要求的回應。 若要深入瞭解數位對應項的格式，請參閱 [瞭解 IoT 隨插即用數位 twins](./concepts-digital-twin.md#digital-twin-example)：

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

數位 twins 可讓您使用 [JSON 修補程式](http://jsonpatch.com/)來更新整個元件或屬性。

例如，您可以更新屬性，如下所示 `targetTemperature` ：

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

先前的更新會在對應的元件層級中設定所需的屬性值， `$metadata` 如下列程式碼片段所示。 IoT 中樞會更新所需的屬性版本：

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>新增、取代或移除元件

元件層級作業需要值內的空白物件 `$metadata` 標記。

[新增或取代元件] 作業會設定所有所提供屬性的所需值。 它也會針對未隨更新提供的任何可寫入屬性清除所需的值。

移除元件會清除所有現有可寫入屬性的所需值。 裝置最後會將此移除同步處理，並停止報告個別的屬性。 然後，會從數位對應項中移除該元件。

下列 JSON Patch 範例顯示如何新增、取代或移除元件：

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>新增、取代或移除屬性

新增或取代作業會設定所需的屬性值。 裝置可同步處理狀態，並報告值的更新，以及程式 `ack` 代碼、版本和描述。

如果已設定屬性，則移除屬性會清除所需的屬性值。 裝置接著可以停止報告此屬性，並將其從元件中移除。 如果這個屬性是元件中的最後一個屬性，則也會移除該元件。

下列 JSON Patch 範例示範如何新增、取代或移除元件中的屬性：

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>設定數位對應項屬性所需值的規則

**名稱**

元件或屬性的名稱必須是有效的 DTDL v2 名稱。

允許的字元為 a-z、a-z、0-9 (不是第一個字元) ，而底線 (不是第一個或最後一個字元) 。

名稱長度可以是1-64 個字元。

**屬性值**

值必須是有效的 [DTDL V2 屬性](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)。

支援所有基本類型。 在複雜類型中，支援列舉、對應和物件。 若要深入瞭解，請參閱 [DTDL V2 架構](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)。

屬性不支援陣列或具有陣列的任何複雜架構。

複雜物件支援五個層級的最大深度。

複雜物件內的所有功能變數名稱都必須是有效的 DTDL v2 名稱。

所有對應索引鍵都必須是有效的 DTDL v2 名稱。

## <a name="troubleshoot-update-digital-twin-api-errors"></a>針對更新數位對應項 API 錯誤進行疑難排解

數位對應項 API 會擲回下列一般錯誤訊息：

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

如果您看到此錯誤，請確定更新修補程式遵循 [設定數位對應項屬性所需值的規則](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

當您更新元件時，請確定已設定 [空的物件 $metadata 標記](#add-replace-or-remove-a-component) 。

如果裝置的回報值不符合 [IoT 隨插即用慣例](./concepts-convention.md#writable-properties)，更新可能會失敗。

## <a name="next-steps"></a>後續步驟

現在您已瞭解數位 twins，以下是一些額外的資源：

- [從您的解決方案與裝置互動](quickstart-service.md)
- [IoT 數位對應項 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 總管](howto-use-iot-explorer.md)
