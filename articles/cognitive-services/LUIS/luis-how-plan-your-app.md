---
title: 規劃您的應用程式-LUIS
description: 概述相關的應用程式意圖和實體，然後在 Language Understanding Intelligent Service (LUIS) 中建立應用程式計畫。
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: 3463078309978ae34918f27a9d75c1dabd59ae66
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654120"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>規劃您的 LUIS 應用程式架構與主旨網域和資料提取

LUIS 應用程式架構包含與您的主體[網域](luis-glossary.md#domain)相關的[意圖](luis-glossary.md#intent)和[實體](luis-glossary.md#entity)。 這些意圖會分類使用者[語句](luis-glossary.md#utterance)，而實體會從使用者語句解壓縮資料。

## <a name="identify-your-domain"></a>識別您的領域

LUIS 應用程式是以主體網域為中心。 例如，您可能有一個旅遊應用程式，負責處理票證、航班、旅館和計程車輛的預訂。 另一個應用程式則可以提供與運動、追蹤健身進度及設定目標相關的內容。 識別網域可協助您尋找與您的網域相關的單字或片語。

> [!TIP]
> LUIS 有針對許多常見案例提供[預先建置的領域](luis-how-to-use-prebuilt-domains.md)。 請確認您是否能使用預先建置的領域作為應用程式的起點。

## <a name="identify-your-intents"></a>識別您的意圖

請考慮對應用程式工作而言很重要的[意圖](luis-concept-intent.md)。

讓我們以一個旅遊應用程式作為例子。這個應用程式能夠預訂航班，並可查看使用者目的地的天氣。 您可以 `BookFlight` `GetWeather` 為這些動作定義和意圖。

在具有更多功能的更複雜應用程式中，您會有更多意圖，而且您應該謹慎定義它們，讓意圖不是特定的。 例如， `BookFlight` 和 `BookHotel` 可能需要是不同的意圖，但 `BookInternationalFlight` 和 `BookDomesticFlight` 可能太相似。

> [!NOTE]
> 最佳做法是僅使用必要的意圖數目來執行應用程式的功能。 若您定義過多的意圖，LUIS 可能會無法正確地分類語句。 如果您定義的太少，它們可能會重迭。

如果您不需要識別整體使用者意圖，請將所有範例使用者語句新增至 `None` 意圖。 如果您的應用程式成長到需要更多意圖，您可以稍後再建立。

## <a name="create-example-utterances-for-each-intent"></a>建立每個意圖的範例語句

一開始，請避免為每個意圖建立太多語句。 決定意圖之後，請建立每個意圖15到30個範例語句。 每個語句應該與先前提供的語句不同。 語句中的絕佳功能包括整體字數統計、單字選擇、動詞時態和[標點符號](luis-reference-application-settings.md#punctuation-normalization)。

如需詳細資訊，請參閱[瞭解 LUIS apps 的良好語句](luis-concept-utterance.md)。

## <a name="identify-your-entities"></a>識別您的實體

在範例語句中，識別您要擷取的實體。 若要預訂航班，您需要「目的地」、「日期」、「航空公司」、「票證」類別和「旅遊」等資訊。 建立這些資料類型的實體，然後在範例語句中標示[實體](luis-concept-entity-types.md)。 實體對於完成意圖很重要。

在決定要在您的應用程式中使用哪些實體時，請記住，有不同類型的實體可用於捕捉物件類型之間的關聯性。 [LUIS 中的實體](luis-concept-entity-types.md)能提供有關這些不同類型的詳細資料。

> [!TIP]
> LUIS 提供[預先](luis-prebuilt-entities.md)建立的實體，適用于常見的對話式使用者案例。 請考慮使用預先建立的實體作為應用程式開發的起點。

## <a name="resolution-with-intent-or-entity"></a>使用意圖或實體解析？

在許多情況下，特別是在使用自然對話時，使用者會提供一個語句，其中可以包含一個以上的函數或意圖。 為了解決這個情況，一般的經驗法則是瞭解輸出的標記法可以同時在意圖和實體中完成。 此標記法應該是您的用戶端應用程式動作的可映射，而且不需要僅限於意圖。

**Int-ent-** 系結是指動作（通常視為意圖）也可以當作實體來捕捉，並在輸出 JSON 中依賴此形式，以便將其對應至特定的動作。 _否定_是利用這項依賴意圖和實體進行完整解壓縮的常見用法。

請考慮下列兩個語句，其中非常接近考慮 word 選擇，但結果不同：

|語句|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

除了有兩個不同的意圖，請使用 `FlightAction` 機器學習實體建立單一意圖。 機器學習服務實體應針對排程和取消要求以及來源或目的地位置，解壓縮動作的詳細資料。

`FlightAction`實體會結構化在機器學習服務實體和子實體的下列 suedo 架構中：

* FlightAction
    * 動作
    * 來源
    * Destination

若要協助解壓縮，請將功能新增至子實體。 您將根據預期會在 [使用者語句] 中看到的詞彙，以及您想要在預測回應中傳回的值，來選擇您的功能。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [學習 LUIS 開發 lifecylce](luis-concept-app-iteration.md)

