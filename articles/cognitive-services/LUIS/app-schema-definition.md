---
title: 應用程式架構定義
description: LUIS 應用程式是以或表示， `.json` `.lu` 而且包含所有意圖、實體、範例語句、功能和設定。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327318"
---
# <a name="app-schema-definition"></a>應用程式架構定義

LUIS 應用程式是以或表示， `.json` `.lu` 而且包含所有意圖、實體、範例語句、功能和設定。

## <a name="format"></a>[格式]

當您匯入和匯出應用程式時，請選擇 `.json` 或 `.lu` 。

|[格式]|資訊|
|--|--|
|`.json`| 標準的程式設計格式|
|`.lu`|受 Bot Framework 的 [Bot Builder 工具](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)支援。|

## <a name="version-7x"></a>版本7。x

* 移至版本7.x，實體會以嵌套的機器學習實體來表示。
* 支援以下列撰寫 Api 的屬性撰寫嵌套的機器學習實體 `enableNestedChildren` ：
    * [新增標籤](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [新增批次標籤](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [審核標籤](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [建議實體的端點查詢](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [建議意圖的端點查詢](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

| element                  | 註解                              |
|--------------------------|--------------------------------------|
| "hierarchicals": [],     | 已淘汰，請使用 [機器學習實體](luis-concept-entity-types.md)。   |
| "合成"： []，        | 已淘汰，請使用 [機器學習實體](luis-concept-entity-types.md)。 [複合實體](reference-entity-composite.md) 參考。 |
| "closedLists": [],       | [列出實體](reference-entity-list.md) 參考，主要是用來做為實體的特徵。    |
| "versionId"： "0.1"，      | LUIS 應用程式的版本。|
| "name"： "範例-應用程式"，   | LUIS 應用程式的名稱。 |
| "desc"： ""，              | LUIS 應用程式的選擇性描述。  |
| 「文化特性」：「en-us」、      | 應用程式的[語言](luis-language-support.md)會影響基礎功能，例如預建實體、機器學習和 tokenizer。  |
| "tokenizerVersion"： "1.0.0"， | [Tokenizer](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Pattern.any 實體](reference-entity-pattern-any.md)    |
| "RegEx_entities"： []，    |  [規則運算式實體](reference-entity-regular-expression.md)   |
| "phraselists": [],       |  [片語清單 (功能) ](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "RegEx_features"： []，    |  已淘汰，請使用 [機器學習實體](luis-concept-entity-types.md)。 |
| "模式"： []，          |  模式使用[模式語法](reference-pattern-syntax.md)[改善預測精確度](luis-concept-patterns.md)   |
| "settings"： []           | [應用程式設定](luis-reference-application-settings.md)|

## <a name="version-6x"></a>版本6。x

* 移至6.x 版，使用新的 [機器學習實體](reference-entity-machine-learned-entity.md) 來代表您的實體。

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>4.x 版

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>後續步驟

* 遷移至 [V3 撰寫 api](luis-migration-authoring-entities.md)