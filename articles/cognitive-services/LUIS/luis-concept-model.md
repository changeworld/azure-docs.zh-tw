---
title: 設計與模型 - LUIS
titleSuffix: Azure Cognitive Services
description: 語言理解提供了幾種類型的模型。 某些型號可以多種方式使用。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221329"
---
# <a name="design-with-intent-and-entity-models"></a>意圖和實體模型設計 

語言理解提供了幾種類型的模型。 某些型號可以多種方式使用。 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 創作使用機器教學

LUIS 使人們可以輕鬆地向機器傳授概念。 然後，機器可以構建模型（概念的功能近似，如分類器和提取器），可用於為智慧應用提供動力。 雖然 LUIS 由機器學習提供支援，但無需瞭解機器學習。 相反，機器教師通過展示概念的正面和負面示例，並解釋如何使用其他相關概念對概念進行建模，從而向 LUIS 傳達概念。 教師還可以通過識別和修復預測錯誤，以對話模式改進 LUIS 模型。 

## <a name="v3-authoring-model-decomposition"></a>V3 創作模型分解

LUIS 支援使用 V3 創作 API 進行_模型分解_，將模型分解為更小的部分。 這使您可以自信地構建模型，以構建和預測各個部件。

模型分解具有以下部分：

* [意圖](#intents-classify-utterances)
    * 功能提供的[描述項](#descriptors-are-features)
* [機器學習實體](#machine-learned-entities)
    * [子元件](#entity-subcomponents-help-extract-data)（也是機器學習的實體）
        * 功能提供的[描述項](#descriptors-are-features) 
        * 非機器學習實體（如正則運算式和清單）提供[的約束](#constraints-are-text-rules)

## <a name="v2-authoring-models"></a>V2 創作模型

LUIS 支援具有 V2 創作 API 的複合實體。 這提供了類似的模型分解，但與 V3 模型分解不同。 推薦的模型體系結構是在 V3 創作 API 中移動到模型分解。 

## <a name="intents-classify-utterances"></a>意圖對話語進行分類

意圖對示例陳述進行分類，以向 LUIS 傳授意圖。 意圖中的示例陳述用作陳述的積極示例。 在所有其他意圖中，這些相同的陳述用作負面示例。

考慮需要確定使用者訂購書籍的意圖的應用以及需要客戶送貨位址的應用。 這個程式有兩個意圖：`OrderBook`和`ShippingLocation`。

`OrderBook`以下陳述是意圖**的積極示例**，`ShippingLocation`對 和`None`意圖的**否定示例**： 

`Buy the top-rated book on bot architecture.`

設計良好的意圖及其示例陳述的結果是高意圖預測。 

## <a name="entities-extract-data"></a>實體提取資料

實體表示要從話語中提取的資料單位。 

### <a name="machine-learned-entities"></a>機器學習實體

機器學習實體是包含子元件的頂級實體，這些子元件也是機器學習的實體。 

**使用機器學習的實體**：

* 當用戶端應用程式需要子元件時
* 説明機器學習演算法分解實體

每個子元件可以具有：

* 子元件
* 約束（正則運算式實體或清單實體）
* 描述項（片語清單等功能） 

機器學習實體的一個示例是飛機票的訂單。 從概念上講，這是一個包含許多較小資料單元的單個交易，如日期、時間、座位數量、座位類型（如頭等艙或長途車、原點位置、目的地位置和膳食選擇）。


### <a name="entity-subcomponents-help-extract-data"></a>實體子元件有助於提取資料

子元件是機器學習父實體中的機器學習子實體。 

**使用子元件進行**：

* 分解機器學習實體（父實體）的部分。

以下表示具有所有這些單獨資料段的機器學習實體：

* 旅行訂單（機器學習實體）
    * 日期時間（預構建日期時間V2）
    * 位置（機器學習實體）
        * 起源（通過上下文（如`from`） 找到的角色
        * 目標（通過上下文（如`to`） 找到的角色
    * 座位（機器學習實體）
        * 數量（預建編號）
        * 品質（具有片語清單描述項的機器學習實體）
    * 膳食（機器學習實體，約束清單實體作為食物選擇）

其中一些資料，如原點位置和目的地位置，應該從話語的上下文中學習，也許用 和`from``to`等措詞來瞭解 。 資料的其他部分可以使用確切的字串匹配 （`Vegan`） 或預構建的實體 （和`Seattle``Cairo`的 地理V2） 進行提取。 

您可以設計如何通過選擇哪些模型以及如何配置資料來匹配和提取資料。

### <a name="constraints-are-text-rules"></a>約束是文本規則

約束是文本匹配規則，由非電腦學習實體（如正則運算式實體或清單實體）提供。 在預測時應用約束以限制預測並提供用戶端應用程式所需的實體解析。 在創作子元件時定義這些規則。 

**使用約束**：
* 當你知道要提取的確切文本時。

約束包括：

* [正則運算式](reference-entity-regular-expression.md)實體
* [清單](reference-entity-list.md)實體 
* [預構建](luis-reference-prebuilt-entities.md)實體

繼續使用飛機票的示例，機場代碼可以位於"清單"實體中，用於精確文本匹配。 

對於機場清單，西雅圖的清單條目是城市名稱，`Seattle`西雅圖的同義字包括西雅圖的機場代碼以及周圍的城鎮和城市：

|`Seattle`列出實體同義字|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

如果只想識別機場代碼的 3 個字母代碼，請使用正則運算式作為約束。 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>意圖與實體

意圖是_整個_陳述的預期結果，而實體是從話語中提取的資料片段。 通常，意圖與用戶端應用程式應執行的操作相關聯，而實體是執行此操作所需的資訊。 從程式設計的角度來看，意圖將觸發方法調用，並且實體將用作該方法調用的參數。

此陳述_必須有_意圖，並且_可能具有_實體：

`Buy an airline ticket from Seattle to Cairo`

此表述只有一個意圖：

* 購買機票

此陳述_可能_有多個實體：

* 西雅圖（產地）和開羅（目的地）的位置
* 單張票的數量

## <a name="descriptors-are-features"></a>描述項是功能

描述項是在訓練時應用於模型的功能，包括片語清單和實體。 

**如果要 ： 使用描述項**：

* 提高描述項標識的單詞和短語的重要性
* 讓 LUIS 推薦新的文本或短語來推薦描述項
* 修復訓練資料上的錯誤

## <a name="next-steps"></a>後續步驟

* 瞭解[意圖](luis-concept-intent.md)和[實體](luis-concept-entity-types.md)。 