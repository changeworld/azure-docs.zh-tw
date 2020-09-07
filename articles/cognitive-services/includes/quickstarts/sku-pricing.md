---
title: 認知服務 SKU 和定價
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: cb066ecc1dc11985c0ccb0fa687a15d6b038b9fa
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321543"
---
請參閱下方的 SKU 和定價資訊清單。 

#### <a name="multi-service"></a>多服務

| 服務                    | 種類                      |
|----------------------------|---------------------------|
| 多種服務。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/)頁面。            | `CognitiveServices`     |


#### <a name="vision"></a>視覺

| 服務                    | 種類                      |
|----------------------------|---------------------------|
| 電腦視覺            | `ComputerVision`          |
| 自訂視覺 - 預測 | `CustomVision.Prediction` |
| 自訂視覺 - 訓練   | `CustomVision.Training`   |
| 臉部                       | `Face`                    |
| 表單辨識器            | `FormRecognizer`          |
| 筆跡辨識器             | `InkRecognizer`           |

#### <a name="search"></a>搜尋

| 服務            | 種類                  |
|--------------------|-----------------------|
| Bing 自動建議   | `Bing.Autosuggest.v7` |
| Bing 自訂搜尋 | `Bing.CustomSearch`   |
| Bing 實體搜尋 | `Bing.EntitySearch`   |
| Bing 搜尋        | `Bing.Search.v7`      |
| Bing 拼字檢查   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>語音

| 服務            | 種類                 |
|--------------------|----------------------|
| 語音服務    | `SpeechServices`     |
| 語音辨識 | `SpeakerRecognition` |

#### <a name="language"></a>Language

| 服務            | 種類                |
|--------------------|---------------------|
| Form Understanding | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 文字分析     | `TextAnalytics`     |
| 文字翻譯   | `TextTranslation`   |

#### <a name="decision"></a>決策

| 服務           | 種類               |
|-------------------|--------------------|
| 異常偵測器  | `AnomalyDetector`  |
| 內容仲裁 | `ContentModerator` |
| 個人化工具      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>定價層和計費

定價層 (以及您支付的金額) 是根據您使用驗證資訊傳送的交易數目而定。 每個定價層都會指定：
* 每秒允許的交易數目上限 (TPS)。
* 在該定價層中啟用的服務功能。
* 預先定義的交易數目成本。 超過此數目會產生額外的費用，如您服務的[定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)中所述。

> [!NOTE]
> 許多認知服務都具有免費層，您可以用來試用服務。 若要使用免費層，請使用 `F0` 作為資源的 SKU。