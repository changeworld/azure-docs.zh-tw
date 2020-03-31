---
title: AI 擴充的文件連結
titleSuffix: Azure Cognitive Search
description: 與 Azure 認知搜尋中的 AI 擴充工作負載相關的文章、教學課程、範例和部落格文章的標註清單。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616182"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Azure 認知搜尋中的 AI 擴充適用的文件資源

AI 擴充是 Azure 認知搜尋索引的一項功能，可在非文字來源和無差異文字中尋找潛在資訊，並將其轉換成 Azure 認知搜尋中可全文檢索搜尋的內容。

下列文章是 AI 擴充的完整文件。

## <a name="getting-started"></a>開始使用
+ [Azure 認知搜尋中的 AI 簡介](cognitive-search-concept-intro.md)
+ [快速入門：在 Azure 門戶中創建認知技能集](cognitive-search-quickstart-blob.md)
+ [教程：使用 AI 豐富索引](cognitive-search-tutorial-blob.md)
+ [示例：為 AI 擴充創建自訂技能](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>做法指引
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [如何參考技能集中的註釋](cognitive-search-concept-annotations-syntax.md)
+ [如何將欄位對應到索引](cognitive-search-output-field-mapping.md)
+ [如何處理影像並從影像擷取資訊](cognitive-search-concept-image-scenarios.md)
+ [如何重建 Azure 認知搜尋索引](search-howto-reindex.md)
+ [如何定義自訂技能介面](cognitive-search-custom-skill-interface.md)
+ [故障排除提示](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>參考資料

+ [內建技能](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ 自訂技能
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [已取代的技能](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [建立技能集 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [建立索引子 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>另請參閱

+ [Azure 認知搜尋 REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Azure 認知搜尋中的索引子](search-indexer-overview.md)
+ [什麼是 Azue 認知搜尋？](search-what-is-azure-search.md)
