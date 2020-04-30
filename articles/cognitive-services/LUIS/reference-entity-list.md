---
title: 列出實體類型-LUIS
description: 清單實體代表一組固定的封閉式相關字組及其同義字。 LUIS 並不會探索清單實體的額外值。 使用建議功能，以根據目前的清單查看適用於新字組的建議。
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 273fabae38f6682cfaaffcdcc19e62adc41b7a47
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097569"
---
# <a name="list-entity"></a>清單實體

清單實體代表一組固定的封閉式相關字組及其同義字。 LUIS 並不會探索清單實體的額外值。 使用**建議**功能，以根據目前的清單查看適用於新字組的建議。 如果有多個清單實體具有相同的值，則在端點查詢中會傳回每個實體。

清單實體不是機器學習實體。 它是全文相符的項目。 LUIS 會將與任何清單中項目相符的項目，在回應中標示為實體。

**當文字資料有下列特性時，最適用此實體：**

* 是已知的組合。
* 不常變更。 如果您需要經常變更清單，或想要讓清單自我展開，使用片語清單提升的簡單實體是較好的選擇。
* 此組合不會超過此實體類型的最大 LUIS [界限](luis-limits.md)。
* 語句中的文字是與同義字或正式名稱相符的項目 (不區分大小寫)。 LUIS 不會將清單用於相符項目以外的範圍。 不會使用清單實體解析模糊比對、詞幹分析、複數和其他變化。 若要管理變化，請考慮使用[模式](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance)並搭配選擇性的文字語法。

![清單實體](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>要匯入到清單實體的範例 json

  您可以使用下列 json 格式，將值匯入到現有的清單實體：

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>範例 JSON 回應

假設應用程式具有一個名為 `Cities` 的清單，其中可允許城市名稱的各種變化，包括機場城市 (Sea-tac)、機場代碼 (SEA)、郵遞區號 (98101) 及電話區碼 (206)。

|清單項目|項目同義字|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

在上述語句中，`paris` 一字會對應至 `Cities` 清單實體中的 paris 項目。 此清單實體會同時比對項目的正規化名稱及項目同義字。

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)


如果`verbose=false`是在查詢字串中設定，則這是 JSON：

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

如果`verbose=true`是在查詢字串中設定，則這是 JSON：

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|資料物件|實體名稱|值|
|--|--|--|
|列出實體|`Cities`|`paris`|


## <a name="next-steps"></a>後續步驟

在本[教學](tutorial-list-entity.md)課程中，您將瞭解如何使用**清單實體**，從已知專案清單中解壓縮完全相符的文字。
