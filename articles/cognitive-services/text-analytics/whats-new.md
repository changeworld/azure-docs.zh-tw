---
title: 文本分析 API 中的新增功能
titleSuffix: Text Analytics - Azure Cognitive Services
description: 本文提供有關 Azure 認知服務文本分析的新版本和功能的資訊。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188810"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文字分析 API 有哪些新功能？

文本分析 API 會持續更新。 為了瞭解最新動向，本文為您提供了有關新版本和功能的資訊。

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>對文本分析 API v3 公共預覽的 SDK 支援

作為[統一 Azure SDK 版本的](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)一部分，文本分析 API v3 SDK 現在可作為以下程式設計語言的公共預覽版提供：
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [JAVA](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [瞭解有關文本分析 API v3 SDK 的更多資訊](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>命名實體識別 v3 公共預覽

隨著我們擴展對文本中常規和個人資訊實體的檢測，命名實體識別 （NER） v3 公共預覽服務中現在提供了其他實體類型。 此更新引入了[模型版本](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)`2020-02-01`，其中包括：

* 識別以下一般實體類型（僅限英語）：
    * 人員類型
    * Products
    * 事件
    * 位置下的地緣政治實體 （GPE） 作為子類型
    * 技能

* 識別以下個人資訊實體類型（僅限英語）：
    * 個人
    * 組織
    * 年齡作為"數量"下的子類型
    * 日期作為日期時間下的子類型
    * 電子郵件 
    * 電話號碼（僅限美國）
    * URL
    * IP 位址

> [!div class="nextstepaction"]
> [瞭解有關命名實體識別 v3 的更多](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2019 年 10 月

#### <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

* 用於識別個人資訊實體類型[的新終結點](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii)（僅限英語）

* [實體識別和](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)[實體連結](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)的單獨終結點。

* [模型版本](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)`2019-10-01`， 包括：
    * 擴展了文本中找到的實體的檢測和分類。 
    * 識別以下新實體類型：
        * 電話號碼
        * IP 位址

實體連結支援英語和西班牙文。 NER 語言支援因實體類型而異。

#### <a name="sentiment-analysis-v3-public-preview"></a>情感分析 V3 公開預覽

* 用於分析情緒[的新端點](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)。
* [模型版本](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)`2019-10-01`， 包括：

    * API 文本分類和評分的準確性和準確性有了顯著改善。
    * 文本中不同情緒的自動標記。
    * 在文檔和句子級別上進行情緒分析和輸出。 

It supports English (`en`), Japanese (`ja`), Chinese Simplified (`zh-Hans`), Chinese Traditional (`zh-Hant`), French (`fr`), Italian (`it`), Spanish (`es`), Dutch (`nl`), Portuguese (`pt`), and German (`de`), and is available in the following regions: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`, and `West US 2`. 

> [!div class="nextstepaction"]
> [瞭解有關情緒分析 v3 的更多](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>後續步驟

* [什麼是文字分析 API？](overview.md)  
* [使用者案例範例](text-analytics-user-scenarios.md)
* [情感分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [語言偵測](how-tos/text-analytics-how-to-language-detection.md)
* [實體識別](how-tos/text-analytics-how-to-entity-linking.md)
* [關鍵片語擷取](how-tos/text-analytics-how-to-keyword-extraction.md)
