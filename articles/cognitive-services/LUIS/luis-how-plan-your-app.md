---
title: 規劃您的應用程式-LUIS
description: 概述相關的應用程式意圖和實體，然後在 Language Understanding Intelligent Service (LUIS) 中建立應用程式計畫。
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 38d80a77303e248ab137f87f8063b5cbc358f51a
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892910"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>使用主體網域和資料解壓縮規劃 LUIS 應用程式架構

LUIS 應用程式架構包含與您的主體[網域](luis-glossary.md#domain)相關的[意圖](luis-glossary.md#intent)和[實體](luis-glossary.md#entity)。 這些意圖會分類使用者 [語句](luis-glossary.md#utterance)，而實體則會從使用者語句中提取資料。

## <a name="identify-your-domain"></a>識別您的領域

LUIS 應用程式是以主體網域為中心。 例如，您可能有一個旅遊應用程式，負責處理票證、航班、旅館和租賃車輛的預約。 另一個應用程式則可以提供與運動、追蹤健身進度及設定目標相關的內容。 識別網域可協助您尋找與您的網域相關的單字或片語。

> [!TIP]
> LUIS 有針對許多常見案例提供[預先建置的領域](luis-how-to-use-prebuilt-domains.md)。 請確認您是否能使用預先建置的領域作為應用程式的起點。

## <a name="identify-your-intents"></a>識別您的意圖

請考慮對應用程式工作而言很重要的 [意圖](luis-concept-intent.md) 。

讓我們以一個旅遊應用程式作為例子。這個應用程式能夠預訂航班，並可查看使用者目的地的天氣。 您可以定義 `BookFlight` `GetWeather` 這些動作的和意圖。

在更複雜的應用程式中，有更多的功能，您會有更多意圖，而您應謹慎定義這些意圖，使意圖不太特定。 例如， `BookFlight` 和 `BookHotel` 可能需要是不同的意圖，但 `BookInternationalFlight` `BookDomesticFlight` 可能太類似。

> [!NOTE]
> 最佳做法是僅使用必要的意圖數目來執行應用程式的功能。 若您定義過多的意圖，LUIS 可能會無法正確地分類語句。 如果您定義太少，它們可能會重迭。

如果您不需要識別整體使用者意圖，請將所有範例使用者語句新增至 `None` 意圖。 如果您的應用程式擴大到需要更多意圖，您可以稍後再建立。

## <a name="create-example-utterances-for-each-intent"></a>建立每個意圖的範例語句

首先，請避免為每個意圖建立太多語句。 一旦決定意圖之後，請針對每個意圖建立15到30個範例語句。 每個語句應該與先前提供的語句不同。 語句中有一個很好的選擇，包括整體字數統計、單字選擇、動詞時態和 [標點符號](luis-reference-application-settings.md#punctuation-normalization)。

如需詳細資訊，請參閱 [瞭解 LUIS apps 的良好語句](luis-concept-utterance.md)。

## <a name="identify-your-entities"></a>識別您的實體

在範例語句中，識別您要擷取的實體。 若要預訂航班，您需要「目的地」、「日期」、「航空公司」、「票證類別」和「旅遊」類別等資訊。 建立這些資料類型的實體，然後在範例語句中標示 [實體](luis-concept-entity-types.md) 。 實體對於完成意圖來說很重要。

當您決定要在應用程式中使用哪些實體時，請記住，有不同類型的實體可用於捕捉物件類型之間的關聯性。 [LUIS 中的實體](luis-concept-entity-types.md)能提供有關這些不同類型的詳細資料。

> [!TIP]
> LUIS 為常見的交談式使用者案例提供預先建立的 [實體](luis-prebuilt-entities.md) 。 考慮使用預先建立的實體作為應用程式開發的起點。

## <a name="resolution-with-intent-or-entity"></a>有意圖或實體的解決方法？

在許多情況下，尤其是在使用自然對話時，使用者會提供一個可包含多個函式或意圖的語句。 若要解決此情況，一般的經驗法則是瞭解可以在意圖和實體中完成輸出的表示。 此標記法應可針對您的用戶端應用程式動作進行可映射，且不需要限制為意圖。

**Ent** 系結是動作 (通常視為意圖的概念) 也可以被當作實體來捕捉，並且在輸出 JSON 中依賴此形式，讓您可以將其對應至特定動作。 _負_ 的常見用法是利用這項依賴意圖和實體來進行完整的解壓縮。

請考慮下列兩個非常接近 word 選擇但結果不同的語句：

|語句|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

使用機器學習實體來建立單一意圖，而不是使用兩個不同的意圖 `FlightAction` 。 機器學習實體應該針對排程和取消要求以及來源或目的地位置，解壓縮動作的詳細資料。

`FlightAction`實體會結構化在機器學習服務實體和子實體的下列虛擬架構中：

* FlightAction
    * 動作
    * 來源
    * Destination

以協助解壓縮將功能新增至子實體。 您將根據預期會在使用者語句中看到的詞彙，以及您想要在預測回應中傳回的值，來選擇您的功能。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [學習 LUIS 開發 lifecylce](luis-concept-app-iteration.md)

