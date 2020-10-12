---
title: 正則運算式實體類型-LUIS
description: 規則運算式最適用於未經處理的語句文字。 這會忽略大小寫並忽略文化特性變體。  在字元等級而非權杖等級的拼字檢查修改之後，才會套用規則運算式比對。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 0c8d4a5013f23a805c22dc5c44444de6b10ab369
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316234"
---
# <a name="regular-expression-entity"></a>規則運算式實體

規則則運算式實體會根據您所提供的規則運算式模式來擷取實體。

規則運算式最適用於未經處理的語句文字。 這會忽略大小寫並忽略文化特性變體。  在字元等級而非權杖等級的拼字檢查修改之後，才會套用規則運算式比對。 如果規則運算式太複雜 (例如使用許多方括號)，您便無法將運算式新增到模型中。 使用元件，但不是所有 [.Net Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) 程式庫。

**以下時機適用此實體：**

* 以任何一致的變化來一致地格式化的資料。
* 規則運算式不需要 2 個層級以上的巢狀結構。

![規則運算式實體](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>使用考量

正則運算式可能比您預期的還要相符。 其中一個範例是數值字組比對 `one` ，例如和 `two` 。 以下是符合數位和其他數位的 RegEx，例如 `one` ：

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

此 RegEx 運算式也會比對以這些數位結尾的任何單字，例如 `phone` 。 為了修正這類問題，請確定 RegEx 相符專案會考慮單字界限。 在下列 RegEx 中，會使用 RegEx 來使用此範例的文字界限：

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>範例 JSON

當使用 `kb[0-9]{6}` 做為正則運算式實體定義時，下列 JSON 回應是具有針對查詢傳回之正則運算式實體的範例語句：

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


如果在 `verbose=false` 查詢字串中設定，則這是 JSON：

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

如果在 `verbose=true` 查詢字串中設定，則這是 JSON：

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

深入瞭解實體：

* [概念](luis-concept-entity-types.md)
* [如何建立](luis-how-to-add-entities.md)