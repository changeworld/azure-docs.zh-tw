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
ms.date: 07/07/2020
ms.openlocfilehash: d33b9b4cb50c1be7b316aad2a736bfd6fb074833
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075670"
---
# <a name="ingestion-rules"></a>內嵌規則
### <a name="json-flattening-escaping-and-array-handling"></a>JSON 簡維、轉義和陣列處理

您的 Azure 時間序列深入解析 Gen2 環境會遵循一組特定的命名慣例，以動態方式建立暖和冷存放區的資料行。 內嵌事件時，會將一組規則套用至 JSON 承載和屬性名稱。 這些包括將某些特殊字元和簡維化的 JSON 物件進行的轉義。 請務必瞭解這些規則，讓您瞭解 JSON 的圖形如何影響您的事件的儲存和查詢方式。 如需規則的完整清單，請參閱下表。 範例 A & B 也會示範如何在陣列中有效率地批次處理多個時間序列。

> [!IMPORTANT]
>
> * 選取[時間序列識別碼屬性](time-series-insights-update-how-to-id.md)和/或您的事件來源[時間戳記屬性（）](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)之前，請先檢查下列規則。 如果您的 TS 識別碼或時間戳記位於嵌套物件中，或包含下列一或多個特殊字元，請務必確定您所提供的屬性名稱符合套用內嵌規則*後*的資料行名稱。 請參閱下面的範例[B](concepts-json-flattening-escaping-rules.md#example-b) 。

| 規則 | 範例 JSON |儲存體中的資料行名稱 |
|---|---|---|
| Azure 時間序列深入解析的 Gen2 資料類型會附加至您的資料行名稱結尾，做為 "_ \<dataType\> " | ```"type": "Accumulated Heat"``` | type_string |
| [事件來源[時間戳記] 屬性](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)會儲存在 Azure 時間序列深入解析 Gen2 中，作為儲存體中的「時間戳記」，以及以 UTC 儲存的值。 您可以自訂事件來源時間戳記屬性來符合解決方案的需求，但暖和冷儲存體中的資料行名稱是 "timestamp"。 不是事件來源時間戳記的其他日期時間 JSON 屬性會以 "_datetime" 儲存在資料行名稱中，如上述規則中所述。  | ```"ts": "2020-03-19 14:40:38.318"``` | timestamp |
| 包含特殊字元的 JSON 屬性名稱。 [\ 和 ' 使用 [' 和 '] 進行轉義  |  ```"id.wasp": "6A3090FD337DE6B"``` | [' id. wasp '] _string |
| 在 [' 和 '] 內有額外的單引號和反斜線的轉義。 單引號會寫成 \ '，而反斜線會寫成\\\ | ```"Foo's Law Value": "17.139999389648"``` | [' Foo \' s 法則值 '] _double |
| 嵌套的 JSON 物件會以句點作為分隔符號來壓平合併。 支援最多10個層級。 |  ```"series": {"value" : 316 }``` | 數列. value_long |
| 基本類型的陣列會儲存為動態類型 |  ```"values": [154, 149, 147]``` | values_dynamic |
| 包含物件的陣列有兩個行為，視物件內容而定：如果 TS ID （s）或 timestamp 屬性位於陣列中的物件內，則會 unrolled 陣列，讓初始 JSON 承載產生多個事件。 這可讓您將多個事件批次處理成一個 JSON 結構。 與陣列對等的所有最上層屬性都會與每個 unrolled 物件一起儲存。 如果您的 TS 識別碼和時間戳記*不*在陣列中，就會以動態類型的形式全部儲存。 | 請參閱以下[的範例 A](concepts-json-flattening-escaping-rules.md#example-a)、 [B](concepts-json-flattening-escaping-rules.md#example-b)和[C](concepts-json-flattening-escaping-rules.md#example-c)
| 包含混合元素的陣列不會壓平合併。 |  ```"values": ["foo", {"bar" : 149}, 147]``` | values_dynamic |
| 512字元是 JSON 屬性名稱限制。 如果名稱超過512個字元，則會被截斷為512，並會附加 ' _< ' 雜湊 ' > '。 **請注意**，這也適用于已從物件簡維連接的屬性名稱，表示一個嵌套物件路徑。 |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` | 資料點的值。遙測< .。。繼續到512個字元>_912ec803b2ce49e4a541068d495ab570_double |

## <a name="understanding-the-dual-behavior-for-arrays"></a>瞭解陣列的雙重行為

物件的陣列會根據您模型化資料的方式，整體儲存或分割成多個事件。 這可讓您使用陣列來批次事件，並避免重複在根物件層級定義的遙測屬性。 批次處理可能很有利，因為這會導致傳送的事件中樞或 IoT 中樞訊息較少。 

不過，在某些情況下，包含物件的陣列只有在其他值的內容中有意義。 建立多個事件會使資料無意義。 若要確保物件的陣列會以非動態類型的形式儲存，請遵循下列資料模型化指導方針，並查看[範例 C](concepts-json-flattening-escaping-rules.md#example-c)

### <a name="how-do-i-know-if-my-array-of-objects-will-produce-multiple-events"></a>如何? 知道我的物件陣列是否會產生多個事件？

如果您的一或多個時間序列識別碼屬性是嵌套在陣列中的物件內，*或*如果您的事件來源時間戳記屬性是嵌套的，則內嵌引擎會將其分割，以建立多個事件。 您為 TS 識別碼和/或時間戳記提供的屬性名稱，應遵循上述的簡維規則，因此會指出您的 JSON 圖形。 請參閱下列範例，並查看如何[選取時間序列識別碼屬性](time-series-insights-update-how-to-id.md)的指南。

### <a name="example-a"></a>範例 A：
位於物件根和時間戳記的時間序列識別碼嵌套<br/>
**環境時間序列識別碼：**`"id"`<br/>
**事件來源時間戳記：**`"values.time"`<br/>
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

**產生 Parquet 檔案：**
<br/>
上述設定和承載會產生三個數據行和四個事件

| timestamp  | id_string | 值. value_double 
| ---- | ---- | ---- | 
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` | 
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` | 
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` | 
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` | 

### <a name="example-b"></a>範例 B：
具有一個嵌套屬性的複合時間序列識別碼<br/> 
**環境時間序列識別碼：** `"plantId"`和`"telemetry.tagId"`<br/>
**事件來源時間戳記：**`"timestamp"`<br/>
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

**產生 Parquet 檔案：**
<br/>
上述設定和承載會產生四個數據行和六個事件

| timestamp  | plantId_string | 遙測。 tagId_string | 遙測。 value_double 
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31.149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20.560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336972` | ``100231-A-A7`` | -30.9918 |  
| `2020-01-22T16:42:14Z` | `9336972` | ``100231-A-A4`` | 19.960796 | 

### <a name="example-c"></a>範例 C：
時間序列識別碼和時間戳記位於物件根目錄<br/> 
**環境時間序列識別碼：**`"id"`<br/>
**事件來源時間戳記：**`"timestamp"`<br/>
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

**產生 Parquet 檔案：**
<br/>
上述設定和承載會產生三個數據行和一個事件

| timestamp  | id_string | datapoints_dynamic  
| ---- | ---- | ---- | 
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>後續步驟

* 瞭解您環境的[輸送量限制](./concepts-streaming-ingress-throughput-limits.md)
