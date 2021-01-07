---
title: Azure IoT Central 中的遙測、屬性和命令承載 |Microsoft Docs
description: Azure IoT Central 裝置範本可讓您指定裝置的遙測、屬性和命令必須執行。 瞭解裝置可與 IoT Central 交換的資料格式。
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 87fb7f0eb4017a39aca081f73de543a67400d4b5
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97969056"
---
# <a name="telemetry-property-and-command-payloads"></a>遙測、屬性和命令承載

_本文適用對象為裝置開發人員。_

Azure IoT Central 中的裝置範本是定義下列各項的藍圖：

* 裝置傳送給 IoT Central 的遙測。
* 裝置與 IoT Central 同步處理的屬性。
* IoT Central 在裝置上呼叫的命令。

本文針對裝置開發人員說明裝置範本中定義的遙測、屬性和命令所傳送和接收的 JSON 承載。

本文不會說明每個可能的遙測、屬性和命令裝載類型，但這些範例會說明所有金鑰類型。

每個範例都會顯示裝置模型中的程式碼片段，以定義型別和範例 JSON 承載，以說明裝置應該如何與 IoT Central 應用程式互動。

> [!NOTE]
> IoT Central 接受任何有效的 JSON，但如果符合裝置模型中的定義，則只能用於視覺效果。 您可以匯出不符合定義的資料，請參閱 [將 IoT 資料匯出至 Azure 中的目的地](howto-export-data.md)。

定義裝置型號的 JSON 檔案會使用數位對應項 [定義語言 (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。

如需顯示使用中部分承載的範例裝置程式碼，請參閱 [建立並將用戶端應用程式連線到您的 Azure IoT Central 應用程式](tutorial-connect-device.md) 教學課程。

## <a name="view-raw-data"></a>檢視未經處理資料

IoT Central 可讓您查看裝置傳送至應用程式的原始資料。 此視圖適用于針對從裝置傳送的承載問題進行疑難排解。 若要查看裝置正在傳送的原始資料：

1. 從 [ **裝置** ] 頁面流覽至裝置。

1. 選取 [ **原始資料** ] 索引標籤：

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="未經處理資料檢視":::

    在此檢視中，您可以選取要顯示的資料行，並設定要檢視的時間範圍。 [未模型化的資料] 資料行會顯示裝置內不符合裝置範本中任何屬性或遙測定義的資料。

## <a name="telemetry"></a>遙測

### <a name="primitive-types"></a>基本類型

本節顯示裝置串流至 IoT Central 應用程式的基本遙測類型範例。

下列來自裝置型號的程式碼片段會顯示 `boolean` 遙測類型的定義：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

裝置用戶端應將遙測以 JSON 形式傳送，如下列範例所示：

```json
{ "BooleanTelemetry": true }
```

下列來自裝置型號的程式碼片段會顯示 `string` 遙測類型的定義：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

裝置用戶端應將遙測以 JSON 形式傳送，如下列範例所示：

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

下列來自裝置型號的程式碼片段會顯示 `integer` 遙測類型的定義：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

裝置用戶端應將遙測以 JSON 形式傳送，如下列範例所示：

```json
{ "IntegerTelemetry": 23 }
```

下列來自裝置型號的程式碼片段會顯示 `double` 遙測類型的定義：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

裝置用戶端應將遙測以 JSON 形式傳送，如下列範例所示：

```json
{ "DoubleTelemetry": 56.78 }
```

下列來自裝置型號的程式碼片段會顯示 `dateTime` 遙測類型的定義：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

裝置用戶端應將遙測以 JSON 格式傳送，如下列範例所示- `DateTime` 類型必須為 ISO 8061 格式：

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

下列來自裝置型號的程式碼片段會顯示 `duration` 遙測類型的定義：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

裝置用戶端應將遙測以 JSON 形式傳送，如下列範例所示-持續時間必須為 ISO 8601 格式：

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>複雜類型

本節顯示裝置串流至 IoT Central 應用程式的複雜遙測類型範例。

下列來自裝置型號的程式碼片段會顯示 `geopoint` 遙測類型的定義：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

裝置用戶端應將遙測以 JSON 形式傳送，如下列範例所示。 IoT Central 將值顯示為地圖上的圖釘：

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

下列來自裝置型號的程式碼片段會顯示 `Enum` 遙測類型的定義：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

裝置用戶端應將遙測以 JSON 形式傳送，如下列範例所示。 可能的值 `0` `1` 為、和， `2` 在 IoT Central 中會顯示為 `Item1` 、 `Item2` 和 `Item3` ：

```json
{ "EnumTelemetry": 1 }
```

來自裝置型號的下列程式碼片段會顯示 `Object` 遙測類型的定義。 此物件有三個具有類型 `dateTime` 、 `integer` 和的欄位 `Enum` ：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

裝置用戶端應將遙測以 JSON 形式傳送，如下列範例所示。 `DateTime` 類型必須符合 ISO 8061 規範。 的可能值為、和， `Property3` `0` `1` 在 IoT Central 中會顯示為 `Item1` 、 `Item2` 和 `Item3` ：

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

下列來自裝置型號的程式碼片段會顯示 `vector` 遙測類型的定義：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

裝置用戶端應將遙測以 JSON 形式傳送，如下列範例所示：

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>事件和狀態類型

本節將說明裝置傳送至 IoT Central 應用程式的遙測事件和狀態範例。

下列來自裝置模型的程式碼片段會顯示 `integer` 事件種類的定義：

```json
{
  "@type": [
    "Telemetry",
    "Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

裝置用戶端應以 JSON 形式傳送事件資料，如下列範例所示：

```json
{ "IntegerEvent": 74 }
```

下列來自裝置模型的程式碼片段會顯示 `integer` 狀態類型的定義：

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

裝置用戶端應以 JSON 形式傳送狀態，如下列範例所示。 可能的整數狀態值為 `1` 、 `2` 或 `3` ：

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>[內容]

> [!NOTE]
> 屬性的裝載格式適用于在07/14/2020 或之後建立的應用程式。

### <a name="primitive-types"></a>基本類型

本節將說明裝置傳送至 IoT Central 應用程式的基本屬性類型範例。

下列來自裝置模型的程式碼片段會顯示 `boolean` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

裝置用戶端應該傳送看起來像下列範例的 JSON 承載，作為裝置對應項中的報告屬性：

```json
{ "BooleanProperty": false }
```

下列來自裝置模型的程式碼片段會顯示 `boolean` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

裝置用戶端應該傳送看起來像下列範例的 JSON 承載，作為裝置對應項中的報告屬性：

```json
{ "LongProperty": 439 }
```

下列來自裝置模型的程式碼片段會顯示 `date` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

裝置用戶端應該傳送看起來像下列範例的 JSON 承載，作為裝置對應項中的報告屬性。 `Date` 類型必須符合 ISO 8061 規範：

```json
{ "DateProperty": "2020-05-17" }
```

下列來自裝置模型的程式碼片段會顯示 `duration` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

裝置用戶端應該傳送如下列範例所示的 JSON 承載，作為裝置對應項中的報告屬性-Duration 必須符合 ISO 8601 持續時間規範：

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

下列來自裝置模型的程式碼片段會顯示 `float` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

裝置用戶端應該傳送看起來像下列範例的 JSON 承載，作為裝置對應項中的報告屬性：

```json
{ "FloatProperty": 1.9 }
```

下列來自裝置模型的程式碼片段會顯示 `string` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

裝置用戶端應該傳送看起來像下列範例的 JSON 承載，作為裝置對應項中的報告屬性：

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>複雜類型

本節將說明裝置傳送至 IoT Central 應用程式的複雜屬性類型範例。

下列來自裝置模型的程式碼片段會顯示 `geopoint` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
}
```

裝置用戶端應該傳送看起來像下列範例的 JSON 承載，作為裝置對應項中的報告屬性：

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

下列來自裝置模型的程式碼片段會顯示 `Enum` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

裝置用戶端應該傳送看起來像下列範例的 JSON 承載，作為裝置對應項中的報告屬性。 可能的值 `0` `1` 為、和，在 IoT Central 中會顯示為 `Item1` 、 `Item2` 和 `Item3` ：

```json
{ "EnumProperty": 1 }
```

下列來自裝置模型的程式碼片段會顯示 `Object` 屬性類型的定義。 此物件具有兩個類型為的欄位 `string` ，以及 `integer` ：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

裝置用戶端應該傳送看起來像下列範例的 JSON 承載，作為裝置對應項中的報告屬性：

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

下列來自裝置模型的程式碼片段會顯示 `vector` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
}
```

裝置用戶端應該傳送看起來像下列範例的 JSON 承載，作為裝置對應項中的報告屬性：

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>可寫入的屬性類型

本節顯示裝置從 IoT Central 應用程式接收的可寫入屬性類型範例。

IoT Central 預期會有裝置的回應可寫入屬性更新。 回應訊息應包含 `ac` 和 `av` 欄位。 `ad` 是選擇性欄位。 如需範例，請參閱下列程式碼片段。

`ac` 是使用下表中的值的數值欄位：

| 值 | 標籤 | 描述 |
| ----- | ----- | ----------- |
| `'ac': 200` | Completed | 屬性變更作業已順利完成。 |
| `'ac': 202`  或 `'ac': 201` | Pending | 屬性變更作業暫止或進行中 |
| `'ac': 4xx` | 錯誤 | 要求的屬性變更無效或發生錯誤 |
| `'ac': 5xx` | 錯誤 | 裝置處理要求的變更時，發生未預期的錯誤。 |

`av` 是傳送給裝置的版本號碼。

`ad` 這是選項字串描述。

下列來自裝置模型的程式碼片段會顯示可寫入 `string` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

裝置會從 IoT Central 接收下列承載：

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

裝置應該在處理更新之後，將下列 JSON 承載傳送至 IoT Central。 此訊息包含從 IoT Central 接收的原始更新的版本號碼。 當 IoT Central 收到此訊息時，它會將屬性標示為已在 UI 中 **同步** 處理：

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

下列來自裝置模型的程式碼片段會顯示可寫入 `Enum` 屬性類型的定義：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

裝置會從 IoT Central 接收下列承載：

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

裝置應該在處理更新之後，將下列 JSON 承載傳送至 IoT Central。 此訊息包含從 IoT Central 接收的原始更新的版本號碼。 當 IoT Central 收到此訊息時，它會將屬性標示為已在 UI 中 **同步** 處理：

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>命令

來自裝置型號的下列程式碼片段會顯示命令的定義，此命令的定義沒有任何參數，且不會預期裝置會傳回任何內容：

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

裝置會在要求中收到空的承載，並應在回應中傳回空白承載，並以 `200` HTTP 回應碼表示成功。

來自裝置模型的下列程式碼片段會顯示具有整數參數且預期裝置傳回整數值之命令的定義：

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

裝置收到的整數值為要求承載。 裝置應該傳回整數值作為回應承載，並以 `200` HTTP 回應碼表示成功。

來自裝置模型的下列程式碼片段會顯示具有物件參數且預期裝置會傳回物件的命令定義。 在此範例中，這兩個物件都有整數和字串欄位：

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
}
```

下列程式碼片段顯示傳送至裝置的範例要求承載：

```json
{ "Field1": 56, "Field2": "A string value" }
```

下列程式碼片段顯示從裝置傳送的範例回應承載。 使用 `200` HTTP 回應碼表示成功：

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="long-running-commands"></a>長時間執行的命令

下列來自裝置型號的程式碼片段會顯示命令的定義。 此命令具有整數參數，並預期裝置會傳回整數值：

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

裝置收到的整數值為要求承載。 如果裝置需要時間來處理此命令，它應該會傳回空的回應承載，其中包含 `202` HTTP 回應碼，以指出裝置已接受處理要求。

當裝置完成要求的處理之後，就應該將屬性傳送至如下列範例所示的 IoT Central。 屬性名稱必須與命令名稱相同：

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

### <a name="offline-commands"></a>離線命令

在 IoT Central web UI 中，您可以在命令的 offline 選項中選取 [ **佇列** ]。 離線命令是在裝置連線時，從您的解決方案傳遞的裝置單向通知。 離線命令可以有要求參數，但不會傳迴響應。

如果您從裝置範本匯出模型或介面，則不包含離線設定的 **佇列** 。 您無法藉由查看已匯出的模型或介面 JSON，讓命令成為離線命令。

離線命令會使用 [IoT 中樞的雲端到裝置訊息](../../iot-hub/iot-hub-devguide-messages-c2d.md) ，將命令和承載傳送到裝置。

下列來自裝置型號的程式碼片段會顯示命令的定義。 此命令具有具有 datetime 欄位和列舉的物件參數：

```json
{
  "@type": "Command",
  "displayName": {
    "en": "Generate Diagnostics"
  },
  "name": "GenerateDiagnostics",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "Payload"
    },
    "name": "Payload",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "StartTime"
          },
          "name": "StartTime",
          "schema": "dateTime"
        },
        {
          "displayName": {
            "en": "Bank"
          },
          "name": "Bank",
          "schema": {
            "@type": "Enum",
            "displayName": {
              "en": "Enum"
            },
            "enumValues": [
              {
                "displayName": {
                  "en": "Bank 1"
                },
                "enumValue": 1,
                "name": "Bank1"
              },
              {
                "displayName": {
                  "en": "Bank2"
                },
                "enumValue": 2,
                "name": "Bank2"
              },
              {
                "displayName": {
                  "en": "Bank3"
                },
                "enumValue": 2,
                "name": "Bank3"
              }
            ],
            "valueSchema": "integer"
          }
        }
      ]
    }
  }
}
```

如果您在上一個程式碼片段中命令的裝置範本 UI 中啟用 [ **離線時佇列** ] 選項，則裝置接收的訊息會包含下列屬性：

| 屬性名稱 | 範例值 |
| ---------- | ----- |
| `custom_properties` | `{'method-name': 'GenerateDiagnostics'}` |
| `data` | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

## <a name="next-steps"></a>後續步驟

作為裝置開發人員，您現在已瞭解裝置範本，建議的後續步驟是閱讀 [連線至 Azure IoT Central](./concepts-get-connected.md) ，以深入瞭解如何使用 IoT Central 註冊裝置，以及 IoT Central 如何保護裝置連線。
