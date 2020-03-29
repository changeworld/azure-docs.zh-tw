---
title: 日期時間V2預構建實體 - LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中 datetimeV2 預先建置的實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 30132983f37323e798efd330f5cc8f15c0a9d2b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270743"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的 DatetimeV2 預先建置實體

**datetimeV2** 預先建置的實體會擷取日期和時間值。 這些值會以標準化格式解析，以供用戶端程式取用。 當語句中有不完整的日期或時間時，LUIS 會在端點回應中包括「過去與未來值」__。 因為此實體已經定型，所以您不需要將包含 datetimeV2 的範例語句加入應用程式意圖。

## <a name="types-of-datetimev2"></a>datetimeV2 類型
日期時間V2由[識別器文本](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml)GitHub 存儲庫管理。

## <a name="example-json"></a>範例 JSON

以下陳述及其部分 JSON 回應如下所示。

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[V3 回應](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 回應](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|屬性名稱 |屬性類型和描述|
|---|---|
|單位|**string** - 以日期、時間、日期範圍或時間範圍的類型從語句中擷取的文字。|
|type|**string** - 其中一個 [datetimeV2 子類型](#subtypes-of-datetimev2)
|startIndex|**int** - 實體開始的語句中的索引。|
|endIndex|**int** - 實體結束的語句中的索引。|
|resolution|有一、二或四個[解析值](#values-of-resolution)的 `values` 陣列。|
|end|時間或日期範圍的結束值，使用的格式和 `value` 相同。 只有當 `type` 為 `daterange`、`timerange` 或 `datetimerange` 時才能使用|

* * *

## <a name="subtypes-of-datetimev2"></a>datetimeV2 子類型

**datetimeV2** 預先建置的實體具有下列子類型，而個別的範例均在下表提供：
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>解析值
* 如果語句中指定的日期或時間完整且明確，陣列會有一個元素。
* 如果 datetimeV2 值語意模糊，陣列會有兩個元素。 語意模糊包括缺少特定年份、時間或時間範圍。 如需範例，請參閱[語意模糊的日期](#ambiguous-dates)。 若時間的上午 或下午語意模糊，即會包含這兩個值。
* 如果語句有兩個語意模糊的元素，陣列會有四個元素。 這包括以下語意模糊的元素：
  * 日期或日期範圍的年份語意模糊
  * 時間或時間範圍的上午或下午 語意模糊。 例如，4 月 3 日 3:00。

`values` 陣列的每個元素可能都有下列欄位：

|屬性名稱|屬性描述|
|--|--|
|timex|以遵循 [ISO 8601 標準](https://en.wikipedia.org/wiki/ISO_8601)的 TIMEX 格式表示的時間、日期或日期範圍，並對註解的 TIMEX3 屬性使用 TimeML 語言。 此註解會在 [TIMEX 指導方針](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf) \(英文\) 中說明。|
|mod|術語用於描述如何使用值，如`before`。 `after`|
|type|子類型可以是以下項之一`datetime`： 、 `date`、 `time`、 `daterange`、 `timerange`、 `datetimerange` `duration`、 `set`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、|
|value|**選。** 格式 yyyy-MM-dd（日期）、HH：mm：ss（時間）yyyy-MM-dd HH：mm：ss（日期時間）中的約會時間物件。 如果 `type` 是 `duration`，則值會是秒數 (duration) <br/> 只有當 `type` 是 `datetime` 或 `date`、`time` 或 `duration 時才能使用。|

## <a name="valid-date-values"></a>有效的日期值

**datetimeV2** 支援的日期範圍如下：

| 最小值 | 最大值 |
|----------|-------------|
| 1900 年 1 月 1 日   | 2099 年 12 月 31 日 |

## <a name="ambiguous-dates"></a>語意模糊的日期

如果可以是過去或未來的日期，LUIS 會兩個值都提供。 例如，語句包含月份與日期，但不含年份。

例如，給定以下表述：

`May 2nd`

* 如果今天的日期為 2017 年 5 月 3 日，LUIS 會提供 "2017-05-02" 與 "2018-05-02" 這兩個值。
* 當今天的日期為 2017 年 5 月 1 日時，LUIS 會提供 "2016-05-02" 與 "2017-05-02" 這兩個值。

下列範例顯示「5 月 2 日」實體的解析。 此解析假設今天的日期介於 2017 年 5 月 2 日到 2018 年 5 月 1 日之間。
`timex` 欄位中包含 `X` 的欄位，是語句中未明確指定的日期部分。

## <a name="date-resolution-example"></a>日期解析示例


以下陳述及其部分 JSON 回應如下所示。

`May 2nd`

#### <a name="v3-response"></a>[V3 回應](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 回應](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>數值日期的日期範圍解析範例

`datetimeV2` 實體會擷取日期和時間範圍。 `start` 和 `end` 欄位可指定範圍的開頭和結尾。 對於陳述`May 2nd to May 5th`，LUIS 提供當前年份和下一年的**日期範圍**值。 在 `timex` 欄位中，`XXXX` 值表示年份的語意模糊。 `P3D` 表示時間週期長達三天。

以下陳述及其部分 JSON 回應如下所示。

`May 2nd to May 5th`

#### <a name="v3-response"></a>[V3 回應](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 回應](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>星期幾的日期範圍解析範例

下面的示例顯示了 LUIS 如何使用**datetimeV2**來解決話語`Tuesday to Thursday`。 在此範例中，目前日期為 6 月 19 日。 LUIS 包含兩個日期範圍的 **daterange** 值，分別在目前日期之前與之後。

以下陳述及其部分 JSON 回應如下所示。

`Tuesday to Thursday`

#### <a name="v3-response"></a>[V3 回應](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 回應](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>語意模糊的時間
如果時間或時間範圍的語意模糊，值陣列會有兩個時間元素。 有語意模糊的時間時，就會有上午 與下午 兩個時間值。

## <a name="time-range-resolution-example"></a>時間範圍解析範例

日期時間V2 JSON回應在 API V3 中已更改。 下列範例示範 LUIS 如何使用 **datetimeV2** 解析有時間範圍的語句。

API V2 的更改：
* `datetimeV2.timex.type`屬性不再返回，因為它在父級別返回 。 `datetimev2.type`
* 該`datetimeV2.value`屬性已重命名為`datetimeV2.timex`。

以下陳述及其部分 JSON 回應如下所示。

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3 回應](#tab/5-1)

以下 JSON 的`verbose`參數設置為`false`：

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/5-2)

以下 JSON 的`verbose`參數設置為`true`：

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 回應](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>時間解析度示例

以下陳述及其部分 JSON 回應如下所示。

`8am`

#### <a name="v3-response"></a>[V3 回應](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 回應](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>已被取代的預先建置 datetime

`datetime` 預先建置的實體已過時 ，並由 **datetimeV2** 取代。

若要在 LUIS 應用程式中以 `datetimeV2` 取代 `datetime`，請完成下列步驟：

1. 開啟 LUIS 網頁介面的 [實體]**** 窗格。
2. 刪除 **datetime** 預先建置的實體。
3. 按一下 [新增預先建置的實體]****
4. 選取 [datetimeV2]****，然後按一下 [儲存]****。

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

了解 [dimension](luis-reference-prebuilt-dimension.md)、[email](luis-reference-prebuilt-email.md) 實體與 [number](luis-reference-prebuilt-number.md) 的相關資訊。

