---
title: 應用程式架構定義
description: LUIS 應用程式會以或表示， `.json` 其中 `.lu` 包含所有意圖、實體、範例語句、功能和設定。
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: dbbeb4eacbe8e07cf080b3a1527ca39c2b9a5991
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684351"
---
# <a name="app-schema-definition"></a>應用程式架構定義

LUIS 應用程式會以或表示， `.json` 其中 `.lu` 包含所有意圖、實體、範例語句、功能和設定。

## <a name="format"></a>格式

當您匯入和匯出應用程式時，請選擇 `.json` 或 `.lu` 。

|格式|資訊|
|--|--|
|`.json`| 標準程式設計格式|
|`.lu`|由 Bot Framework 的[Bot Builder 工具](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)支援。|

## <a name="version-7x"></a>版本7。x

* 移至版本7.x，實體會以嵌套的機器學習實體來表示。
* 支援使用下列撰寫 Api 的屬性來撰寫嵌套機器學習實體 `enableNestedChildren` ：
    * [新增標籤](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [新增批次標籤](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [審查標籤](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
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
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-6x"></a>版本6。x

* 移至版本6.x 時，請使用新的[機器學習實體](reference-entity-machine-learned-entity.md)來代表您的實體。

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

## <a name="version-4x"></a>1.x 版

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

* 遷移至[V3 撰寫 api](luis-migration-authoring-entities.md)