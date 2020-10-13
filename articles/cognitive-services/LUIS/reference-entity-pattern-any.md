---
title: Pattern：任何實體類型-LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any 是僅用於模式範本語句的可變長度預留位置，用來標記實體開始及結束的位置。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: ec23be3709cebc534c059a21c52452abff683b18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542201"
---
# <a name="patternany-entity"></a>Pattern.any 實體

Pattern.any 是僅用於模式範本語句的可變長度預留位置，用來標記實體開始及結束的位置。  

Pattern.any 實體需要在[模式](luis-how-to-model-intent-pattern.md)範本裡的範例 (而不是意圖裡使用者所提供的範例) 中進行標記。

**以下時機適用此實體：**

* 實體的結尾可能會與語句的其餘文字混淆。

## <a name="usage"></a>使用量

如果用戶端應用程式要根據書名來搜尋書籍，pattern.any 會擷取完整的書名。 為搜尋此書而使用 pattern.any 的範本語句是 `Was {BookTitle} written by an American this year[?]`。

下表中，每個資料列都有兩個版本的語句。 最上層的語句是 LUIS 一開始如何看到語句。 本書標題的開頭和結尾並不清楚。 底部的語句會使用模式。任何實體都可標示實體的開頭和結尾。

|具有粗體實體的語句|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Was **The Man Who Mistook His Wife for a Hat and Other Clinical Tales** written by an American this year?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Was **Half Asleep in Frog Pajamas** written by an American this year?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>這是 **檸檬蛋糕的特殊悲傷：今年今年所撰寫的新穎** 嗎？|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**我的 Pocket 有 Wocket！** written by an American this year?|
||



## <a name="example-json"></a>範例 JSON

請考慮以下查詢：

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

將內嵌表單名稱解壓縮為模式。任何：

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

如果在 `verbose=false` 查詢字串中設定，則這是 JSON：

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

如果在 `verbose=true` 查詢字串中設定，則這是 JSON：

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

在本 [教學](luis-tutorial-pattern.md)課程中，使用 **模式。任何** 實體都會從語句中將資料解壓縮，其中語句的格式正確，而且資料的結尾可能會與語句的其餘單字很容易混淆。
