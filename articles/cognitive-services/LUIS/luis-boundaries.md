---
title: 限制-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Azure 認知服務 Language Understanding (LUIS) 的已知限制。 LUIS 句有數個界線領域。 模型界線可控制 LUIS 中的意圖、實體和特性。 以金鑰類型為基礎的配額限制。 鍵盤組合可控制 LUIS 網站。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d584b00caef628eb9dfd085b1fdce2bb7b353988
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221345"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>LUIS 模型和金鑰的界限
LUIS 句有數個界線領域。 第一個是[模型界線](#model-boundaries)，其控制 LUIS 中的意圖、實體和功能。 第二個領域是以金鑰類型為基礎的[配額限制](#key-limits)。 第三個界線領域是用來控制 LUIS 網站的[鍵盤組合](#keyboard-controls)。 第四個領域是 LUIS 撰寫網站和 LUIS [端點](luis-reference-regions.md) API 之間的[世界區域對應](luis-glossary.md#endpoint)。


## <a name="model-boundaries"></a>模型界線

如果您的應用程式超出 LUIS 模型限制和界限，請考慮使用 [LUIS 分派](luis-concept-enterprise.md#dispatch-tool-and-model) 應用程式或使用 [LUIS 容器](luis-container-howto.md)。

|區域|限制|
|--|:--|
| [應用程式名稱][luis-get-started-create-app] | *預設字元上限 |
| 應用程式| 500每個 Azure 撰寫資源的應用程式 |
| [批次測試][batch-testing]| 10 個資料集，每個資料集 1000 個語句|
| 明確清單 | 每個應用程式 50 個|
| 外部實體 | 無限制 |
| [意圖][intents]|每個應用程式500：499個自訂意圖，以及必要的_None_意圖。<br>[發送型](https://aka.ms/dispatch-tool) \(英文\) 應用程式具有相對應的 500 個發送來源。|
| [清單實體](./luis-concept-entity-types.md) | 父系：50 個項目，子系：20,000 個項目。 正式名稱為*預設字元上限。同義值沒有長度限制。 |
| [機器學習的實體 + 角色](./luis-concept-entity-types.md)：<br> 複<br>單個<br>實體角色|100父實體或330實體的限制，以一開始的限制為准。 角色會計算為此界限用途的實體。 其中一個範例是具有簡單實體的複合，其中有2個角色：1個複合 + 1 簡單 + 2 個角色 = 4 個330實體。<br>子元件可以嵌套到5個層級。|
|模型即功能| 可做為特定模型之描述項（特徵）的模型數目上限，為10個模型。 用來做為特定模型之描述項（功能）的片語清單數目上限為10個片語清單。|
| [預覽-動態清單實體](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2個每個查詢預測端點要求的清單|
| [模式](luis-concept-patterns.md)|每個應用程式 500 個模式。<br>模式的長度上限為 400 個字元。<br>每個模式 3 個 pattern.any 實體<br>模式中最多有 2 個巢狀選擇性文字|
| [Pattern.any](./luis-concept-entity-types.md)|每個應用程式 100 個，每個模式 3 個 pattern.any 實體 |
| [片語清單][phrase-list]|500片語清單。 不可互換的 phraselist 最多可有5000個片語。 可互換的 Phraselist 最多可有50000個片語。 每一應用程式500000片語的總片語數上限。|
| [預先建置實體](./luis-prebuilt-entities.md) | 沒有限制|
| [規則運算式實體](./luis-concept-entity-types.md)|20 個實體<br>每個規則運算式實體模式 具有 500 個字元的上限|
| [角色](luis-concept-roles.md)|每個應用程式 300 個角色。 每個實體 10 個角色|
| [語句][utterances] | 500 個字元|
| [語句][utterances] | 每個應用程式 15000-每個意圖的語句數目沒有限制|
| [版本](luis-concept-version.md)| 100每個應用程式的版本 |
| [版本名稱][luis-how-to-manage-versions] | 10 個字元，限制為英數字元和句號 (.) |

*預設字元上限為 50 個字元。

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>名稱唯一性

請使用下列命名唯一性規則。

下列在 LUIS 應用程式內必須是唯一的：

* 版本名稱
* 意圖
* 實體
* 角色

下列在套用的範圍內必須是唯一的：

* 片語清單

## <a name="object-naming"></a>物件命名

請不要在下列名稱中使用下列字元。

|Object|排除字元|
|--|--|
|意圖、實體和角色名稱|`:`<br>`$` <br> `&`|
|版本名稱|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>金鑰使用量

Language Understand 有不同的金鑰，一種適用於撰寫，一種適用於查詢預測端點。 若要深入了解金鑰類型之間的差異，請參閱 [LUIS 中的撰寫與查詢預測端點金鑰](luis-concept-keys.md)。

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>資源金鑰限制

資源金鑰在撰寫和端點方面有不同的限制。 LUIS 預測查詢端點金鑰只對端點查詢有效。

* 500每個 Azure 撰寫資源的應用程式

|Key|編寫|端點|目的|
|--|--|--|--|
|入門|1 百萬個/月，5 個/秒|1 千個/月，5 個/秒|撰寫 LUIS 應用程式|
|F0-免費層 |1 百萬個/月，5 個/秒|1 萬個/月，5 個/秒|查詢 LUIS 端點|
|S0-基本層|-|50 個/秒|查詢 LUIS 端點|
|S0-標準層|-|50 個/秒|查詢 LUIS 端點|
|[情感分析整合](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|在不需要另一個 Azure 資源的情況下，會提供新增包含關鍵字組資料解壓縮的情感資訊。 |
|[語音整合](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000每單位成本的端點要求數|將口語語句轉換成文字語句並傳回 LUIS 結果|

[深入瞭解定價。][pricing]

## <a name="keyboard-controls"></a>鍵盤控制項

|鍵盤輸入 | 描述 |
|--|--|
|Ctrl+E|在語句清單上的權杖和實體之間切換|

## <a name="website-sign-in-time-period"></a>網站登入時間週期

您登入存取的時間為 **60 分鐘**。 在此時段之後，您將會收到這個錯誤。 您需要再次登入。

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
