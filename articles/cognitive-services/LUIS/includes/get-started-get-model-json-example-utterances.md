---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: e879afdbd4c34e9d74405644de86421fb2cbab46
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279532"
---
範例表達會遵循特定的格式。

`text` 欄位包含範例語句的文字。 `intentName` 欄位必須對應到 LUIS 應用程式中的現有意圖名稱。 `entityLabels` 是必填欄位。 如果您不想要標示任何實體，請提供空白陣列。

如果 entityLabels 陣列不是空的，`startCharIndex` 和 `endCharIndex` 必須標示 `entityName` 欄位中參考的實體。 索引是以零為起始，這表示頂端範例中的 6 是指 Seattle 的 "S"，而不是大寫 S 之前的空格。如果您在文字中的某個空格處開始或結束標記，則用於新增語句的 API 呼叫會失敗。

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
