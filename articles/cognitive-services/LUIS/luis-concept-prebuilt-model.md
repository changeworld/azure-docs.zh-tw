---
title: 預構建型號 - LUIS
titleSuffix: Azure Cognitive Services
description: 預先建置的模型會提供定義域、意圖、語句及實體。 您可以使用預先建置的定義域來開始您的應用程式，或是稍後將相關的定義域新增至您的應用程式。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280845"
---
# <a name="prebuilt-models"></a>預先建置的模型

預先建置的模型會提供定義域、意圖、語句及實體。 您可以使用預構建的模型啟動應用，或稍後向應用添加相關模型。 

## <a name="types-of-prebuilt-models"></a>預先建置的模型類型

LUIS 提供三種類型的預構建模型。 您可以隨時將各個模型新增至您的應用程式。 

|模型類型|Includes|
|--|--|
|[域](luis-reference-prebuilt-domains.md)|意圖、語句、實體|
|對應方式|意圖、語句|
|[實體](luis-reference-prebuilt-entities.md)|僅限實體| 

## <a name="prebuilt-domains"></a>預先建置的定義域

語言理解 （LUIS） 提供*預構建的域*，它們是預先訓練的[意向](luis-how-to-add-intents.md)模型和[實體](luis-concept-entity-types.md)，它們共同適用于域或用戶端應用程式的通用類別。 

預先建置的定義域已定型，並已準備好新增到您的 LUIS 應用程式。 將預構建域的意圖和實體添加到應用後，將完全自訂。 

> [!TIP]
> 預建網域中的意圖和實體一起運作的效果最好。 最好儘可能結合來自相同網域的意圖和實體。
> 公用程式預先建置的定義域含有可供您自訂以在任何定義域中使用的意圖。 例如，您可以將 `Utilities.Repeat` 新增至您的應用程式並訓練它辨識使用者可能想要在您的應用程式中重複的動作。 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>變更預建網域意圖的行為

您可能會發現，預建網域包含的意圖類似於您希望存在 LUIS 應用程式中，但行為不同的意圖。 例如，"**地點**"預建域提供了進行餐廳`MakeReservation`預訂的意圖，但您希望應用使用該意圖進行酒店預訂。 在這種情況下，您可以通過向預訂酒店的意圖添加示例陳述，然後重新訓練應用來修改該意圖的行為。 

您可以在[預建網域參考](./luis-reference-prebuilt-domains.md)中發現完整的預建網域清單。

## <a name="prebuilt-intents"></a>預先建置的意圖

LUIS 為其每個預構建域提供預構建的意圖及其陳述。 您可以新增意圖而不新增整個定義域。 添加意圖是向應用添加意圖及其陳述的過程。 意圖名稱和語句清單都可供修改。  

## <a name="prebuilt-entities"></a>預先建置的實體

LUIS 包含一組預先建置的實體，用於辨識常見的資訊類型，例如日期、時間、數字、計量及貨幣。 預先建置的實體支援會因 LUIS 應用程式的文化特性而異。 如需 LUIS 所支援預先建置實體的完整清單，包括文化特性支援的實體在內，請參閱[預先建置的實體參考](./luis-reference-prebuilt-entities.md)。

當應用程式中包含預先建置的實體時，其預測會包含在已發佈的應用程式中。 預先建置實體的行為已預先定型且**無法**修改。 

> [!NOTE]
> **builtin.datetime** 已被取代。 它已由 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md) 取代，可供辨識日期與時間範圍，以及改善辨識語意模糊的日期與時間。

## <a name="next-steps"></a>後續步驟

了解如何[將預先建置的實體](luis-prebuilt-entities.md)新增到您的應用程式。
