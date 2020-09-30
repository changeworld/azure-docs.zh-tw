---
title: 預建模型-LUIS
titleSuffix: Azure Cognitive Services
description: 預先建置的模型會提供定義域、意圖、語句及實體。 您可以使用預先建置的定義域來開始您的應用程式，或是稍後將相關的定義域新增至您的應用程式。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 32755ea9dccd2a7d4a0e727c7a006491d5ea3521
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535878"
---
# <a name="prebuilt-models"></a>預先建置的模型

預先建置的模型會提供定義域、意圖、語句及實體。 您可以使用預先建立的模型來啟動您的應用程式，或稍後將相關的模型新增至您的應用程式。 

## <a name="types-of-prebuilt-models"></a>預先建置的模型類型

LUIS 提供三種類型的預建模型。 您可以隨時將各個模型新增至您的應用程式。 

|模型類型|Includes|
|--|--|
|[網域](luis-reference-prebuilt-domains.md)|意圖、語句、實體|
|對應方式|意圖、語句|
|[實體](luis-reference-prebuilt-entities.md)|僅限實體| 

## <a name="prebuilt-domains"></a>預先建置的定義域

Language Understanding (LUIS) 提供預 *建網域*，這是預先定型的 [意圖](luis-how-to-add-intents.md) 和 [實體](luis-concept-entity-types.md) 模型，可針對網域或常見的用戶端應用程式類別一起運作。 

預先建置的定義域已定型，並已準備好新增到您的 LUIS 應用程式。 當您將預建網域的意圖和實體新增至應用程式之後，就可以完全自訂這些意圖和實體。 

> [!TIP]
> 預建網域中的意圖和實體一起運作的效果最好。 最好儘可能結合來自相同網域的意圖和實體。
> 公用程式預先建置的定義域含有可供您自訂以在任何定義域中使用的意圖。 例如，您可以將 `Utilities.Repeat` 新增至您的應用程式並訓練它辨識使用者可能想要在您的應用程式中重複的動作。 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>變更預建網域意圖的行為

您可能會發現，預建網域包含的意圖類似於您希望存在 LUIS 應用程式中，但行為不同的意圖。 例如 **，[預** 建網域] 可提供 `MakeReservation` 進行餐廳保留的意圖，但您希望您的應用程式使用該意圖來進行旅館保留。 在此情況下，您可以藉由將範例語句新增至有關進行旅館預訂的意圖，然後重新定型應用程式，來修改該意圖的行為。 

您可以在[預建網域參考](./luis-reference-prebuilt-domains.md)中發現完整的預建網域清單。

## <a name="prebuilt-intents"></a>預先建置的意圖

LUIS 為其每個預建網域提供預先建立的意圖及其語句。 您可以新增意圖而不新增整個定義域。 新增意圖是將意圖及其語句新增至您應用程式的程式。 意圖名稱和語句清單都可供修改。  

## <a name="prebuilt-entities"></a>預先建置的實體

LUIS 包含一組預先建置的實體，用於辨識常見的資訊類型，例如日期、時間、數字、計量及貨幣。 預先建置的實體支援會因 LUIS 應用程式的文化特性而異。 如需 LUIS 所支援預先建置實體的完整清單，包括文化特性支援的實體在內，請參閱[預先建置的實體參考](./luis-reference-prebuilt-entities.md)。

當應用程式中包含預先建置的實體時，其預測會包含在已發佈的應用程式中。 預先建置實體的行為已預先定型且**無法**修改。 

> [!NOTE]
> **builtin.datetime** 已被取代。 它已由 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md) 取代，可供辨識日期與時間範圍，以及改善辨識語意模糊的日期與時間。

## <a name="next-steps"></a>後續步驟

了解如何[將預先建置的實體](luis-prebuilt-entities.md)新增到您的應用程式。
