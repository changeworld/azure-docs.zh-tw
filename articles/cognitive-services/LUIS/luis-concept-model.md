---
title: 使用模型設計-LUIS
description: 語言理解會提供數種類型的模型。 有些模型可用於多種方式。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 933588f96570e931cdc627aaae82bee1037bbdaa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591874"
---
# <a name="design-with-intent-and-entity-models"></a>使用意圖和實體模型設計

語言理解提供兩種類型的模型，供您定義應用程式架構。 您的應用程式架構會決定您從新使用者語句的預測收到哪些資訊。

應用程式架構是從您使用[機器教學](#authoring-uses-machine-teaching)所建立的模型建立而成：
* [意圖](#intents-classify-utterances)分類使用者語句
* [實體](#entities-extract-data)從語句解壓縮資料

## <a name="authoring-uses-machine-teaching"></a>撰寫使用機器教學

LUIS 的機器教學方法可讓您輕鬆地將概念告訴機器。 瞭解_機器學習_服務並不需要使用 LUIS。 相反地，身為老師，您會提供概念的範例，並說明如何使用其他相關概念來模型化概念，藉此將概念傳達給 LUIS。 身為老師，您也可以藉由識別和修正預測錯誤來以互動方式改善 LUIS 的模型。

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>意圖分類語句

意圖會分類範例語句，以教授 LUIS 的意圖。 意圖中的範例語句會當做語句的正面範例使用。 這些相同的語句會當做所有其他意圖中的負面範例使用。

假設有一個應用程式需要決定使用者訂購書籍的意圖，以及需要客戶交貨位址的應用程式。 此應用程式有兩個意圖： `OrderBook` 和 `ShippingLocation` 。

下列語句是意圖的**正向範例** `OrderBook` ，以及和意圖的**負數範例** `ShippingLocation` `None` ：

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>實體會將資料解壓縮

實體代表您要從語句中解壓縮的資料單位。 機器學習的實體是包含子實體的最上層實體，也就是機器學習的實體。

機器學習實體的範例是平面票證的訂單。 在概念上，這是具有許多較小資料單位的單一交易，例如日期、時間、基座數量、基座類型，例如第一個類別或教練、原始位置、目的地位置，以及飲食選擇。

## <a name="intents-versus-entities"></a>意圖與實體

意圖是_整個_語句的所需結果，而實體是從語句中解壓縮的資料片段。 意圖通常會系結至用戶端應用程式應採取的動作。 實體是執行此動作所需的資訊。 從程式設計的觀點來看，意圖會觸發方法呼叫，而實體會當做該方法呼叫的參數來使用。

此語句_必須_具有意圖，而且_可能會_有實體：

`Buy an airline ticket from Seattle to Cairo`

這個語句有單一的意圖：

* 購買平面票證

此語句_可能_有數個實體：

* 西雅圖的位置（原點）和 Cairo （目的地）
* 單一票證的數量

## <a name="entity-model-decomposition"></a>實體模型分解

LUIS 支援使用撰寫 Api 來進行_模型分解_，將概念細分成較小的部分。 這可讓您放心地建立模型，以自信地建造和預測各種不同的元件。

模型分解具有下列部分：

* [意圖](#intents-classify-utterances)
    * [功能](#features)
* [機器學習的實體](reference-entity-machine-learned-entity.md)
    * 子實體（也是機器學習的實體）
        * [功能](#features)
            * [片語清單](luis-concept-feature.md)
            * [非機器學習的實體](luis-concept-feature.md)，例如[正則運算式](reference-entity-regular-expression.md)、[清單](reference-entity-list.md)和預先建立的[實體](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>功能

[功能](luis-concept-feature.md)是您的系統觀察到的資料特徵或屬性。 機器學習服務功能提供 LUIS 重要的提示，讓您在何處尋找會區分概念的專案。 這些是 LUIS 可以使用但不是硬規則的提示。 這些提示會與標籤搭配使用，以尋找資料。

## <a name="patterns"></a>模式

當數個語句非常相似時，[模式](luis-concept-patterns.md)是設計來改善精確度。 模式可讓您取得更精確的意圖，而不需提供更多的語句。

## <a name="extending-the-app-at-runtime"></a>在執行時間擴充應用程式

應用程式的架構（模型和功能）會定型併發行至預測端點。 您可以將[新資訊](schema-change-prediction-runtime.md)連同使用者的語句傳遞至預測端點，以擴大預測。

## <a name="next-steps"></a>後續步驟

* 瞭解[意圖](luis-concept-intent.md)和[實體](luis-concept-entity-types.md)。
* 深入瞭解[功能](luis-concept-feature.md)