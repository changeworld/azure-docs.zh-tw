---
title: 使用 AI 瞭解 Blob 存儲資料
titleSuffix: Azure Cognitive Search
description: 使用 Azure 認知搜索中的 AI 擴充管道向 Azure blob 添加語義、自然語言處理和圖像分析。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496438"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>使用 AI 瞭解 Blob 存儲資料

Azure Blob 存儲中的資料通常是各種非結構化內容，如圖像、長文本、PDF 和 Office 文檔。 通過使用 Azure 認知搜索中的 AI 功能，您可以通過多種方式從 Blob 中瞭解和提取有價值的資訊。 將 AI 應用於 blob 內容的示例包括：

+ 使用光學字元辨識 （OCR） 從圖像中提取文本
+ 從照片中生成場景描述或標記
+ 檢測語言並將文本翻譯成不同的語言
+ 使用命名實體識別 （NER） 處理文本，以查找對人員、日期、地點或組織的引用 

雖然您可能只需要這些 AI 功能之一，但通常將多個功能合併到同一管道中（例如，從掃描的圖像中提取文本，然後查找其中引用的所有日期和地點）。 

AI 擴充將創建存儲在欄位中的新資訊（捕獲為文本）。 擴充後，您可以通過全文檢索搜尋從搜索索引訪問此資訊，或者將豐富的文檔發送回 Azure 存儲，以為新的應用程式體驗提供支援，其中包括探索發現或分析方案的資料。 

在本文中，我們通過寬鏡頭查看 AI 擴充，以便您可以快速掌握整個過程，從在 blob 中轉換原始資料到搜索索引或知識存儲中的可查詢資訊。

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>使用 AI"豐富"blob 資料意味著什麼

*AI 擴充*是 Azure 認知搜索索引體系結構的一部分，該體系結構集成了來自 Microsoft 或您提供的自訂 AI 的內置 AI。 它可以説明您實現端到端方案，其中您需要處理 Blob（現有 blob 和新 blob 在來或更新時），打開所有檔案格式以提取圖像和文本，使用各種 AI 功能提取所需的資訊，以及索引它們在搜索索引中快速搜尋、檢索和探索。 

輸入是在 Azure Blob 存儲中的單個容器中的 Blob。 Blob 幾乎可以是任何類型的文本或圖像資料。 

輸出始終是一個搜索索引，用於用戶端應用程式中的快速文本搜索、檢索和流覽。 此外，輸出還可以是一*個知識存儲，* 用於將富集文檔專案專案到 Azure Blob 或 Azure 表中，以便以 Power BI 或資料科學工作負荷等工具進行下游分析。

中間是管道體系結構本身。 管道基於*索引子*功能，您可以為其分配*一個技能集，該技能集*由提供 AI 的一個或多個*技能*組成。 管道的目的是生成*豐富的文檔*，這些文檔以原始內容形式輸入，但在通過管道時獲取其他結構、上下文和資訊。 在索引過程中使用豐富的文檔，以創建用於全文檢索搜尋或探索和分析的反轉索引和其他結構。

## <a name="start-with-services"></a>從服務開始

您需要 Azure 認知搜索和 Azure Blob 存儲。 在 Blob 存儲中，您需要一個提供源內容的容器。

您可以直接在存儲帳戶門戶頁中開始。 在左側導航頁中，按一下 **"** 添加**Azure 認知搜索"** 以創建新服務或選擇現有服務。 

將 Azure 認知搜索添加到存儲帳戶後，可以按照標準過程來豐富任何 Azure 資料來源中的資料。 我們建議在 Azure 認知搜索中**導入資料**嚮導，以便輕鬆初始介紹 AI 充實。 此快速入門將引導您完成以下步驟：[在門戶中創建 AI 擴充管道](cognitive-search-quickstart-blob.md)。 

在以下各節中，我們將探討更多元件和概念。

## <a name="use-a-blob-indexer"></a>使用 Blob 索引子

AI 擴充是索引管道的載入項，在 Azure 認知搜索中，這些管道構建在*索引子*之上。 索引子是資料來源感知子服務，具有用於採樣資料、讀取中繼資料資料、檢索資料和將本地格式的資料序列化到 JSON 文檔中以便後續導入的內部邏輯。 索引子通常由自身用於導入，與 AI 分開，但如果要構建 AI 擴充管道，則需要索引子和技能集來使用它。 本節重點介紹索引子;下一節將重點介紹技能集。

Azure 存儲中的 Blob 使用[Azure 認知搜索 Blob 存儲索引子](search-howto-indexing-azure-blob-storage.md)進行索引。 您可以使用**導入資料**嚮導、REST API 或 .NET SDK 調用此索引子。 在代碼中，可以通過設置類型和提供包含 Azure 存儲帳戶和 Blob 容器的連接資訊來使用此索引子。 您可以通過創建虛擬目錄（然後作為參數傳遞）或篩選檔案類型副檔名來對 Blob 進行子集。

索引子執行"文檔破解"，打開 blob 以檢查內容。 連接到資料來源後，它是管道中的第一步。 對於 Blob 資料，這是檢測 PDF、辦公室文檔、圖像和其他內容類型的位置。 使用文本提取進行文檔破解不收取任何費用。 使用圖像提取進行文檔破解按您可以在[定價頁面上](https://azure.microsoft.com/pricing/details/search/)找到的費率收費。

儘管所有文檔都將被破解，但僅當您明確提供這樣做的技能時，才會進行充實。 例如，如果管道完全由圖像分析組成，則忽略容器或文檔中的文本。

Blob 索引子附帶配置參數，如果基礎資料提供足夠的資訊，則支援更改跟蹤。 您可以在[Azure 認知搜索 Blob 存儲索引子](search-howto-indexing-azure-blob-storage.md)中瞭解有關核心功能的更多資訊。

## <a name="add-ai-components"></a>添加 AI 元件

AI 擴充是指查找模式或特徵，然後相應地執行操作的模組。 照片中的面部識別、照片的文本描述、檢測文檔中的關鍵短語以及 OCR（或識別二進位檔案中的列印或手寫文本）都是說明性示例。

在 Azure 認知搜索中，*技能*是 AI 處理的各個元件，您可以單獨使用，也可以與其他技能結合使用。 

+ 內置技能由認知服務提供支援，基於電腦視覺的圖像分析基於文本分析，自然語言處理。 有關完整清單，請參閱[內容豐富的內置技能](cognitive-search-predefined-skills.md)。

+ 自訂技能是自訂代碼，包裝在允許集成到管道中的[介面定義](cognitive-search-custom-skill-interface.md)中。 在客戶解決方案中，通常使用兩者，具有提供開源、協力廠商或第一方 AI 模組的自訂技能。

*技能集*是管道中使用的技能的集合，在文檔破解階段使內容可用後調用它。 索引子可以完全使用一個技能集，但該技能集獨立于索引子存在，以便在其他方案中重用它。

自訂技能聽起來可能很複雜，但在實施方面可能簡單明瞭。 如果現有包提供模式匹配或分類模型，則可以將從 Blob 中提取的內容傳遞給這些模型進行處理。 由於 AI 擴充基於 Azure，因此模型也應位於 Azure 上。 一些常見的託管方法包括使用[Azure 函數](cognitive-search-create-custom-skill-example.md)或[容器](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)。

認知服務支援的內置技能需要[附加的認知服務](cognitive-search-attach-cognitive-services.md)一體式訂閱金鑰，以便您訪問資源。 一體式金鑰為您提供圖像分析、語言檢測、文本翻譯和文本分析。 其他內置技能是 Azure 認知搜索的功能，無需其他服務或金鑰。 文本拆分器、拆分器和合併是協助程式技能的示例，這些技能有時在設計管道時是必需的。

如果您只使用自訂技能和內置實用程式技能，則不存在與認知服務相關的依賴項或成本。

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>在下游解決方案中消耗 AI 豐富的輸出

AI 擴充的輸出是 Azure 認知搜索上的搜索索引，或 Azure 存儲中[的知識存儲](knowledge-store-concept-intro.md)。

在 Azure 認知搜索中，搜索索引用於使用用戶端應用中的免費文本和篩選查詢進行互動式流覽。 通過 AI 創建的豐富文檔在 JSON 中設置格式，並對所有文檔在 Azure 認知搜索中編制索引的方式進行索引，利用索引子提供的所有優勢。 例如，在索引期間，blob 索引子引用配置參數和設置以利用任何欄位映射或更改檢測邏輯。 此類設置完全可用於常規索引和 AI 富集工作負載。 索引後，當內容存儲在 Azure 認知搜索上時，可以生成豐富的查詢和篩選器運算式來瞭解內容。

在 Azure 存儲中，知識存儲有兩種表現形式：blob 容器或表存儲中的表。 

+ Blob 容器捕獲富集文檔的全部內容，如果要饋送到其他進程，這非常有用。 

+ 相反，表存儲可以容納富集文檔的物理投影。 您可以創建包含或排除特定部件的富集文檔切片或圖層。 在 Power BI 中進行分析時，Azure 表存儲中的表將成為用於進一步視覺化和探索的資料來源。

管道末尾的富集文檔與其原始輸入版本不同，因為存在包含在擴充過程中提取或生成的新資訊的其他欄位。 因此，您可以使用原始內容和創建內容的組合，而不管使用哪種輸出結構。

## <a name="next-steps"></a>後續步驟

對於 Azure 存儲中的大部分資料，您可以執行更多 AI 擴充功能，包括以不同的方式組合認知服務，以及為方案沒有現有認知服務的情況編寫自訂技能。 您可以按照以下連結瞭解更多資訊。

+ [使用 Azure 門戶（Azure Blob 存儲）上載、下載和列出 Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [設置 Blob 索引子（Azure 認知搜索）](search-howto-indexing-azure-blob-storage.md) 
+ [AI 擴充概述（Azure 認知搜索）](cognitive-search-concept-intro.md) 
+ [創建技能集（Azure 認知搜索）](cognitive-search-defining-skillset.md)
+ [注釋樹中的映射節點（Azure 認知搜索）](cognitive-search-output-field-mapping.md)
