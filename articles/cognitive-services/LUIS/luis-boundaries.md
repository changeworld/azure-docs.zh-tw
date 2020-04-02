---
title: 限制 - LUIS
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
ms.openlocfilehash: 6c021e68f8b76d8b0d3e6e9ff21c242580f53313
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520947"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>LUIS 模型和金鑰的界限
LUIS 句有數個界線領域。 第一個是[模型界線](#model-boundaries)，其控制 LUIS 中的意圖、實體和功能。 第二個領域是以金鑰類型為基礎的[配額限制](#key-limits)。 第三個界線領域是用來控制 LUIS 網站的[鍵盤組合](#keyboard-controls)。 第四個領域是 LUIS 撰寫網站和 LUIS [端點](luis-glossary.md#endpoint) API 之間的[世界區域對應](luis-reference-regions.md)。


## <a name="model-boundaries"></a>模型界線

如果您的應用程式超出 LUIS 模型限制和界限，請考慮使用 [LUIS 分派](luis-concept-enterprise.md#dispatch-tool-and-model) 應用程式或使用 [LUIS 容器](luis-container-howto.md)。

|區域|限制|
|--|:--|
| [應用程式名稱][luis-get-started-create-app] | *預設字元上限 |
| 應用程式| 每個 Azure 創作資源 500 個應用程式 |
| [批次測試][batch-testing]| 10 個資料集，每個資料集 1000 個語句|
| 明確清單 | 每個應用程式 50 個|
| 外部實體 | 無限制 |
| [對應方式][intents]|每個應用程式 500 個:499 個自定義意圖,以及所需的_無_意圖。<br>[基於調度](https://aka.ms/dispatch-tool)的應用程式具有相應的 500 個調度源。|
| [清單實體](./luis-concept-entity-types.md) | 父系：50 個項目，子系：20,000 個項目。 正式名稱為*預設字元上限。同義值沒有長度限制。 |
| [機器學習的實體 + 角色](./luis-concept-entity-types.md):<br> 複合<br>簡單<br>實體角色|100 個父實體或 330 個實體的限制,以使用者首先命中的限制為準。 為了此邊界,角色計為實體。 例如,具有簡單實體的複合,它有 2 個角色:330 個實體中的 4 個複合 = 1 個簡單 + 2 個角色。<br>子元件最多可嵌套 5 個級別。|
|建模為特徵| 可用作特定模型的描述符(特徵)的最大模型數為 10 個模型。 用作特定模型描述符(功能)的短語清單的最大數量為 10 個短語清單。|
| [預覽 - 動態清單實體](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|每個查詢預測終結點請求的 2 個 ±1k 清單|
| [模式](luis-concept-patterns.md)|每個應用程式 500 個模式。<br>模式的長度上限為 400 個字元。<br>每個模式 3 個 pattern.any 實體<br>模式中最多有 2 個巢狀選擇性文字|
| [Pattern.any](./luis-concept-entity-types.md)|每個應用程式 100 個，每個模式 3 個 pattern.any 實體 |
| [片語清單][phrase-list]|500 個短語清單。 10 個全域短語清單,由於模型作為特徵限制。 不可互換的短語清單最多包含 5,000 個短語。 可互換短語清單最多包含 50,000 個短語。 每個應用的最大總短語數為 500,000 個短語。|
| [預置實體](./luis-prebuilt-entities.md) | 沒有限制|
| [規則運算式實體](./luis-concept-entity-types.md)|20 個實體<br>每個規則運算式實體模式 具有 500 個字元的上限|
| [角色](luis-concept-roles.md)|每個應用程式 300 個角色。 每個實體 10 個角色|
| [語句][utterances] | 500 個字元|
| [語句][utterances] | 每個應用程式 15,000 ─ 每個意圖的話語次數沒有限制|
| [版本](luis-concept-version.md)| 每個應用程式 100 個版本 |
| [版本名稱][luis-how-to-manage-versions] | 10 個字元，限制為英數字元和句號 (.) |

*預設字元上限為 50 個字元。

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>名稱唯一性

使用以下命名唯一性規則。

以下必須在 LUIS 應用中是唯一的:

* 版本名稱
* 意圖
* 實體
* 角色

以下必須在應用範圍內是唯一的:

* 短語清單

## <a name="object-naming"></a>物件命名

請勿在以下名稱中使用以下字元。

|Object|排除字元|
|--|--|
|意向、實體和角色名稱|`:`<br>`$` <br> `&`|
|版本名稱|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>金鑰使用量

Language Understand 有不同的金鑰，一種適用於撰寫，一種適用於查詢預測端點。 若要深入了解金鑰類型之間的差異，請參閱 [LUIS 中的撰寫與查詢預測端點金鑰](luis-concept-keys.md)。

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>資源金鑰限制

資源鍵對創作和終結點有不同的限制。 LUIS 預測查詢終結點密鑰僅適用於終結點查詢。

* 每個 Azure 創作資源 500 個應用程式

|Key|編寫|端點|目的|
|--|--|--|--|
|入門|1 百萬個/月，5 個/秒|1 千個/月，5 個/秒|撰寫 LUIS 應用程式|
|F0 - 免費套餐 |1 百萬個/月，5 個/秒|1 萬個/月，5 個/秒|查詢 LUIS 端點|
|S0 - 基本層|-|50 個/秒|查詢 LUIS 端點|
|S0 - 標準層|-|50 個/秒|查詢 LUIS 端點|
|[情感分析整合](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|無需其他 Azure 資源即可添加情緒資訊(包括關鍵短語數據提取)。 |
|[語音整合](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|單位成本 1,00 個端點請求|將口語語句轉換成文字語句並傳回 LUIS 結果|

[詳細了解定價。][pricing]

## <a name="keyboard-controls"></a>鍵盤控制項

|鍵盤輸入 | 描述 |
|--|--|
|Ctrl+E|在語句清單上的權杖和實體之間切換|

## <a name="website-sign-in-time-period"></a>網站登入時間段

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
