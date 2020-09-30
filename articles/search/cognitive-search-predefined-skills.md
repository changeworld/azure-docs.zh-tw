---
title: 在編制索引期間內建的文字和影像處理
titleSuffix: Azure Cognitive Search
description: 資料提取、自然語言、影像處理認知技能會將語義和結構新增至 Azure 認知搜尋管線中的原始內容。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 417cc13c4a201a0dd86a846c347cb395b8d81c43
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535520"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>內建的認知技能，可在編制索引 (Azure 認知搜尋) 期間進行文字和影像處理

在本文中，您將瞭解 Azure 認知搜尋所提供的認知技能，可包含在技能集中以解壓縮內容和結構。 *認知技能*是以某種方式轉換內容的模組或操作。 它通常是一種擷取資料或推斷結構的元件，因此能提高我們對輸入資料的了解。 輸出幾乎都是以文字為基礎的。 *技能集*是定義擴充管線的技能集合。 

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://azure.microsoft.com/pricing/details/search/)。
>
> [增量擴充 (preview) ](cognitive-search-incremental-indexing-conceptual.md)功能可讓您提供快取，讓索引子更有效率地執行您在未來修改技能集時所需的認知技能，以節省您的時間和金錢。


## <a name="built-in-skills"></a>內建技能

有數種技能所取用或產生的內容都很具彈性。 一般而言，大部分的技能乃是依據預先定型的模型，這表示您無法使用自己的定型資料來定型模型。 下表列舉並說明 Microsoft 所提供的技能。 

| 技能 | 說明 |
|-------|-------------|
|[CustomEntityLookupSkill。](cognitive-search-skill-custom-entity-lookup.md)| 從自訂、使用者定義的單字和片語清單中尋找文字。|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | 這項技能使用預先定型的模型，根據字詞位置、語言規則、與其他字詞的鄰近程度，以及字詞在來源資料中是否尋常來偵測重要詞彙。 |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | 這項技能使用預先定型的模型，偵測所使用的語言 (每個文件一個語言識別碼)。 當相同的文字區段內使用多種語言時，輸出是主要使用語言的 LCID。|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | 將一組欄位中的文字合併成單一欄位。  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | 這項技能會使用預先定型的模型，為一組固定的類別 (人員、位置、組織、電子郵件、URL、日期時間欄位) 建立實體。 |
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | 這種技能會使用預先定型模型，從指定文字中將個人資訊解壓縮。 技能也會提供各種選項，以遮罩文字中偵測到的個人資訊實體。  |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | 這項技能使用預先定型的模型，為每一筆記錄評定正分或負分的人氣。 分數介於 0 到 1 之間。 當無法偵測到人氣時的 Null 案例，以及對於視為中性的文字，兩者都會發生中性的分數。  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | 將文字分成多個頁面，讓您能夠以遞增方式來擴充或增加內容。 |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | 這項技能會使用預先定型模型，將輸入文字轉譯成各種不同的語言，以供正規化或當地語系化使用案例使用。 |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | 這項技能使用影像偵測演算法，以識別影像的內容並產生文字描述。 |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 光學字元辨識。 |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | 允許篩選、指派預設值，以及根據條件來合併資料。|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | 從擴充管線內的檔案解壓縮內容。 |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 將輸出對應到複雜類型 (多部分的資料類型，可用於全名、多行地址，或姓氏與個人識別碼的組合)。 |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | 藉由對自訂 Web API 進行 HTTP 呼叫，允許 AI 擴充管線的擴充性 |
| [AmlSkill。](cognitive-search-aml-skill.md) | 允許 AI 擴充管線與 Azure Machine Learning 模型的擴充性 |


如需建立 [自訂技能](cognitive-search-custom-skill-web-api.md)的指引，請參閱 [如何定義自訂介面](cognitive-search-custom-skill-interface.md) 和 [範例：建立 AI 擴充的自訂技能](cognitive-search-create-custom-skill-example.md)。

## <a name="see-also"></a>另請參閱

+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [自訂技能介面定義](cognitive-search-custom-skill-interface.md)
+ [教學課程：使用 AI 擴充編制索引](cognitive-search-tutorial-blob.md)
