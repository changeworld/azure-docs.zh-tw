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
ms.date: 06/03/2020
ms.author: aahi
ms.openlocfilehash: 7ab886b65e665bdd38e2461e49fa051213602f0e
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2020
ms.locfileid: "84462784"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文字分析 API 有哪些新功能？

文字分析 API 會持續更新。 為了讓您隨時掌握最新的開發，這篇文章為您提供新版本和功能的相關資訊。

## <a name="may-2020"></a>2020 年 5 月

### <a name="text-analytics-api-v3-general-availability"></a>文字分析 API v3 正式推出

文字分析 API v3 現已公開提供下列更新：

* 模型版本`2020-04-01`
* 每項功能的新[資料限制](concepts/data-limits.md)
* [情感分析（SA） v3](how-tos/text-analytics-how-to-sentiment-analysis.md)的更新[語言支援](language-support.md)
* 實體連結的個別端點 
* [命名實體辨識（NER） v3](how-tos/text-analytics-how-to-entity-linking.md)中的新「位址」實體類別。
* NER v3 中的新子類別：
   * 位置-地理
   * 位置-結構化
   * 組織-股票交換
   * 組織-醫療
   * 組織-體育
   * 事件-文化
   * 事件-自然
   * 活動-運動

已新增 JSON 回應中的下列屬性：
   * `SentenceText`在情感分析
   * `Warnings`針對每份檔 

JSON 回應中下列屬性的名稱已變更（如果適用）：

* `score` 已重新命名為 `confidenceScore`
    * `confidenceScore`有兩個小數位數。 
* `type` 已重新命名為 `category`
* `subtype` 已重新命名為 `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [深入瞭解文字分析 API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>文字分析 API 3.1 版公開預覽
   * 新的情感分析功能-[意見挖掘](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * 適用于受保護健全狀況資訊的新[個人（ `PII` ）網域篩選](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)（ `PHI` ）。
   * 新的 Personal （ `PII` ）類別：
      * 疾病的國際分類（ICD-9-CM）
      * 疾病的國際分類（ICD-10-CM）

> [!div class="nextstepaction"]
> [深入瞭解文字分析 API 3.1 版預覽](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>文字分析 API v3 公開預覽版本的 SDK 支援

作為[整合 AZURE SDK 版本](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)的一部分，文字分析 API v3 SDK 現在可作為下列程式設計語言的公開預覽：
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [深入瞭解文字分析 API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>命名實體辨識 v3 公開預覽

現在已可在「命名實體辨識」（NER） v3 公開預覽服務中使用其他實體類型，因為我們會展開文字中找到的一般和個人資訊實體的偵測。 此更新會引進[模型版本](concepts/model-versioning.md) `2020-02-01` ，其中包括：

* 識別下列一般實體類型（僅限英文）：
    * PersonType
    * 產品
    * 事件
    * 地緣政治實體（GPE）作為 [位置] 底下的子類型
    * 技能

* 識別下列個人資訊實體類型（僅限英文）：
    * 個人
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

* [模型版本](concepts/model-versioning.md) `2019-10-01` ，其中包括：
    * 已展開文字中找到的實體偵測和分類。 
    * 識別下列新的實體類型：
        * 電話號碼
        * IP 位址

實體連結支援英文和西班牙文。 NER 語言支援會因實體類型而異。

#### <a name="sentiment-analysis-v3-public-preview"></a>情感分析 V3 公開預覽

* 用於分析情感的[新端點](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)。
* [模型版本](concepts/model-versioning.md) `2019-10-01` ，其中包括：

    * 應用程式開發介面文字分類和計分的精確度和詳細資料大幅改進。
    * 自動標記文字中的不同情緒。
    * 在檔和句子層級上情感分析和輸出。 

它支援英文（ `en` ）、日文（ `ja` ）、簡體中文（ `zh-Hans` ）、繁體中文（ `zh-Hant` ）、法文（ `fr` ）、義大利文（ `it` ）、西班牙文（ `es` ）、荷蘭文（ `nl` ）、葡萄牙文（ `pt` ）和德文（ `de` ），而且可在下欄區域使用： `Australia East` 、 `Central Canada` 、 `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` 、、、、、、、、和。 

> [!div class="nextstepaction"]
> [深入瞭解情感分析 v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>後續步驟

* [文字分析 API 是什麼？](overview.md)  
* [使用者案例範例](text-analytics-user-scenarios.md)
* [情感分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [語言偵測](how-tos/text-analytics-how-to-language-detection.md)
* [實體辨識](how-tos/text-analytics-how-to-entity-linking.md)
* [關鍵片語擷取](how-tos/text-analytics-how-to-keyword-extraction.md)
