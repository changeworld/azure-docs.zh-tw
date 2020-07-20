---
title: 文字分析 API v2 的遷移指南
titleSuffix: Azure Cognitive Services
description: 瞭解如何將您的應用程式移至使用第3版的文字分析 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/25/2020
ms.author: aahi
ms.openlocfilehash: 12c09ad8e1db3914263fcc864c9c2d09069d63a6
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2020
ms.locfileid: "85412578"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>遷移至2.x 版的文字分析 API

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

如果您使用2.1 版的文字分析 API，本文將協助您將應用程式升級為使用2.x 版。 版本3.0 已正式推出，並引進了新功能，例如擴充的[命名實體識別（NER）](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)和[模型版本](concepts/model-versioning.md)設定。 預覽版本的3.1 （3.1-preview. x）也可供使用，這會新增[意見挖掘](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)等功能。 在 v2 中使用的模型將不會接收未來的更新。 

#### <a name="sentiment-analysis"></a>[情感分析](#tab/sentiment-analysis)

## <a name="feature-changes"></a>功能變更 

2.1 版中的情感分析會針對每個傳送至 API 的檔傳回0到1之間的情感分數，分數接近1則表示更正面的情感。 第3版會改為傳回句子和檔整體的情感標籤（例如 "正數" 或 "負值"），以及其相關聯的信賴分數。 

## <a name="steps-to-migrate"></a>遷移步驟

### <a name="rest-api"></a>REST API

如果您的應用程式使用 REST API，請將其要求端點更新為 v3 端點，以進行情感分析。 例如： `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` 。 您也必須更新應用程式，以使用[JSON 回應](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results)中所傳回的情感標籤。 

### <a name="client-libraries"></a>用戶端程式庫

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="ner-and-entity-linking"></a>[NER 和實體連結](#tab/named-entity-recognition)

## <a name="feature-changes"></a>功能變更

> [!NOTE] 
> 目前， [v3 實體類別](named-entity-types.md)只會傳回英文和西班牙文的文字。 API 會傳回其他語言中要求的2.1 版結果，前提是版本2.1 中支援它們。

在2.1 版中，文字分析 API 會使用一個端點來命名實體識別（NER）和實體連結。 第3版提供擴充的命名實體偵測，並針對 NER 和實體連結要求使用不同的端點。 從 3.1-preview. 1 開始，NER 可以額外偵測個人 `pii` 和健康狀態 `phi` 資訊。 

## <a name="steps-to-migrate"></a>遷移步驟

### <a name="rest-api"></a>REST API

如果您的應用程式使用 REST API，請將其要求端點更新為 NER 和（或）實體連結的 v3 端點。

實體連結
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

您也需要更新應用程式，以使用[JSON 回應](how-tos/text-analytics-how-to-entity-linking.md#view-results)中所傳回的[實體類別](named-entity-types.md)。

### <a name="client-libraries"></a>用戶端程式庫

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="language-detection"></a>[語言偵測](#tab/language-detection)

## <a name="feature-changes"></a>功能變更 

在端點版本外的 v3 中，語言偵測功能未變更，但 JSON 回應將包含， `ConfidenceScore` 而不是 `score` 。 V3 也只會傳回輸出中的單一語言。 

## <a name="steps-to-migrate"></a>遷移步驟

### <a name="rest-api"></a>REST API

如果您的應用程式使用 REST API，請將其要求端點更新為適用于語言偵測的 v3 端點。 例如： `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` 。 您也必須將應用程式更新為使用， `ConfidenceScore` 而不是 `score` [JSON 回應](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results)中的。 

### <a name="client-libraries"></a>用戶端程式庫

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="key-phrase-extraction"></a>[關鍵片語擷取](#tab/key-phrase-extraction)

## <a name="feature-changes"></a>功能變更 

在端點版本外的 v3 中，關鍵字組解壓縮功能並未變更。

## <a name="steps-to-migrate"></a>遷移步驟

### <a name="rest-api"></a>REST API

如果您的應用程式使用 REST API，請將其要求端點更新為用於主要片語解壓縮的 v3 端點。 例如：`https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

### <a name="client-libraries"></a>用戶端程式庫

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---


## <a name="see-also"></a>另請參閱

* [文字分析 API v2 參考](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/)
* [什麼是文字分析 API](overview.md)
* [語言支援](language-support.md)
* [模型版本設定](concepts/model-versioning.md)