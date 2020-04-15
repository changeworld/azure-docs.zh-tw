---
title: 規劃您的應用程式 - LUIS
description: 概述相關的應用程式意圖和實體，然後在 Language Understanding Intelligent Service (LUIS) 中建立應用程式計畫。
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382295"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>使用主題網域和資料提取規劃 LUIS 應用架構

LUIS 應用程式架構包含與您的主題[網域](luis-glossary.md#domain)相關的[意圖](luis-glossary.md#intent)與[實體](luis-glossary.md#entity)。 意圖對使用者[陳述](luis-glossary.md#utterance)進行分類,實體從使用者陳述中提取數據。

## <a name="identify-your-domain"></a>識別您的領域

LUIS 應用以主題域為中心。 例如,您可能有一個旅行應用來處理機票、航班、酒店和租車的預訂。 另一個應用程式則可以提供與運動、追蹤健身進度及設定目標相關的內容。 識別域可説明您找到與您的域相關的單詞或短語。

> [!TIP]
> LUIS 有針對許多常見案例提供[預先建置的領域](luis-how-to-use-prebuilt-domains.md)。 請確認您是否能使用預先建置的領域作為應用程式的起點。

## <a name="identify-your-intents"></a>識別您的意圖

考慮[對應用程式](luis-concept-intent.md)任務很重要的意圖。

讓我們以一個旅遊應用程式作為例子。這個應用程式能夠預訂航班，並可查看使用者目的地的天氣。 您可以定義這些操作`BookFlight``GetWeather`的和意圖。

在具有更多函數的更複雜的應用中,您有更多的意圖,並且應該仔細定義它們,以便意圖不會太具體。 `BookFlight`例如,`BookHotel`並且可能需要是單獨的意圖,`BookInternationalFlight`但`BookDomesticFlight`可能太相似了。

> [!NOTE]
> 最佳做法是僅使用必要的意圖數目來執行應用程式的功能。 若您定義過多的意圖，LUIS 可能會無法正確地分類語句。 如果定義太少,它們可能過於一般,以至於它們重疊。

如果不需要識別總體用戶意圖,請將所有範例使用者陳述添加到意圖中`None`。 如果應用增長到需要更多意圖,則可以稍後創建它們。

## <a name="create-example-utterances-for-each-intent"></a>建立每個意圖的範例語句

首先,避免為每個意圖創建過多的話語。 確定意圖後,請按意圖創建 15 到 30 個範例陳述。 每個陳述應不同於以前提供的話語。 話語中的多種內容包括總字數、單字選擇、動詞時態和[標點符號](luis-reference-application-settings.md#punctuation-normalization)。

有關詳細資訊,請參閱瞭解[LUIS 應用的良好表述](luis-concept-utterance.md)。

## <a name="identify-your-entities"></a>識別您的實體

在範例語句中，識別您要擷取的實體。 要預訂航班,您需要目的地、日期、機票類別和艙位等資訊。 為這些資料類型建立實體,然後在範例陳述中標記[實體](luis-concept-entity-types.md)。 實體對於實現意圖非常重要。

確定要在應用中使用哪些實體時,請記住,存在不同類型的實體來捕獲對象類型之間的關係。 [LUIS 中的實體](luis-concept-entity-types.md)能提供有關這些不同類型的詳細資料。

> [!TIP]
> LUIS 為常見的工作階段使用者方案提供[預建的實體](luis-prebuilt-entities.md)。 請考慮使用預建構的實體作為應用程式開發的起點。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [學習LUIS開發生活](luis-concept-app-iteration.md)

