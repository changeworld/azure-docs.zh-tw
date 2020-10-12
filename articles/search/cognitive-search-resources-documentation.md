---
title: AI 擴充的文件連結
titleSuffix: Azure Cognitive Search
description: 與 Azure 認知搜尋中的 AI 擴充工作負載相關的文章、教學課程、範例和部落格文章的標註清單。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935342"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Azure 認知搜尋中的 AI 擴充適用的文件資源

AI 擴充是以索引子為基礎的索引附加元件，可在非文字來源和無差異文字中尋找潛在資訊，並將其轉換成 Azure 認知搜尋中的全文檢索搜尋內容。 

針對內建處理，認知服務中預先定型的 AI 模型會在內部呼叫，以執行分析。 您也可以使用 Azure Machine Learning、Azure Functions 或其他方法來整合自訂模型。

以下是 AI 擴充檔的匯總清單。

## <a name="concepts"></a>概念

+ [AI 擴充](cognitive-search-concept-intro.md)
+ [技能集](cognitive-search-working-with-skillsets.md)
+ [偵錯工作階段](cognitive-search-debug-session.md)
+ [知識存放區](knowledge-store-concept-intro.md)
+ [投影](knowledge-store-projection-overview.md)
+ [遞增擴充 (重複使用快取的擴充檔) ](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>實際操作逐步解說

+ [快速入門：在 Azure 入口網站中建立認知技能集](cognitive-search-quickstart-blob.md)
+ [教學課程：使用 AI 擴充編制索引](cognitive-search-tutorial-blob.md)
+ [教學課程：使用 Debug 會話診斷、修復和認可您的技能集變更](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>知識存放區

+ [快速入門：在 Azure 入口網站中建立知識存放區](knowledge-store-create-portal.md)
+ [使用 REST 和 Postman 建立知識存放區](knowledge-store-create-rest.md)
+ [使用儲存體總管來檢視知識存放區](knowledge-store-view-storage-explorer.md)
+ [使用 Power BI 連線到知識存放區](knowledge-store-connect-power-bi.md)
+ [投影範例 (如何塑造和匯出擴充) ](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>自訂技能 (advanced) 

+ [如何定義自訂技能介面](cognitive-search-custom-skill-interface.md)
+ [範例：使用 Azure Functions (和 Bing 實體搜尋 Api 建立自訂技能) ](cognitive-search-create-custom-skill-example.md)
+ [範例：使用 Python 建立自訂技能](cognitive-search-custom-skill-python.md)
+ [範例：使用表單辨識器建立自訂技能](cognitive-search-custom-skill-form.md) 
+ [範例：使用 Azure Machine Learning 建立自訂技能](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>做法指引

+ [附加認知服務資源](cognitive-search-attach-cognitive-services.md)
+ [定義技能集](cognitive-search-defining-skillset.md)
+ [技能集中的參考批註](cognitive-search-concept-annotations-syntax.md)
+ [將欄位對應至索引](cognitive-search-output-field-mapping.md)
+ [從映像處理及擷取資訊](cognitive-search-concept-image-scenarios.md)
+ [設定增量擴充的快取](search-howto-incremental-index.md)
+ [如何重建 Azure 認知搜尋索引](search-howto-reindex.md)
+ [設計秘訣](cognitive-search-concept-troubleshooting.md)
+ [常見的錯誤和警告](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>技能參考

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
  + [AmlSkill。](cognitive-search-aml-skill.md)
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [已取代的技能](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>API

+ [REST API](/rest/api/searchservice/)
  + [建立技能集 (api 版本 = 2020-06-30) ](/rest/api/searchservice/create-skillset)
  + [建立索引子 (api 版本 = 2020-06-30) ](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>另請參閱

+ [Aure 認知搜尋 REST API](/rest/api/searchservice/)
+ [Azure 認知搜尋中的索引子](search-indexer-overview.md)
+ [什麼是 Azure 認知搜尋？](search-what-is-azure-search.md)