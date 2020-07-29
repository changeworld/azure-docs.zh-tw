---
title: Azure IoT Central 中的遙測、屬性和命令承載 |Microsoft Docs
description: Azure IoT Central 裝置範本可讓您指定裝置必須執行的遙測、屬性和命令。 瞭解裝置可以與 IoT Central 交換的資料格式。
author: dominicbetts
ms.author: dobett
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 554079ddec3332ced2817d18ea55ce1260d68817
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291606"
---
# <a name="telemetry-property-and-command-payloads"></a>遙測、屬性和命令裝載

_本文適用對象為裝置開發人員。_

Azure IoT Central 中的裝置範本是一個藍圖，可定義：

* 裝置傳送至 IoT Central 的遙測。
* 裝置與 IoT Central 同步處理的屬性。
* IoT Central 在裝置上呼叫的命令。

本文說明裝置開發人員針對裝置範本中定義的遙測、屬性和命令所傳送和接收的 JSON 承載。

本文不會說明每個可能的遙測、屬性和命令裝載類型，但這些範例會說明所有的索引鍵類型。

每個範例都會顯示裝置功能模型（DCM）中的程式碼片段，其定義型別和範例 JSON 承載，以說明裝置應該如何與 IoT Central 應用程式互動。

> [!NOTE]
> IoT Central 會接受任何有效的 JSON，但如果它符合 DCM 中的定義，則只能用於視覺效果。 您可以匯出不符合定義的資料，請參閱[將 IoT 資料匯出至 Azure 中的目的地](howto-export-data.md)。

定義 DCM 的 JSON 檔案會使用數位對應項[定義語言（DTDL） V1](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/dtdlv1.md)。 此規格包含屬性格式的定義 `@id` 。

如需顯示使用中部分承載的範例裝置程式碼，請參閱[建立用戶端應用程式並將其連接到您的 azure IoT Central 應用程式（Node.js）](tutorial-connect-device-nodejs.md) ，並[建立用戶端應用程式並將其連接到您的 azure IoT Central 應用程式（Python）](tutorial-connect-device-python.md)教學課程。

## <a name="view-raw-data"></a>查看原始資料

IoT Central 可讓您查看裝置傳送至應用程式的原始資料。 此視圖適用于針對從裝置傳送的承載問題進行疑難排解。 若要查看裝置正在傳送的原始資料：

1. 從 [**裝置**] 頁面流覽至裝置。

1. 選取 [**原始資料**] 索引標籤：

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="原始資料檢視":::

    在此視圖中，您可以選取要顯示的資料行，並設定要查看的時間範圍。 [未**模型化資料**] 欄位會顯示裝置中不符合裝置範本中任何屬性或遙測定義的資料。

## <a name="telemetry"></a>遙測

### <a name="primitive-types"></a>基本類型

本節顯示裝置串流至 IoT Central 應用程式的基本遙測類型範例。

下列來自 DCM 的程式碼片段顯示遙測類型的定義 `boolean` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

裝置用戶端應該會將遙測資料傳送為 JSON，如下列範例所示：

```json
{ "BooleanTelemetry": true }
```

下列來自 DCM 的程式碼片段顯示遙測類型的定義 `string` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

裝置用戶端應該會將遙測資料傳送為 JSON，如下列範例所示：

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

下列來自 DCM 的程式碼片段顯示遙測類型的定義 `integer` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

裝置用戶端應該會將遙測資料傳送為 JSON，如下列範例所示：

```json
{ "IntegerTelemetry": 23 }
```

下列來自 DCM 的程式碼片段顯示遙測類型的定義 `double` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

裝置用戶端應該會將遙測資料傳送為 JSON，如下列範例所示：

```json
{ "DoubleTelemetry": 56.78 }
```

下列來自 DCM 的程式碼片段顯示遙測類型的定義 `dateTime` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

裝置用戶端應該以 JSON 形式傳送遙測，如下列範例所示- `DateTime` 類型必須符合 ISO 8061 規範：

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

下列來自 DCM 的程式碼片段顯示遙測類型的定義 `duration` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

裝置用戶端應該以 JSON 格式傳送遙測，如下列範例所示-持續時間必須符合 ISO 8601 持續時間：

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>複雜類型

本節顯示裝置串流至 IoT Central 應用程式的複雜遙測類型範例。

下列來自 DCM 的程式碼片段顯示遙測類型的定義 `geopoint` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

裝置用戶端應該會將遙測資料傳送為 JSON，如下列範例所示。 IoT Central 會將值顯示為地圖上的釘選：

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

下列來自 DCM 的程式碼片段顯示遙測類型的定義 `Enum` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
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

裝置用戶端應該會將遙測資料傳送為 JSON，如下列範例所示。 可能的值為 `0` 、和，會 `1` `2` 在 IoT Central 中顯示為 `Item1` 、 `Item2` 和 `Item3` ：

```json
{ "EnumTelemetry": 1 }
```

下列來自 DCM 的程式碼片段顯示遙測類型的定義 `Object` 。 這個物件有三個具有類型 `dateTime` 、 `integer` 和的欄位 `Enum` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@id": "<element id>",
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
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

裝置用戶端應該會將遙測資料傳送為 JSON，如下列範例所示。 `DateTime`類型必須符合 ISO 8061 規範。 的可能值為、和， `Property3` 會 `0` `1` 在 IoT Central 中顯示為 `Item1` 、 `Item2` 和 `Item3` ：

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

下列來自 DCM 的程式碼片段顯示遙測類型的定義 `vector` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

裝置用戶端應該會將遙測資料傳送為 JSON，如下列範例所示：

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

下列來自 DCM 的程式碼片段顯示事件種類的定義 `integer` ：

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

裝置用戶端應該以 JSON 格式傳送事件資料，如下列範例所示：

```json
{ "IntegerEvent": 74 }
```

下列來自 DCM 的程式碼片段會顯示 `integer` 狀態類型的定義：

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
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

裝置用戶端應該將狀態傳送為 JSON，如下列範例所示。 可能的整數狀態值包括 `1` 、 `2` 或 `3` ：

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>屬性

> [!NOTE]
> 屬性的裝載格式適用于在07/14/2020 或之後建立的應用程式。

### <a name="primitive-types"></a>基本類型

本節顯示裝置傳送至 IoT Central 應用程式的基本屬性類型範例。

下列來自 DCM 的程式碼片段會顯示 `boolean` 屬性類型的定義：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean"
}
```

裝置用戶端應傳送如下列範例所示的 JSON 承載，做為裝置對應項中的報告屬性：

```json
{ "BooleanProperty": false }
```

下列來自 DCM 的程式碼片段會顯示 `boolean` 屬性類型的定義：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long"
}
```

裝置用戶端應傳送如下列範例所示的 JSON 承載，做為裝置對應項中的報告屬性：

```json
{ "LongProperty": 439 }
```

下列來自 DCM 的程式碼片段會顯示 `date` 屬性類型的定義：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date"
}
```

裝置用戶端應傳送如下列範例所示的 JSON 承載，做為裝置對應項中的報告屬性。 `Date`類型必須符合 ISO 8061 規範：

```json
{ "DateProperty": "2020-05-17" }
```

下列來自 DCM 的程式碼片段會顯示 `duration` 屬性類型的定義：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration"
}
```

裝置用戶端應傳送如下列範例所示的 JSON 承載，做為裝置對應項中的報告屬性-持續時間必須符合 ISO 8601 期間：

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

下列來自 DCM 的程式碼片段會顯示 `float` 屬性類型的定義：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float"
}
```

裝置用戶端應傳送如下列範例所示的 JSON 承載，做為裝置對應項中的報告屬性：

```json
{ "FloatProperty": 1.9 }
```

下列來自 DCM 的程式碼片段會顯示 `string` 屬性類型的定義：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string"
}
```

裝置用戶端應傳送如下列範例所示的 JSON 承載，做為裝置對應項中的報告屬性：

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>複雜類型

本節顯示裝置傳送至 IoT Central 應用程式的複雜屬性類型範例。

下列來自 DCM 的程式碼片段會顯示 `geopoint` 屬性類型的定義：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint"
}
```

裝置用戶端應傳送如下列範例所示的 JSON 承載，做為裝置對應項中的報告屬性：

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

下列來自 DCM 的程式碼片段會顯示 `Enum` 屬性類型的定義：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
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

裝置用戶端應傳送如下列範例所示的 JSON 承載，做為裝置對應項中的報告屬性。 可能的值為 `0` 、和，會 `1` 在 IoT Central 中顯示為 `Item1` 、 `Item2` 和 `Item3` ：

```json
{ "EnumProperty": 1 }
```

下列來自 DCM 的程式碼片段會顯示 `Object` 屬性類型的定義。 這個物件有兩個具有類型 `string` 和的欄位 `integer` ：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
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

裝置用戶端應傳送如下列範例所示的 JSON 承載，做為裝置對應項中的報告屬性：

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

下列來自 DCM 的程式碼片段會顯示 `vector` 屬性類型的定義：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector"
}
```

裝置用戶端應傳送如下列範例所示的 JSON 承載，做為裝置對應項中的報告屬性：

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

本節顯示裝置從 IoT Central 應用程式接收之可寫入屬性類型的範例。

IoT Central 預期裝置的回應可寫入屬性更新。 回應訊息應該包含 `ac` 和 `av` 欄位。 `ad` 是選擇性欄位。 如需範例，請參閱下列程式碼片段。

`ac`這是使用下表中的值的數值欄位：

| 值 | 標籤 | 描述 |
| ----- | ----- | ----------- |
| `'ac': 200` | Completed | 已成功完成屬性變更作業。 |
| `'ac': 202`或`'ac': 201` | Pending | 屬性變更作業暫止或進行中 |
| `'ac': 4xx` | 錯誤 | 要求的屬性變更無效或發生錯誤 |
| `'ac': 5xx` | 錯誤 | 在處理要求的變更時，裝置發生未預期的錯誤。 |

`av`是傳送至裝置的版本號碼。

`ad`這是一個選項字串描述。

下列來自 DCM 的程式碼片段顯示可寫入 `string` 屬性類型的定義：

```json
{
  "@id": "<element id>",
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

裝置應該會在處理更新之後，將下列 JSON 承載傳送至 IoT Central。 此訊息包含從 IoT Central 收到的原始更新版本號碼。 當 IoT Central 收到此訊息時，它會將屬性標示為在 UI 中**同步**處理：

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

下列來自 DCM 的程式碼片段顯示可寫入 `Enum` 屬性類型的定義：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
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

裝置應該會在處理更新之後，將下列 JSON 承載傳送至 IoT Central。 此訊息包含從 IoT Central 收到的原始更新版本號碼。 當 IoT Central 收到此訊息時，它會將屬性標示為在 UI 中**同步**處理：

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

### <a name="synchronous-command-types"></a>同步命令類型

下列來自 DCM 的程式碼片段顯示沒有參數且不預期裝置傳回任何專案之同步命令的定義：

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "displayName": {
    "en": "SynchronousCommandBasic"
  },
  "name": "SynchronousCommandBasic"
}
```

裝置會在要求中接收空的承載，並在回應中傳回空的承載， `200` 以指出成功的 HTTP 回應碼。

下列來自 DCM 的程式碼片段顯示具有整數參數且預期裝置傳回整數值的同步命令定義：

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "SynchronousCommandSimple"
  },
  "name": "SynchronousCommandSimple"
}
```

裝置會收到整數值做為要求裝載。 裝置應該傳回整數值做為回應裝載， `200` HTTP 回應碼為，表示成功。

下列來自 DCM 的程式碼片段顯示具有物件參數且預期裝置傳回物件的同步命令定義。 在此範例中，兩個物件都有整數和字串欄位：

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
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
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
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
    "en": "SynchronousCommandComplex"
  },
  "name": "SynchronousCommandComplex"
}
```

下列程式碼片段顯示傳送至裝置的範例要求承載：

```json
{ "Field1": 56, "Field2": "A string value" }
```

下列程式碼片段顯示從裝置傳送的範例回應承載。 使用 `200` HTTP 回應碼來表示成功：

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="asynchronous-command-types"></a>非同步命令類型

下列來自 DCM 的程式碼片段顯示非同步命令的定義。 此命令具有整數參數，而且預期裝置會傳回整數值：

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "asynchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "AsynchronousCommandSimple"
  },
  "name": "AsynchronousCommandSimple"
}
```

裝置會收到整數值做為要求裝載。 裝置應該會傳回空的回應承載與 `202` HTTP 回應碼，以指出裝置已接受非同步處理的要求。

當裝置完成處理要求時，應該將屬性傳送至如下列範例所示的 IoT Central。 屬性名稱必須與命令名稱相同：

```json
{
  "AsynchronousCommandSimple": {
    "value": 87
  }
}
```

## <a name="next-steps"></a>後續步驟

身為裝置開發人員，現在您已瞭解裝置範本，建議的後續步驟是閱讀[連線至 Azure IoT Central](./concepts-get-connected.md) ，以深入瞭解如何向 IoT Central 註冊裝置，以及 IoT Central 如何保護裝置連線的安全。
