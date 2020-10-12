---
title: JSON 簡維和轉義規則-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析 Gen2 中的 JSON 簡維、轉義和陣列處理。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.openlocfilehash: a1f633548ed36320f40e485f540923c8e3045a99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460861"
---
# <a name="json-flattening-escaping-and-array-handling"></a>JSON 壓平合併、逸出和陣列處理

您的 Azure 時間序列深入解析 Gen2 環境會在一組特定的命名慣例之後，以動態方式建立暖和冷存放區的資料行。 內嵌事件時，會將一組規則套用至 JSON 承載和屬性名稱。 這些包括將某些特殊字元和簡維的嵌套 JSON 物件進行轉義。 請務必瞭解這些規則，如此您才能瞭解 JSON 的圖形將如何影響您的事件的儲存和查詢方式。 如需規則的完整清單，請參閱下表。 & B 的範例也會示範如何有效率地在陣列中批次處理多個時間序列。

> [!IMPORTANT]
>
> * 選取 [時間序列識別碼屬性](time-series-insights-update-how-to-id.md) 和/或您的事件來源 [時間戳記屬性 () ](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)之前，請先檢查下列規則。 如果您的 TS 識別碼或時間戳記是在嵌套物件內，或有一或多個特殊字元，則請務必確定您提供的屬性名稱在套用內嵌規則 *之後* 符合資料行名稱。 請參閱下面的範例 [B](concepts-json-flattening-escaping-rules.md#example-b) 。

| 規則 | 範例 JSON | [時間序列運算式語法](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) | Parquet 中的屬性資料行名稱
|---|---|---|---|
| Azure 時間序列深入解析的 Gen2 資料類型會附加至資料行名稱的結尾，作為 "_ \<dataType\> " | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| [事件來源 [時間戳記] 屬性](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) 會儲存在 Azure 時間序列深入解析 Gen2 中，做為儲存區中的 "timestamp"，以及以 UTC 儲存的值。 您可以自訂事件來源 (s) timestamp 屬性以符合解決方案的需求，但暖和冷儲存體中的資料行名稱為 "timestamp"。 其他不是事件來源時間戳記的日期時間 JSON 屬性，將會在資料行名稱中儲存 "_datetime"，如同上述規則中所述。  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| 包含特殊字元的 JSON 屬性名稱。 [\ 和 ' 使用 [' and '] 進行了轉義  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| 在 [' and '] 內，有一個單引號和反斜線的額外轉義。 單引號將會寫入為 \ '，而反斜線將會撰寫為 \\\ | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| 嵌套的 JSON 物件會以句號作為分隔符號來壓平合併。 支援最多10個層級的嵌套。 |  ```"series": {"value" : 316 }``` | `$event.series.value.Long`、`$event['series']['value'].Long` 或 `$event.series['value'].Long` |  `series.value_long` |
| 基本類型的陣列會儲存為動態類型 |  ```"values": [154, 149, 147]``` | 動態類型只能透過 [GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API 來抓取 | `values_dynamic` |
| 根據物件內容，包含物件的陣列有兩種行為：如果 (s 的 TS 識別碼) 或時間戳記 (屬性，) 在陣列的物件內，則會展開陣列，使初始 JSON 承載產生多個事件。 這可讓您將多個事件批次處理成一個 JSON 結構。 任何與陣列對等的最上層屬性都會與每個展開物件一起儲存。 如果您的 TS 識別碼 (s) ，而且時間戳記 *不* 在陣列內，則會將整個儲存為動態類型。 | 請參閱下列範例[A](concepts-json-flattening-escaping-rules.md#example-a)、 [B](concepts-json-flattening-escaping-rules.md#example-b)和[C](concepts-json-flattening-escaping-rules.md#example-c)
| 包含混合元素的陣列不會壓平合併。 |  ```"values": ["foo", {"bar" : 149}, 147]``` | 動態類型只能透過 [GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API 來抓取 | `values_dynamic` |
| 512個字元是 JSON 屬性名稱限制。 如果名稱超過512個字元，則會截斷為512，並附加 ' _< ' 雜湊 ' > '。 **請注意** ，這也適用于已從物件壓平合併的屬性名稱，表示嵌套物件路徑。 |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>瞭解陣列的雙重行為

系統會根據您模型化資料的方式，將物件的陣列全部儲存或分割成多個事件。 這可讓您使用陣列來批次處理事件，並避免在根物件層級定義的重複遙測屬性。 批次處理可能很有利，因為這樣會導致較少的事件中樞或已傳送的 IoT 中樞訊息。

不過，在某些情況下，包含物件的陣列只有在其他值的內容中才有意義。 建立多個事件會呈現無意義的資料。 若要確保物件的陣列儲存為動態類型，請遵循下列資料模型化指導方針，並看看 [範例 C](concepts-json-flattening-escaping-rules.md#example-c)

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>如何知道我的物件陣列是否會產生多個事件

如果您的一或多個時間序列識別碼屬性 (的) 會嵌套在陣列中的物件內， *或* 如果您的事件來源時間戳記屬性是嵌套的，則內嵌引擎會將它分割出來以建立多個事件。 您為 TS 識別碼 (s) 和/或時間戳記提供的屬性名稱應遵循上述的簡維規則，因此會指出您的 JSON 形式。 請參閱下列範例，並查看如何[選取時間序列識別碼屬性](time-series-insights-update-how-to-id.md)的指南。

### <a name="example-a"></a>範例 A

物件根目錄的時間序列識別碼和時間戳記嵌套的 \
**環境時間序列識別碼：**`"id"`\
**事件來源時間戳記：**`"values.time"`\
**JSON 承載：**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**產生 Parquet 檔案：**\
上述設定和承載將會產生三個數據行和四個事件

| timestamp  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>範例 B

具有一個屬性 nested 的複合時間序列識別碼 \
**環境時間序列識別碼：** `"plantId"` 和 `"telemetry.tagId"`\
**事件來源時間戳記：**`"timestamp"`\
**JSON 承載：**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**產生 Parquet 檔案：**\
上述設定和承載將會產生四個數據行和六個事件

| timestamp  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31.149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20.560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30.9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19.960796 |

### <a name="example-c"></a>範例 C

時間序列識別碼和時間戳記位於物件根目錄 \
**環境時間序列識別碼：**`"id"`\
**事件來源時間戳記：**`"timestamp"`\
**JSON 承載：**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**產生 Parquet 檔案：**\
上述設定和承載會產生三個數據行和一個事件

| timestamp  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>後續步驟

* 瞭解您環境的 [輸送量限制](./concepts-streaming-ingress-throughput-limits.md)
