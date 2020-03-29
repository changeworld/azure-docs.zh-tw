---
title: 詞彙表 - LUIS
titleSuffix: Azure Cognitive Services
description: 此詞彙說明使用 LUIS API Service 時可能遇到的字詞。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221503"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>常用詞彙和概念的語言理解詞彙
Language Understanding (LUIS) 詞彙說明使用 LUIS API Service 時可能遇到的字詞。

## <a name="active-version"></a><a name="active-version"></a>作用中版本

使用中的 LUIS 版本是收到模型任何變更的版本。 在[LUIS](luis-reference-regions.md)門戶中，如果要對非活動版本的版本進行更改，則需要首先將該版本設置為活動版本。

## <a name="authoring"></a><a name="authoring"></a>編寫

創作是使用[LUIS](luis-reference-regions.md)門戶或[創作 API](https://go.microsoft.com/fwlink/?linkid=2092087)創建、管理和部署[LUIS 應用](#luis-app)的能力。

## <a name="authoring-key"></a><a name="authoring-key"></a>創作金鑰

先前命名為「程式設計」金鑰。 用來編寫應用程式。 不用於生產環境層級的端點查詢。 如需詳細資訊，請參閱[金鑰限制](luis-boundaries.md#key-limits)。

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>批次處理測試 JSON 檔

批次處理測試是使用一致且已知的使用者話語測試集驗證當前 LUIS 應用模型的能力。 批次處理測試在[JSON 格式化檔中](luis-concept-batch-test.md#batch-file-format)定義。

另請參閱：
* [概念](luis-concept-batch-test.md)
* [如何](luis-how-to-batch-test.md)
* [教學課程](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>共同作業者

協作者/參與者不是應用[的擁有者](#owner)，但具有添加、編輯和刪除意圖、實體和陳述的相同許可權。

## <a name="contributor"></a><a name="contributor"></a>貢獻

貢獻者與[合作者](#collaborator)是一回事。

## <a name="descriptor"></a><a name="descriptor"></a>描述 符

描述[符是在訓練](#features)時應用於模型的功能，包括[片語清單](#phrase-list)和[實體](#entity)。 

## <a name="domain"></a><a name="domain"></a>域

在 LUIS 的脈絡中，**領域**是一個知識的範圍。 您的領域與您的應用程式知識範圍有關。 這可以是一般的範圍，例如旅行社應用程式。 旅行社應用程式也可以只與您公司的資訊範圍有關，例如特定的地理位置、語言和服務。

## <a name="endpoint"></a><a name="endpoint"></a>端點

[LUIS 端點](https://go.microsoft.com/fwlink/?linkid=2092356) URL 是您編寫和發佈 [LUIS 應用程式](#luis-app)之後，送出 LUIS 查詢的位置。 端點 URL 包含已發佈應用程式的區域，以及應用程式識別碼。 您可以在應用程式的[金鑰和端點](luis-how-to-azure-subscription.md)**** 頁面上找到端點，或者可以從 [Get App Info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API 取得端點 URL。

## <a name="entity"></a><a name="entity"></a>實體

[實體](luis-concept-entity-types.md)是[語句](luis-concept-utterance.md)中的重要字詞，描述與[意圖](luis-concept-intent.md)有關的資訊，有時候不可或缺。 實體在 LUIS 中基本上是一種資料類型。

## <a name="f-measure"></a><a name="f-measure"></a>F 量值

在[批次測試](luis-interactive-test.md#batch-testing)中，是指測試精確度的量值。

## <a name="false-negative-fn"></a><a name="false-negative"></a>假負數 （FN）

在[批次測試](luis-interactive-test.md#batch-testing)中，資料點代表您的應用程式不正確預測了不存在目標意圖/實體的語句。

## <a name="false-positive-fp"></a><a name="false-positive"></a>誤報 （FP）

在[批次測試](luis-interactive-test.md#batch-testing)中，資料點代表您的應用程式不正確預測了存在目標意圖/實體的語句。

## <a name="features"></a><a name="features"></a>特徵

在機器學習中，[特性](luis-concept-feature.md)是您的系統觀察到的資料特徵或屬性。

## <a name="intent"></a><a name="intent"></a>意圖

[意圖](luis-concept-intent.md)表示使用者要執行的任務或操作。 它是使用者輸入中表達的目的或目標，例如預訂班機、支付帳單，或尋找新聞文章。 在 LUIS 中，意圖預測是以整個語句為基礎。 相較之下，實體是語句的片段。

## <a name="labeling"></a><a name="labeling"></a>標記

標記或標記是將意圖[話語](#utterance)中的單詞或短語與[實體](#entity)（資料類型）相關聯的過程。

## <a name="luis-app"></a><a name="luis-app"></a>LUIS 應用程式

LUIS 應用程式是用於自然語言處理的語言模型的集合，包括[意圖](#intent)、[實體](#entity)和標記[的話語](#utterance)。

## <a name="owner"></a><a name="owner"></a>擁有者

每個應用程式都有一個擁有者，這是建立應用程式的人員。 擁有者可以新增[共同作業者](#collaborator)。

## <a name="patterns"></a><a name="pattern"></a>模式
先前的「模式」(Pattern) 特性已被[模式](luis-concept-patterns.md) (Patterns) 取代。 使用模式可以透過提供較少的定型範例提高預測準確度。

## <a name="phrase-list"></a><a name="phrase-list"></a>片語清單

[片語清單](luis-concept-feature.md)包括屬於同一類且必須類似處理的值（如城市或產品名稱）的一組值（單詞或短語）。 可互換的清單會被視為同義字。

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>預建網域

[預先建置的領域](luis-how-to-use-prebuilt-domains.md)是針對特定領域設定的 LUIS 應用程式，例如家庭自動化 (HomeAutomation) 或餐廳預約 (RestaurantReservation)。 意圖、語句和實體會針對此領域設定。

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>預建實體

[預先建置的實體](luis-prebuilt-entities.md)是 LUIS 針對一般的資訊類型 (例如數字、URL 和電子郵件) 提供的實體。 您可以選擇在您的應用程式中加入預先建置的實體。

## <a name="precision"></a><a name="precision"></a>精度
在[批次測試](luis-interactive-test.md#batch-testing)中，精確度 (也稱為陽性預測值) 是相關語句在所擷取語句之間的比例。

## <a name="programmatic-key"></a><a name="programmatic-key"></a>程式設計金鑰

已重新命名為[編寫金鑰](#authoring-key)。

## <a name="publish"></a><a name="publish"></a>發佈

發佈表示讓 LUIS 使用中版本在暫存或生產環境[端點](#endpoint)上可用。  

## <a name="quota"></a><a name="quota"></a>配額

LUIS 配額是 [Azure 訂用帳戶層](https://aka.ms/luis-price-tier)的限制。 LUIS 配額會受到每秒要求數 (HTTP 狀態 429) 和一個月的要求總數 (HTTP 狀態 403) 兩者的限制。

## <a name="recall"></a><a name="recall"></a>召回
在[批次測試](luis-interactive-test.md#batch-testing)中，記憶 (也稱為敏感度) 是 LUIS 一般化的功能。

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>語意字典
[清單實體] 頁面和 [片語清單] 頁面都提供語意字典。 語意字典根據目前的範圍提供單字的建議。

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>情緒分析
情感分析提供由[文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)提供的語句的正值或負值。

## <a name="speech-priming"></a><a name="speech-priming"></a>語音預備

語音預備可讓您的語音服務準備好與您的 LUIS 模型搭配運作。

## <a name="spelling-correction"></a><a name="spelling-correction"></a>拼字校正

啟用 Bing 拼字檢查工具，可在預測之前更正語句中拼字錯誤的單字。

## <a name="starter-key"></a><a name="starter-key"></a>啟動鍵

首次使用 LUIS 時要使用的可用金鑰。

## <a name="structure"></a><a name="structure"></a>結構

將結構添加到機器學習的實體，以向子元件提供描述項（功能）和約束（正則運算式或清單實體）。

## <a name="subscription-key"></a><a name="subscription-key"></a>訂閱金鑰

訂閱金鑰是與[在 Azure 中創建的](luis-how-to-azure-subscription.md)LUIS 服務關聯的**預測終結點**金鑰。 這個金鑰不是[編寫金鑰](#programmatic-key)。 如果您有端點金鑰，則應該針對任何端點要求使用，而不是針對撰寫金鑰使用。 您可以在 [LUIS](luis-reference-regions.md) 網站中[金鑰和端點**** 頁面](luis-how-to-azure-subscription.md)底部的端點 URL 內，查看您目前的端點金鑰。 這是 **subscription-key** 名稱/值組的值。

## <a name="test"></a><a name="test"></a>測試

[測試](luis-interactive-test.md#test-your-app) LUIS 應用程式表示將語句傳入 LUIS 並檢視 JSON 結果。

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>時區位移

端點包含 timezoneOffset。 這是您想要加入 datetimeV2 預先建置的實體或從中移除的分鐘數。 例如，如果語句是「現在是幾點？」，傳回的 datetimeV2 是用戶端要求的目前時間。 如果您的用戶端要求是來自 Bot 或與您 Bot 使用者不同的其他應用程式，則您應該傳入 Bot 與使用者之間的時差。

請參閱[變更預先建置 datetimeV2 實體的時區](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity)。

## <a name="token"></a><a name="token"></a>權杖
語彙基元是實體中可以加上標籤的最小單位。 Token 化是以應用程式的[文化特性](luis-language-support.md#tokenization)為基礎所進行。

## <a name="train"></a><a name="train"></a>火車

定型是教導 LUIS 自最後一次定型後使用中版本任何變更的程序。

## <a name="true-negative-tn"></a><a name="true-negative"></a>真否定 (TN)

在[批次測試](luis-interactive-test.md#batch-testing)中，資料點代表您的應用程式正確預測了不存在目標意圖/實體的語句。

## <a name="true-positive-tp"></a><a name="true-positive"></a>真肯定 (TP)

在[批次測試](luis-interactive-test.md#batch-testing)中，資料點代表您的應用程式正確預測了存在目標意圖/實體的語句。

## <a name="utterance"></a><a name="utterance"></a>語句

語句是一種自然語言片語，例如「訂兩張機票下星期二到西雅圖」。 意圖中會加入範例語句。

## <a name="version"></a><a name="version"></a>版本

LUIS [版本](luis-how-to-manage-versions.md)是與 LUIS 應用程式識別碼和已發佈端點相關聯的特定資料模型。 每個 LUIS 應用程式至少會有一個版本。
