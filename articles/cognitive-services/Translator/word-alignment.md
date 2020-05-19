---
title: 文字對齊-翻譯工具
titleSuffix: Azure Cognitive Services
description: 若要收到對齊方式資訊，請使用 Translate 方法並包含選擇性的 includeAlignment 參數。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: aa43312e16b2c1777ab9a1f1668cc4ea9c9d18e4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588457"
---
# <a name="how-to-receive-word-alignment-information"></a>如何接收單字對齊方式資訊

## <a name="receiving-word-alignment-information"></a>接收單字對齊方式資訊
若要收到對齊方式資訊，請使用 Translate 方法並包含選擇性的 includeAlignment 參數。

## <a name="alignment-information-format"></a>對齊方式資訊格式
系統會以下列格式的字串值，傳回來源每個單字的對齊方式。 每個單字的資訊會以空格分隔，包括中文這類非以空格分隔的語言 (指令碼)：

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

範例對齊方式字串："0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21"。

換句話說，冒號會分隔開始與結束索引、虛線會分隔語言，而空格則分隔單字。 一個單字可能會與另一種語言中的零個、一個或多個單字對齊，而對齊的單字可能不連續。 沒有對齊方式資訊可用時，Alignment 元素便會空白。 在該情況下，方法不會傳回任何錯誤。

## <a name="restrictions"></a>限制
目前，只會傳回一部分語言組的對齊方式：
* 從英文到任何其他語言；
* 從任何其他語言到英文，但從簡體中文、繁體中文和拉脫維亞文到英文除外
* 從日文到韓文，或從韓文到日文。如果句子是已定義的翻譯，您便不會收到對齊方式資訊。 已定義的翻譯範例包括 "This is a test"、"I love you" 及其他經常出現的句子。

## <a name="example"></a>範例

範例 JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
