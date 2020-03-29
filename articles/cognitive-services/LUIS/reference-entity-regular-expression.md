---
title: 正則運算式實體類型 - LUIS
titleSuffix: Azure Cognitive Services
description: 規則運算式最適用於未經處理的語句文字。 這會忽略大小寫並忽略文化特性變體。  在字元等級而非權杖等級的拼字檢查修改之後，才會套用規則運算式比對。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74841212"
---
# <a name="regular-expression-entity"></a>規則運算式實體

正則運算式實體基於您提供的正則運算式模式提取實體。

規則運算式最適用於未經處理的語句文字。 這會忽略大小寫並忽略文化特性變體。  在字元等級而非權杖等級的拼字檢查修改之後，才會套用規則運算式比對。 如果規則運算式太複雜 (例如使用許多方括號)，您便無法將運算式新增到模型中。 使用部分但不是所有[.NET Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)庫。

**以下時機適用此實體：**

* 以任何一致的變化來一致地格式化的資料。
* 規則運算式不需要 2 個層級以上的巢狀結構。

![規則運算式實體](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>使用考量

正則運算式可能比您預期的匹配數更多。 例如，數位單詞匹配，`one`如 和`two`。 例如以下 RegEx，它匹配數位`one`和其他數位：

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

此 RegEx 運算式還匹配以這些數位結尾的任何單詞，如`phone`。 為了修復這樣的問題，請確保正則運算式匹配考慮單詞邊界。 用於此示例的單詞邊界的正則運算式用於以下正則運算式：

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>範例 JSON

當使用`kb[0-9]{6}`作為正則運算式實體定義時，以下 JSON 回應是查詢返回的正則運算式實體的示例陳述：

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)


如果在`verbose=false`查詢字串中設置，則這是 JSON：

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

如果在`verbose=true`查詢字串中設置，則這是 JSON：

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>後續步驟

在[本教程中](tutorial-regex-entity.md)，創建一個應用，以便使用**正則運算式**實體從陳述中提取一致格式的資料。