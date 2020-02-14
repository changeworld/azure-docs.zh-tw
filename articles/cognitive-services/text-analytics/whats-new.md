---
title: 文字分析 API 的新功能
titleSuffix: Text Analytics - Azure Cognitive Services
description: 本文提供 Azure 認知服務文字分析的新版本和功能的相關資訊。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188810"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文字分析 API 有哪些新功能？

文字分析 API 會持續更新。 為了讓您隨時掌握最新的開發，這篇文章為您提供新版本和功能的相關資訊。

## <a name="february-2020"></a>2020年2月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>文字分析 API v3 公開預覽版本的 SDK 支援

作為[整合 AZURE SDK 版本](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)的一部分，文字分析 API v3 SDK 現在可作為下列程式設計語言的公開預覽：
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript （node.js）](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [深入瞭解文字分析 API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>命名實體辨識 v3 公開預覽

現在已可在「命名實體辨識」（NER） v3 公開預覽服務中使用其他實體類型，因為我們會展開文字中找到的一般和個人資訊實體的偵測。 此更新引進[模型版本](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)`2020-02-01`，其中包括：

* 識別下列一般實體類型（僅限英文）：
    * PersonType
    * Products
    * 事件
    * 地緣政治實體（GPE）作為 [位置] 底下的子類型
    * 技能

* 識別下列個人資訊實體類型（僅限英文）：
    * 人員
    * 組織
    * 年齡為 [Quantity] 底下的子類型
    * Date 做為 DateTime 底下的子類型
    * 電子郵件 
    * 電話號碼（僅限美國）
    * URL
    * IP 位址

> [!div class="nextstepaction"]
> [深入瞭解命名實體辨識 v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2019 年 10 月

#### <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

* 識別個人資訊實體類型的[新端點](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii)（僅限英文版）

* [實體](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)辨識和[實體連結](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)的個別端點。

* [模型版本](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)`2019-10-01`，其中包括：
    * 已展開文字中找到的實體偵測和分類。 
    * 識別下列新的實體類型：
        * 電話號碼
        * IP 位址

實體連結支援英文和西班牙文。 NER 語言支援會因實體類型而異。

#### <a name="sentiment-analysis-v3-public-preview"></a>情感分析 V3 公開預覽

* 用於分析情感的[新端點](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)。
* [模型版本](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)`2019-10-01`，其中包括：

    * 應用程式開發介面文字分類和計分的精確度和詳細資料大幅改進。
    * 自動標記文字中的不同情緒。
    * 在檔和句子層級上情感分析和輸出。 

它支援英文（`en`）、日文（`ja`）、簡體中文（`zh-Hans`）、繁體中文（`zh-Hant`）、法文（`fr`）、義大利文（`it`）、西班牙文（`es`）、荷蘭文（`nl`）、葡萄牙文（`pt`）和德文（`de`），並可在下欄區域中取得： `Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`和 `Southeast Asia`。`South Central US``UK South``West Europe``West US 2` 

> [!div class="nextstepaction"]
> [深入瞭解情感分析 v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>後續步驟

* [什麼是文字分析 API？](overview.md)  
* [使用者案例範例](text-analytics-user-scenarios.md)
* [情感分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [語言偵測](how-tos/text-analytics-how-to-language-detection.md)
* [實體辨識](how-tos/text-analytics-how-to-entity-linking.md)
* [關鍵片語擷取](how-tos/text-analytics-how-to-keyword-extraction.md)
