---
title: 知識存放區簡介 (預覽)
titleSuffix: Azure Cognitive Search
description: 將擴充的檔傳送至 Azure 儲存體，您可以在 Azure 認知搜尋和其他應用程式中查看、重新塑造及取用擴充的檔。 這項功能處於公開預覽狀態。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942988"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Azure 認知搜尋中的知識存放區簡介

> [!IMPORTANT] 
> 知識存放區目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供預覽功能。 目前的入口網站支援有限，而且沒有 .NET SDK 支援。

知識存放區是 Azure 認知搜尋的一項功能，可保存來自[AI 擴充管線](cognitive-search-concept-intro.md)的輸出以進行獨立分析或下游處理。 *擴充的文件*是管線的輸出，從使用 AI 程序進行擷取、結構化及分析的內容建立而來。 在標準 AI 管線中，擴充的文件是暫時性的，只會在編製索引期間使用，其後即捨棄。 透過知識存放區，擴充的文件得以保留。 

如果您過去曾使用過認知技能，您已經知道*技能集*透過一連串的擴充來移動檔。 其結果可能會產生搜尋索引，或 (此預覽版中的新功能) 知識存放區中的投射。 這兩個輸出（搜尋索引和知識存放區）是相同管線的產品;衍生自相同的輸入，但會產生結構化、儲存的輸出，並以非常不同的方式使用。

實際上，知識存放區是 [Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-account-overview)，可能會以 Azure 資料表儲存體和 (或) Azure Blob 儲存體的形式存在。 任何可連線至 Azure 儲存體的工具或程序都可以取用知識存放區的內容。

![管線圖表中的知識存放區](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "管線圖表中的知識存放區")

## <a name="benefits-of-knowledge-store"></a>知識存放區的優點

知識存放區提供您結構、內容和實際的以往累積所有，從非結構化和半結構化資料檔案（例如 blob、經過分析的影像檔，甚至是結構化資料），再改變為新的表單。 在[逐步](knowledge-store-create-rest.md)解說中，您可以看到密集 JSON 檔如何分割成子結構、重組為新的結構，並以其他方式提供給下游進程，例如機器學習服務和資料科學工作負載。

雖然查看 AI 擴充管線可以產生的功能很有用，但知識存放區的實際潛能是能夠改變數據的能力。 您可以從基本的技能集開始，然後逐一查看以便逐漸新增結構層級，然後再結合到新的結構中，就可在 Azure 認知搜尋以外的其他應用程式中取用。

列舉的知識存放區優點包括：

+ 使用搜尋以外的[分析和報告工具](#tools-and-apps)取用擴充文件。 Power Query 的 Power BI 是一種很好的選擇，但是可以連接到 Azure 儲存體的任何工具或應用程式都可以從您建立的知識存放區提取。

+ 為步驟和技能集定義偵錯的同時，精簡 AI 索引管線。 知識存放區會在 AI 索引管線中為您顯示技能集定義的產品。 您可以使用這些結果來設計更好的技能集，因為您可以看到擴充實際的樣子。 您可以使用 Azure 儲存體中的[儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)來查看知識存放區的內容。

+ 將資料塑造成新的形式。 重整是在技能集中編寫的，但重點是技能集現在可以提供這項功能。 Azure 認知搜尋中的[塑形器技能](cognitive-search-skill-shaper.md)已經擴充，可因應這項工作。 重整可讓您定義與資料預定用途相符的投影，同時保留關聯性。

> [!Note]
> AI 擴充和認知技能的新手嗎？ Azure 認知搜尋可與認知服務視覺和語言功能整合，以使用光學字元辨識 (OCR)，透過影像檔、實體辨識以及文字檔中的關鍵片語擷取等等，來擷取並擴充來源資料。 如需詳細資訊，請參閱 [Azure 認知搜尋中的 AI 擴充](cognitive-search-concept-intro.md)。

## <a name="physical-storage"></a>實體儲存體

知識存放區的實體運算式是透過技能集中 `knowledgeStore` 定義的 `projections` 元素來表達。 投射會定義輸出的結構，使其符合您的預期用途。

投影可以表達成資料表、物件或檔案。

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

您在此結構中指定的投射類型會決定知識存放區所使用的儲存類型。

+ 當您定義 `tables`時，會使用資料表儲存體。 當您需要對分析工具輸入的表格式報表結構，或將資料框架匯出至其他資料存放區時，請定義資料表投射。 您可以指定多個 `tables`，以取得擴充檔的子集或跨區段。 在相同的投射群組中，會保留資料表關聯性，讓您可以使用它們。

+ 當您定義 `objects` 或 `files`時，會使用 Blob 儲存體。 `object` 的實體表示是一種階層式 JSON 結構，代表擴充的檔。 `file` 是從檔解壓縮的影像，並完整傳輸至 Blob 儲存體。

單一投射物件包含一組 `tables`、`objects`、`files`，以及許多案例中，建立一個投影可能就已足夠。 

不過，您可以建立多組 `table`-`object`-`file` 投影，如果您想要不同的資料關聯性，您可能會這麼做。 在集合中，資料是相關的，假設這些關聯性存在而且可以偵測。 如果您建立其他集合，則每個群組中的檔絕對不會相關。 使用多個投射群組的範例可能是，如果您想要將相同的資料投射到與您的線上系統搭配使用，而且需要以特定方式表示，則您也會想要在表示的資料科學管線中，使用相同的資料來進行預測完全.

## <a name="requirements"></a>需求 

需要[Azure 儲存體](https://docs.microsoft.com/azure/storage/)。 它提供實體存放裝置。 您可以使用 Blob 儲存體、資料表儲存體或兩者。 Blob 儲存體用於完整擴充的檔，通常是在輸出到下游進程時。 資料表儲存體適用于擴充檔的配量，通常用於分析和報告。

[技能集](cognitive-search-working-with-skillsets.md)是必要的。 它包含 `knowledgeStore` 定義，而且它會決定擴充檔的結構和組合。 您無法使用空的技能集來建立知識存放區。 您在技能集中至少必須有一項技能。

[索引子](search-indexer-overview.md)是必要的。 技能集是由索引子叫用，它會驅動執行。 索引子會隨附自己的一組需求和屬性。 其中幾個屬性對知識存放區具有直接的關係：

+ 索引子需要[支援的 azure 資料來源](search-indexer-overview.md#supported-data-sources)（最後建立知識存放區的管線會從 Azure 上支援的來源提取資料開始）。 

+ 索引子需要搜尋索引。 索引子會要求您提供索引架構，即使您不打算使用它也一樣。 最小索引有一個字串欄位，指定為金鑰。

+ 索引子會提供選擇性的欄位對應，用來將來源欄位別名為目的地欄位。 如果預設欄位對應需要修改（使用不同的名稱或類型），您可以在索引子內建立[欄位對應](search-indexer-field-mappings.md)。 對於知識存放區輸出，目的地可以是 blob 物件或資料表中的欄位。

+ 索引子具有排程和其他屬性，例如各種資料來源所提供的變更偵測機制，也可以套用至知識存放區。 例如，您可以定期[排程](search-howto-schedule-indexers.md)擴充以重新整理內容。 

## <a name="how-to-create-a-knowledge-store"></a>如何建立知識存放區

若要建立知識存放區，請使用入口網站或預覽 REST API （`api-version=2019-05-06-Preview`）。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

[匯**入資料**] wizard 包含用來建立知識存放區的選項。 針對初始探索，請[以四個步驟建立您的第一個知識存放區](knowledge-store-connect-power-bi.md)。

1. 選取支援的資料來源。

1. 指定擴充：附加資源、選取技能，然後指定知識存放區。 

1. 建立索引架構。 嚮導會要求它，而且可以為您推斷一個。

1. 執行嚮導。 在最後一個步驟中，會進行解壓縮、擴充和儲存。

### <a name="use-create-skillset-and-the-preview-rest-api"></a>使用 Create 技能集和 preview REST API

`knowledgeStore` 是在[技能集](cognitive-search-working-with-skillsets.md)內定義的，而[索引子](search-indexer-overview.md)又會叫用它。 在擴充期間，Azure 認知搜尋會在您的 Azure 儲存體帳戶中建立空間，並根據您的設定，將擴充的檔投射為 blob 或資料表。

目前，預覽 REST API 是用來以程式設計方式建立知識存放區的唯一機制。 探索簡單的方式是[使用 Postman 和 REST API 建立您的第一個知識存放區](knowledge-store-create-rest.md)。

此預覽功能的參考內容位於本文的[API 參考](#kstore-rest-api)一節中。 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>如何使用工具和應用程式進行連接

一旦擴充存在於儲存體之後，連線到 Azure Blob 或資料表儲存體的任何工具或技術都可以用來探索、分析或取用內容。 下列清單是一個起點：

+ [儲存體總管](knowledge-store-view-storage-explorer.md)，可檢視擴充的文件結構及內容。 您可以將此視為基準工具來檢視知識存放區內容。

+ 用於報告和分析的[Power BI](knowledge-store-connect-power-bi.md) 。 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)，用於進一步操作。

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API 參考

REST API 版本 `2019-05-06-Preview` 會透過技能集上的其他定義來提供知識存放區。 除了參考以外，請參閱[使用 Postman 建立知識存放區](knowledge-store-create-rest.md)，以取得如何呼叫 api 的詳細資料。

+ [建立技能集 (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Update 技能集（api 版本 = 2019-05 06-01.5.1-Preview）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>後續步驟

知識存放區可提供擴充文件的持續性，在設計技能集時，或是在建立新結構和內容供任何能夠存取 Azure 儲存體帳戶的用戶端應用程式使用時，將可發揮作用。

建立擴充檔的最簡單方法是[透過入口網站](knowledge-store-create-portal.md)，但您也可以使用 Postman 和 REST API，如果您想要深入瞭解如何建立和參考物件，這會更有用。

> [!div class="nextstepaction"]
> [使用 Postman 和 REST 建立知識存放區](knowledge-store-create-rest.md)

若要深入瞭解預測，以及如何[在技能集中定義這些](knowledge-store-projection-overview.md)功能

> [!div class="nextstepaction"]
> [知識存放區中的投影](knowledge-store-projection-overview.md)

如需涵蓋切割、內嵌成形和關聯性等先進預測概念的教學課程，請從[在知識存放區中定義投影](knowledge-store-projections-examples.md)開始

> [!div class="nextstepaction"]
> [定義知識存放區中的投影](knowledge-store-projections-examples.md)