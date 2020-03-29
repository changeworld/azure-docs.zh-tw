---
title: 模式.任何實體類型 - LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any 是僅用於模式範本語句的可變長度預留位置，用來標記實體開始及結束的位置。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75979168"
---
# <a name="patternany-entity"></a>Pattern.any 實體

Pattern.any 是僅用於模式範本語句的可變長度預留位置，用來標記實體開始及結束的位置。  

Pattern.any 實體需要在[模式](luis-how-to-model-intent-pattern.md)範本裡的範例 (而不是意圖裡使用者所提供的範例) 中進行標記。

**以下時機適用此實體：**

* 實體的結尾可能會與語句的其餘文字混淆。

## <a name="usage"></a>使用量

如果用戶端應用程式要根據書名來搜尋書籍，pattern.any 會擷取完整的書名。 為搜尋此書而使用 pattern.any 的範本語句是 `Was {BookTitle} written by an American this year[?]`。

下表中，每個資料列都有兩個版本的語句。 最上面的話語是 LUIS 最初如何看待話語。 不清楚書名的開始和結束位置。 底部陳述使用 Pattern.任何實體來標記實體的開始和結束。

|以粗體顯示的實體|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Was **The Man Who Mistook His Wife for a Hat and Other Clinical Tales** written by an American this year?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Was **Half Asleep in Frog Pajamas** written by an American this year?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>**檸檬蛋糕的特殊悲傷：** 一個美國人今年寫的小說嗎？|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**我的口袋裡有個沃克嗎？** written by an American this year?|
||



## <a name="example-json"></a>範例 JSON

請考慮以下查詢：

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

使用嵌入的表格名稱提取為 Pattern.any：

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

如果在`verbose=false`查詢字串中設置，則這是 JSON：

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

如果在`verbose=true`查詢字串中設置，則這是 JSON：

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
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

在[本教程](luis-tutorial-pattern.md)中，使用**Pattern.任何**實體從陳述格式良好的、資料末尾可能很容易與話語的剩餘字詞混淆的話語中提取資料。
