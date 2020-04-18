---
title: 索引期間的內建文字和影像處理
titleSuffix: Azure Cognitive Search
description: 數據提取、自然語言、圖像處理認知技能為 Azure 認知搜索管道中的原始內容添加語義和結構。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b1cf1750924ab8ea8afff6ac788683565433866b
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618026"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>索引期間文字和影像處理的內建認知技能(Azure 認知搜尋)

在本文中,您將瞭解 Azure 認知搜索提供的認知技能,這些技能可以包含在提取內容和結構的技能集中。 *認知技能*是以某種方式轉換內容的模組或操作。 它通常是一種擷取資料或推斷結構的元件，因此能提高我們對輸入資料的了解。 輸出幾乎都是以文字為基礎的。 *技能集*是定義擴充管線的技能集合。 

> [!NOTE]
> 隨著使用處理頻率、新增更多文件或新增更多 AI 演算法來擴大範圍,您需要[附加計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。
>
> [增量擴充(預覽)](cognitive-search-incremental-indexing-conceptual.md)功能允許您提供緩存,使索引器能夠更高效地運行將來修改技能集時所需的認知技能,從而節省時間和金錢。


## <a name="built-in-skills"></a>內建技能

有數種技能所取用或產生的內容都很具彈性。 一般而言，大部分的技能乃是依據預先定型的模型，這表示您無法使用自己的定型資料來定型模型。 下表列舉並說明 Microsoft 所提供的技能。 

| 技能 | 描述 |
|-------|-------------|
|[微軟.技能.文本.自定義實體查找技能](cognitive-search-skill-custom-entity-lookup.md)| 從自定義的、使用者定義的單詞和短語清單中查找文本。|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | 這項技能使用預先定型的模型，根據字詞位置、語言規則、與其他字詞的鄰近程度，以及字詞在來源資料中是否尋常來偵測重要詞彙。 |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | 這項技能使用預先定型的模型，偵測所使用的語言 (每個文件一個語言識別碼)。 當相同的文字區段內使用多種語言時，輸出是主要使用語言的 LCID。|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | 將一組欄位中的文字合併成單一欄位。  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | 這項技能會使用預先定型的模型，為一組固定的類別 (人員、位置、組織、電子郵件、URL、日期時間欄位) 建立實體。 |
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | 此技能使用預先訓練的模型從給定文本中提取個人身份資訊。 該技能還提供了在文本中屏蔽檢測到的 PII 實體的各種選項。  |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | 這項技能使用預先定型的模型，為每一筆記錄評定正分或負分的人氣。 分數介於 0 到 1 之間。 當無法偵測到人氣時的 Null 案例，以及對於視為中性的文字，兩者都會發生中性的分數。  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | 將文字分成多個頁面，讓您能夠以遞增方式來擴充或增加內容。 |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | 此技能使用預先訓練的模型將輸入文本轉換為各種語言,以便規範化或本地化用例。 |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | 這項技能使用影像偵測演算法，以識別影像的內容並產生文字描述。 |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 光學字元辨識。 |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | 允許篩選、分配預設值以及基於條件合併數據。|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | 從濃縮管道中的檔中提取內容。 |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 將輸出對應到複雜類型 (多部分的資料類型，可用於全名、多行地址，或姓氏與個人識別碼的組合)。 |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | 透過 HTTP 呼叫到自訂 Web API 中,允許擴充導管 |


有關創建[自訂技能](cognitive-search-custom-skill-web-api.md)的指導,請參閱[如何定義自訂介面](cognitive-search-custom-skill-interface.md)和[範例:為 AI 充實創建自訂技能](cognitive-search-create-custom-skill-example.md)。

## <a name="see-also"></a>另請參閱

+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [自訂技能介面定義](cognitive-search-custom-skill-interface.md)
+ [教學:使用 AI 豐富索引](cognitive-search-tutorial-blob.md)
