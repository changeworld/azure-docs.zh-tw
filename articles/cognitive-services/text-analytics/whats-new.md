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
ms.date: 10/16/2020
ms.author: aahi
ms.openlocfilehash: f0204f5b923cf3363d85be9642d2866436df155a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369455"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文字分析 API 有哪些新功能？

文字分析 API 會持續更新。 為了隨時掌握最新的開發，本文提供新版本和功能的相關資訊。

## <a name="october-2020"></a>2020 年 10 月

* 情感分析 v3. x 的印度文支援（從 `2020-04-01` 模型版本開始）。 
* `2020-09-01`V3/languages 端點的模型版本，可增加語言偵測和精確度的改進。
* 推出印度中部和阿拉伯聯合大公國北部的 v3。

## <a name="september-2020"></a>2020 年 9 月

### <a name="general-api-updates"></a>一般 API 更新

* 發行文字分析3.1 公開預覽的新 URL，以支援下列命名實體辨識 v3 端點的更新： 
    * `/pii` 端點現在 `redactedText` 會在回應 JSON 中包含新的屬性，其中在輸入文字中偵測到的 PII 實體會由 `*` 這些實體的每個字元取代。
    * `/linking` 端點現在會 `bingID` 在已連結實體的回應 JSON 中包含屬性。
* 下列文字分析預覽版 API 端點已于2020年9月4日淘汰：
    * 2.1 版-預覽
    * v3.0-preview
    * v3.0-preview。1
    
> [!div class="nextstepaction"]
> [深入瞭解文字分析 API 3.1-Preview。2](quickstarts/text-analytics-sdk.md)

### <a name="text-analytics-for-health-container-updates"></a>健全狀況容器更新的文字分析

下列更新僅適用于九月版文字分析 for health 容器的特定版本。
* 新的容器映射具有具有 `1.1.013530001-amd64-preview` 新模型版本的標記，已 `2020-09-03` 發行至 containerpreview 存放庫。 
* 此模型版本提供實體辨識、縮寫偵測和延遲增強功能的改進。

> [!div class="nextstepaction"]
> [深入瞭解文字分析的健全狀況](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>2020 年 8 月

### <a name="general-api-updates"></a>一般 API 更新

* V3 的模型版本 `2020-07-01` `/keyphrases` `/pii` 和 `/languages` 端點，其會新增：
    * 命名實體辨識的其他政府和國家特定 [實體類別](named-entity-types.md?tabs=personal) 。
    * 情感分析 v3 支援挪威文和土耳其文。
* 針對超過已發佈 [資料限制](concepts/data-limits.md)的 v3 API 要求，現在會傳回 HTTP 400 錯誤。 
* 傳回位移的端點現在支援選擇性 `stringIndexType` 參數，會將傳回的 `offset` 和值調整 `length` 為符合支援的 [字串索引配置](concepts/text-offsets.md)。

### <a name="text-analytics-for-health-container-updates"></a>健全狀況容器更新的文字分析

下列更新僅適用于2004年8月發行的健康情況容器文字分析。

* 新模型-健全狀況的文字分析版本： `2020-07-24`
* 用於傳送健康情況要求文字分析的新 URL： `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (請注意，將需要瀏覽器快取，才能使用此新容器映射中包含的示範 web 應用程式) 

JSON 回應中的下列屬性已變更：

* `type` 已重新命名為 `category` 
* `score` 已重新命名為 `confidenceScore`
* `category`JSON 輸出欄位中的實體現在是 pascal 大小寫。 下列實體已重新命名：
    * `EXAMINATION_RELATION` 已經重新命名為 `RelationalOperator`。
    * `EXAMINATION_UNIT` 已經重新命名為 `MeasurementUnit`。
    * `EXAMINATION_VALUE` 已經重新命名為 `MeasurementValue`。
    * `ROUTE_OR_MODE` 已重新命名 `MedicationRoute` 。
    * 關聯式實體 `ROUTE_OR_MODE_OF_MEDICATION` 已重新命名為 `RouteOfMedication` 。

已新增下列實體：

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* 關聯性解壓縮
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [深入瞭解 health 容器的文字分析](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>2020 年 7 月 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>健康情況容器的文字分析-公用網關預覽

Health 容器的文字分析現已進入公開閘道預覽，可讓您從臨床檔中的非結構化英文文字中解壓縮資訊，例如：患者的進氣窗體、醫生的記事、研究論文和釋出摘要。 目前，您不需要支付健康情況容器使用量文字分析的費用。

容器提供下列功能：

* 具名實體辨識
* 關聯性解壓縮
* 實體連結
* 否定

## <a name="may-2020"></a>2020 年 5 月

### <a name="text-analytics-api-v3-general-availability"></a>文字分析 API v3 正式推出

文字分析 API v3 現在已正式運作，並提供下列更新：

* 模型版本 `2020-04-01`
* 每項功能的新[資料限制](concepts/data-limits.md)
* [情感分析 (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)的更新[語言支援](language-support.md)
* 實體連結的個別端點 
* 命名實體辨識中的新「位址」實體類別 [ (NER) v3](how-tos/text-analytics-how-to-entity-linking.md)。
* NER v3 中的新子類別：
   * 位置-地理
   * 位置-結構化
   * 組織股票的 Exchange
   * 組織-醫療
   * 組織-體育
   * 事件-文化
   * 事件-自然
   * 活動-體育

已新增 JSON 回應中的下列屬性：
   * `SentenceText` 在情感分析
   * `Warnings` 針對每份檔 

JSON 回應中的下列屬性名稱已變更，適用于：

* `score` 已重新命名為 `confidenceScore`
    * `confidenceScore` 有兩個小數點的精確度。 
* `type` 已重新命名為 `category`
* `subtype` 已重新命名為 `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [深入瞭解文字分析 API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>文字分析 API 3.1 版公開預覽
   * 新的情感分析功能- [意見挖掘](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * 針對受保護的健康情況資訊，新的 [個人 (`PII`) 網域篩選](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) (`PHI`) 。

> [!div class="nextstepaction"]
> [深入瞭解文字分析 API 3.1 版 Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>文字分析 API v3 公開預覽的 SDK 支援

作為 [整合 AZURE SDK 版本](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)的一部分，文字分析 API v3 SDK 現已提供下列程式設計語言的公開預覽版本：
   * [C#](./quickstarts/text-analytics-sdk.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/text-analytics-sdk.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/text-analytics-sdk.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/text-analytics-sdk.md?pivots=programming-language-java&tabs=version-3)
   
   > [!div class="nextstepaction"]
> [深入瞭解文字分析 API v3 SDK](./quickstarts/text-analytics-sdk.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>命名實體辨識 v3 公開預覽

當我們展開在文字中找到的一般和個人資訊實體的偵測時，可在命名實體辨識中使用其他實體類型 (NER) v3 公開預覽服務。 此更新引進 [模型版本](concepts/model-versioning.md) `2020-02-01` ，其中包括：

* 下列一般實體類型的辨識僅 (英文) ：
    * PersonType
    * 產品
    * 事件
    * 地緣政治 Entity (GPE) 作為位置下的子類型
    * 技能

* 僅) 下列個人資訊實體類型的識別 (英文：
    * 個人
    * 組織
    * 作為 [數量] 下的子類型的年齡
    * 日期為 DateTime 下的子類型
    * 電子郵件 
    * 電話號碼僅 (US) 
    * URL
    * IP 位址

> [!div class="nextstepaction"]
> [深入瞭解命名實體辨識 v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2019 年 10 月

#### <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

* 用來辨識個人資訊實體類型的 [新端點](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) (英文版) 

* 分隔 [實體](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) 辨識和 [實體連結](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)的端點。

* [模型版本](concepts/model-versioning.md) `2019-10-01` ，其中包括：
    * 展開在文字中找到的實體偵測和分類。 
    * 識別下列新的實體類型：
        * 電話號碼
        * IP 位址

實體連結支援英文和西班牙文。 NER 語言支援會因實體類型而異。

#### <a name="sentiment-analysis-v3-public-preview"></a>情感分析 V3 公開預覽

* 用來分析情感的 [新端點](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) 。
* [模型版本](concepts/model-versioning.md) `2019-10-01` ，其中包括：

    * API 文字分類和評分的精確度和詳細資料大幅改進。
    * 文字中不同情緒的自動標記。
    * 情感檔和句子層級的分析和輸出。 

它支援英文 (`en`) 、日文 (`ja`) 、中文簡體的 () `zh-Hans` 、繁體中文 (`zh-Hant`) 、法文 (`fr`) 、義大利文 (`it`) 、西班牙文 (`es`) 、荷蘭文 (`nl`) 、葡萄牙文 (`pt`) 和德文 () `de` ，而且可于下欄區域使用： `Australia East` 、 `Central Canada` 、 `Central US` 、、 `East Asia` `East US` 、 `East US 2` 、 `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` 、、、、和。 

> [!div class="nextstepaction"]
> [深入瞭解情感分析 v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>後續步驟

* [什麼是文字分析 API？](overview.md)  
* [使用者案例範例](text-analytics-user-scenarios.md)
* [情感分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [語言偵測](how-tos/text-analytics-how-to-language-detection.md)
* [實體辨識](how-tos/text-analytics-how-to-entity-linking.md)
* [關鍵片語擷取](how-tos/text-analytics-how-to-keyword-extraction.md)