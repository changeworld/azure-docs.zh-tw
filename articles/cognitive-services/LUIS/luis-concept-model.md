---
title: 使用模型設計-LUIS
titleSuffix: Azure Cognitive Services
description: 語言理解會提供數種類型的模型。 有些模型可用於多種方式。
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221329"
---
# <a name="design-with-intent-and-entity-models"></a>使用意圖和實體模型設計 

語言理解會提供數種類型的模型。 有些模型可用於多種方式。 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 撰寫使用機器教學

LUIS 可讓人們輕鬆地將概念告訴機器。 然後，機器可以建立模型（功能近似于分類和擷取器等概念），以用來增強智慧型應用程式。 雖然 LUIS 是由機器學習服務所提供，但對機器學習服務的瞭解並不需要使用它。 相反地，機器老師會藉由顯示概念的正面和負面範例，並說明如何使用其他相關概念來模型化概念，來將概念傳達給 LUIS。 教師也可以藉由找出並修正預測錯誤，以互動方式改善 LUIS 的模型。 

## <a name="v3-authoring-model-decomposition"></a>V3 撰寫模型分解

LUIS 支援使用 V3 撰寫 Api 來進行_模型分解_，將模型細分成較小的部分。 這可讓您放心地建立模型，以自信地建造和預測各種不同的元件。

模型分解具有下列部分：

* [意圖](#intents-classify-utterances)
    * 功能所提供的[描述](#descriptors-are-features)項
* [機器學習的實體](#machine-learned-entities)
    * [子元件](#entity-subcomponents-help-extract-data)（也是機器學習的實體）
        * 功能所提供的[描述](#descriptors-are-features)項 
        * 非機器學習的實體（例如正則運算式和清單）所提供的[條件約束](#constraints-are-text-rules)

## <a name="v2-authoring-models"></a>V2 撰寫模型

LUIS 支援使用 V2 撰寫 Api 的複合實體。 這會提供類似的模型分解，但與 V3 模型分解不同。 建議的模型架構是移至 V3 撰寫 Api 中的模型分解。 

## <a name="intents-classify-utterances"></a>意圖分類語句

意圖會分類範例語句，以教授 LUIS 的意圖。 意圖中的範例語句會當做語句的正面範例使用。 這些相同的語句會當做所有其他意圖中的負面範例使用。

假設有一個應用程式需要決定使用者訂購書籍的意圖，以及需要客戶交貨位址的應用程式。 此應用程式有兩個意圖： `OrderBook` 和 `ShippingLocation`。

下列語句是 `OrderBook` 意圖的**正向範例**，以及 `ShippingLocation` 和 `None` 意圖的**負面範例**： 

`Buy the top-rated book on bot architecture.`

設計良好的意圖及其範例語句的結果，是高意圖預測。 

## <a name="entities-extract-data"></a>實體會將資料解壓縮

實體代表您要從語句中解壓縮的資料單位。 

### <a name="machine-learned-entities"></a>機器學習的實體

機器學習實體是包含子元件（也就是機器學習的實體）的最上層實體。 

**使用機器學習的實體**：

* 用戶端應用程式需要的子元件
* 協助機器學習服務演算法分解實體

每個子元件都可以有：

* 元件
* 條件約束（正則運算式實體或清單實體）
* 描述項（如片語清單的功能） 

機器學習實體的範例是平面票證的訂單。 在概念上，這是具有許多較小資料單位的單一交易，例如日期、時間、基座數量、基座類型，例如第一個類別或教練、原始位置、目的地位置，以及飲食選擇。


### <a name="entity-subcomponents-help-extract-data"></a>實體子元件可協助將資料解壓縮

子元件是機器學習的父實體內機器學習的子實體。 

**使用子元件來**執行下列動作：

* 分解機器學習實體（父實體）的元件。

以下代表機器學習的實體，其中包含所有這些不同的資料片段：

* TravelOrder （機器學習的實體）
    * DateTime （預先建立的 datetimeV2）
    * 位置（機器學習的實體）
        * 來源（透過內容找到的角色，例如 `from`）
        * 目的地（透過內容找到的角色，例如 `to`）
    * 座位（機器學習的實體）
        * 數量（預先建立的數位）
        * 品質（機器學習的實體，包含片語清單的描述項）
    * 餐費（機器學習實體，具有清單實體的條件約束做為食物選擇）

部分資料（例如，原始位置和目的地位置）應從語句的內容中學習，也許是 `from` 和 `to`這類用語。 您可以使用完全相符的字串（`Vegan`）或預先建立的實體（`Seattle` 和 `Cairo`的 geographyV2）來解壓縮資料的其他部分。 

您可以設計資料的比對方式，以及您所選擇的模型以及如何設定它們。

### <a name="constraints-are-text-rules"></a>條件約束為文字規則

條件約束是由非機器學習的實體（例如正則運算式實體或清單實體）所提供的文字比對規則。 條件約束會在預測時間套用，以限制預測並提供用戶端應用程式所需的實體解析。 您會在撰寫子元件時定義這些規則。 

**使用條件約束**：
* 當您知道要解壓縮的確切文字時。

條件約束包括：

* [正則運算式](reference-entity-regular-expression.md)實體
* [列出](reference-entity-list.md)實體 
* [預先](luis-reference-prebuilt-entities.md)建立的實體

繼續進行飛機票證的範例，機場代碼可以在清單實體中，以符合完全相符的文字。 

針對機場清單，西雅圖的清單專案是城市名稱、`Seattle` 和西雅圖的同義字包括西雅圖的機場代碼以及周圍的城鎮和城市：

|`Seattle` 列出實體同義字|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

如果您只想要辨識機場代碼的3個字母代碼，請使用正則運算式做為條件約束。 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>意圖與實體

意圖是_整個_語句的所需結果，而實體是從語句中解壓縮的資料片段。 意圖通常會系結至用戶端應用程式應採取的動作，而實體則是執行此動作所需的資訊。 從程式設計的觀點來看，意圖會觸發方法呼叫，而實體會當做該方法呼叫的參數來使用。

此語句_必須_具有意圖，而且_可能會_有實體：

`Buy an airline ticket from Seattle to Cairo`

這個語句有單一的意圖：

* 購買平面票證

此語句_可能_有數個實體：

* 西雅圖的位置（原點）和 Cairo （目的地）
* 單一票證的數量

## <a name="descriptors-are-features"></a>描述項是功能

描述項是在定型時間套用至模型的功能，包括片語清單和實體。 

**當您想要進行下列動作時，請使用描述**項：

* 提升描述項所識別之單字和片語的重要性
* 讓 LUIS 針對描述項建議新的文字或片語
* 修正定型資料的錯誤

## <a name="next-steps"></a>後續步驟

* 瞭解[意圖](luis-concept-intent.md)和[實體](luis-concept-entity-types.md)。 