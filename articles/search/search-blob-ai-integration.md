---
title: 使用 AI 來瞭解 Blob 儲存體資料
titleSuffix: Azure Cognitive Search
description: 使用 Azure 認知搜尋中的 AI 擴充管線，在 Azure blob 中新增語義、自然語言處理和影像分析。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "73496438"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>使用 AI 來瞭解 Blob 儲存體資料

Azure Blob 儲存體中的資料通常是各種非結構化內容，例如影像、長文字、Pdf 和 Office 檔。 藉由使用 Azure 認知搜尋中的 AI 功能，您可以透過各種不同的方式來瞭解及解壓縮 blob 中的重要資訊。 將 AI 套用至 blob 內容的範例包括：

+ 使用光學字元辨識（OCR）從影像中解壓縮文字
+ 從相片產生場景描述或標記
+ 偵測語言並將文字翻譯成不同的語言
+ 使用命名實體辨識（NER）處理文字，以尋找人員、日期、地點或組織的參考 

雖然您可能只需要這些 AI 功能的其中一個，但通常會將多個整合到相同的管線（例如，從掃描的影像中解壓縮文字，然後尋找其中所參考的所有日期和位置）。 

AI 擴充會建立儲存在欄位中的新資訊，以文字形式加以捕捉。 擴充後，您可以透過全文檢索搜尋從搜尋索引存取這份資訊，或將擴充的檔傳回到 Azure 儲存體，以支援新的應用程式體驗，其中包括探索探索或分析案例的資料。 

在本文中，我們會透過寬型鏡頭來觀看 AI 擴充，讓您可以快速瞭解整個程式，從將 blob 中的原始資料轉換成搜尋索引或知識存放區中的可查詢資訊。

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>使用 AI 「充實」 blob 資料的意義

*AI 擴充*是 Azure 認知搜尋的編制索引架構的一部分，可整合來自 Microsoft 的內建 ai 或您提供的自訂 ai。 它可協助您執行端對端案例，您必須在其中處理 blob （現有的和新的 blob，或更新時）、「開啟所有檔案格式」以解壓縮影像和文字、使用各種 AI 功能來解壓縮所需的資訊，並在搜尋索引中編制它們的索引，以便快速搜尋、抓取和探索。 

輸入是您在 Azure Blob 儲存體中的 Blob (在單一容器中)。 Blob 幾乎可以是任何類型的文字或影像資料。 

輸出一律是搜尋索引，用於在用戶端應用程式中進行快速文字搜尋、抓取和探索。 此外，輸出也可以是將擴充的檔投射到 Azure blob 或 Azure 資料表的*知識存放區*，以便在 Power BI 或資料科學工作負載之類的工具中進行下游分析。

在中，是管線架構本身。 管線是以*索引子*功能為基礎，您可以在其中指派*技能集*，這是由提供 AI 的一或多項*技術*所組成。 管線的目的是要產生擴充的*檔*，以輸入為原始內容，但在透過管線移動時，會挑選額外的結構、內容和資訊。 在編制索引期間會耗用擴充的檔，以建立反向索引和其他用於全文檢索搜尋或探索和分析的結構。

## <a name="start-with-services"></a>開始使用服務

您需要 Azure 認知搜尋和 Azure Blob 儲存體。 在 Blob 儲存體中，您需要提供來源內容的容器。

您可以在您的儲存體帳戶入口網站頁面中直接開始。 在左側導覽頁面的 [Blob 服務] 底下，按一下 [新增 Azure 認知搜尋] 以建立新服務，或選取現有服務。 

將 Azure 認知搜尋新增至您的儲存體帳戶之後，您可以遵循標準程式來擴充任何 Azure 資料來源中的資料。 我們建議您在 Azure 認知搜尋中使用「匯**入資料**」 wizard，以取得 AI 擴充的初始簡介。 本快速入門會逐步引導您進行步驟：[在入口網站中建立 AI 擴充管線](cognitive-search-quickstart-blob.md)。 

在下列各節中，我們將探索更多元件和概念。

## <a name="use-a-blob-indexer"></a>使用 Blob 索引子

AI 擴充是索引管線的附加元件，而在 Azure 認知搜尋中，這些管線是建置於*索引子*之上。 「索引子」是資料來源感知的子服務，其具備內部邏輯，可用來取樣資料、讀取中繼資料、擷取資料，以及將資料從原生格式序列化為 JSON 文件以供後續匯入。 索引子通常會用於匯入，與 AI 分開，但如果您想要建立 AI 擴充管線，則需要索引子和技能集來與它搭配使用。 本節將重點放在索引子;下一節著重于技能集。

Azure 儲存體中的 Blob 是使用 [Azure 認知搜尋 Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)來編製索引。 您可以使用**匯入資料**精靈、REST API 或 .NET SDK 來叫用此索引子。 在程式碼中，您可以藉由設定類型，以及提供包含 Azure 儲存體帳戶和 Blob 容器的連線資訊，來使用此索引子。 您可以藉由建立虛擬目錄來將 Blob 子集化，然後將其當作參數傳遞，或藉由篩選檔案類型副檔名來進行。

索引子會執行「文件破解」(開啟 Blob 以檢查內容)。 連線到資料來源之後，其就是管線中的第一個步驟。 若是 blob 資料，則會偵測到 PDF、office 檔、影像和其他內容類型。 使用文字擷取的文件破解是免費的。 使用影像解壓縮的檔破解是以您可以在[定價頁面](https://azure.microsoft.com/pricing/details/search/)上找到的費率收費。

雖然所有檔都會被破解，但只有在您明確提供技能來執行此動作時，才會發生擴充。 例如，如果您的管線僅包含影像分析，則會忽略容器或檔中的文字。

Blob 索引子隨附設定參數，如果底層資料提供足夠的資訊，則支援變更追蹤。 您可以在 [Azure 認知搜尋 Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)中深入了解核心功能。

## <a name="add-ai-components"></a>新增 AI 元件

AI 擴充指的是可尋找模式或特性的模組，然後據以執行操作。 相片中的臉部辨識、相片的文字描述、偵測檔中的主要片語，以及 OCR （或辨識二進位檔案中的列印或手寫文字）都是說明範例。

在 Azure 認知搜尋中，*技能*是 AI 處理的個別元件，您可以單獨使用或結合其他技能。 

+ 內建技能的支援認知服務、以電腦視覺為基礎的影像分析，以及以文字分析為基礎的自然語言處理。 如需完整清單，請參閱[content 擴充的內建技能](cognitive-search-predefined-skills.md)。

+ 自訂技能是包裝在[介面定義](cognitive-search-custom-skill-interface.md)中的自訂程式碼，可讓您整合到管線中。 在客戶解決方案中，常見的作法是使用這兩種方式，搭配提供開放原始碼、協力廠商或第一方 AI 模組的自訂技能。

*技能集*是管線中使用的技能集合，它是在檔破解階段讓內容可供使用時叫用。 索引子只能取用一個技能集，但該技能集獨立于索引子，因此您可以在其他案例中重複使用它。

自訂技能可能非常複雜，但在執行方面可以簡單明瞭。 如果您有提供模式比對或分類模型的現有封裝，則從 blob 解壓縮的內容可以傳遞至這些模型進行處理。 由於 AI 擴充是以 Azure 為基礎，因此您的模型也應該在 Azure 上。 某些常見的裝載方法包括使用[Azure Functions](cognitive-search-create-custom-skill-example.md)或[容器](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)。

認知服務所支援的內建技能需要[附加的認知服務](cognitive-search-attach-cognitive-services.md)全功能訂用帳戶金鑰，讓您能夠存取資源。 全功能金鑰可提供影像分析、語言偵測、文字翻譯和文字分析。 其他內建技能是 Azure 認知搜尋的功能，不需要額外的服務或金鑰。 文字組成者、分隔器和合併是設計管線時有時需要的協助程式技能範例。

如果您僅使用自訂技能和內建的公用程式技能，則沒有與認知服務相關的相依性或成本。

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>在下游解決方案中使用 AI 擴充的輸出

AI 擴充的輸出是 Azure 認知搜尋上的搜尋索引，或 Azure 儲存體中的[知識存放區](knowledge-store-concept-intro.md)。

在 Azure 認知搜尋中，搜尋索引是用來在用戶端應用程式中使用自由文字和篩選查詢進行互動式探索。 透過 AI 建立的擴充檔會以 JSON 格式格式化，並以 Azure 認知搜尋中所有檔編制索引的相同方式來編制索引，並利用索引子提供的所有優點。 例如，在編制索引期間，blob 索引子會參照設定參數和設定，以利用任何欄位對應或變更偵測邏輯。 這類設定完全適用于一般索引和 AI 擴充的工作負載。 索引編制後，當內容儲存在 Azure 認知搜尋時，您可以建立豐富的查詢和篩選運算式，以瞭解您的內容。

在 Azure 儲存體中，知識存放區有兩個形式分門別類： blob 容器或資料表儲存體中的資料表。 

+ Blob 容器會全面捕捉擴充的檔，如果您想要送入其他進程，這會很有用。 

+ 相反地，資料表儲存體可以容納擴充檔的實際預測。 您可以建立包含或排除特定元件之擴充檔的配量或圖層。 針對 Power BI 中的分析，Azure 資料表儲存體中的資料表會成為資料來源，以供進一步的視覺效果和探索。

管線結尾的擴充檔與原始輸入版本不同，因為有其他欄位包含擴充期間所解壓縮或產生的新資訊。 因此，不論您使用何種輸出結構，都可以使用原始和建立內容的組合。

## <a name="next-steps"></a>後續步驟

您還可以利用 AI 擴充來充分運用您在 Azure 儲存體中的資料，包括以不同的方式結合認知服務，以及在沒有任何現有認知服務的情況下，撰寫自訂技能。 您可以遵循下列連結深入瞭解。

+ [使用 Azure 入口網站上傳、下載及列出 Blob (Azure Blob 儲存體)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [設定 Blob 索引子 (Azure 認知搜尋)](search-howto-indexing-azure-blob-storage.md) 
+ [AI 擴充總覽（Azure 認知搜尋）](cognitive-search-concept-intro.md) 
+ [建立技能集（Azure 認知搜尋）](cognitive-search-defining-skillset.md)
+ [對應注釋樹狀結構中的節點（Azure 認知搜尋）](cognitive-search-output-field-mapping.md)
