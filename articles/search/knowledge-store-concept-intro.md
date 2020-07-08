---
title: 知識存放區概念
titleSuffix: Azure Cognitive Search
description: 將擴充文件傳送到 Azure 儲存體，以便從該處檢視、調整及取用 Azure 認知搜尋和其他應用程式中的擴充文件。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 75ecfcca24aa801c2ec277e810f60dbc0a9167fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565266"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Azure 認知搜尋中的知識存放區

知識存放區是 Azure 認知搜尋中的一項功能，可保存 [AI 擴充管線](cognitive-search-concept-intro.md)的輸出，以進行獨立分析或下游處理。 *擴充的文件*是管線的輸出，從使用 AI 程序進行擷取、結構化及分析的內容建立而來。 在標準 AI 管線中，擴充的文件是暫時性的，只會在編製索引期間使用，其後即捨棄。 選擇建立知識存放區，可讓您保留擴充的檔。 

如果過去使用過認知技能，您已經知道該「技能集」會透過一連串的擴充來移動文件。 結果可以是搜尋索引，或在知識存放區中的投影。 這兩個輸出 (搜尋索引和知識存放區) 是相同管線的產品；衍生自相同輸入，但會導致產生以非常不同的方式來結構化、儲存及使用的輸出。

實際上，知識存放區是 [Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-account-overview)，可能會以 Azure 資料表儲存體和 (或) Azure Blob 儲存體的形式存在。 任何可連線至 Azure 儲存體的工具或程序都可以取用知識存放區的內容。


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=235&end=426]


![管線圖表中的知識存放區](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "管線圖表中的知識存放區")

## <a name="benefits-of-knowledge-store"></a>知識存放區的優點

知識存放區可從非結構化及半結構化資料檔案 (如 Blob、經過分析的影像檔)，甚至是重新調整成新格式的結構化資料，以提供結構、情境和實際的內容。 在[逐步解說](knowledge-store-create-rest.md)中，您可率先看到密集的 JSON 文件如何分割成子結構，重新建構成新結構，並提供給下游處理序 (例如機器學習服務和資料科學工作負載) 使用。

知識存放區可用來查看 AI 擴充管線可產生的內容，但其真正的潛力在於能夠重新調整資料。 您可以從基本的技能集開始，然後逐一查看以便逐漸新增結構層級，然後再結合到新的結構中，就可在 Azure 認知搜尋以外的其他應用程式中取用。

列舉的知識存放區優點包括：

+ 使用搜尋以外的[分析和報告工具](#tools-and-apps)取用擴充文件。 Power BI 搭配 Power Query 是相當具吸引力的選擇，但可連線到 Azure 儲存體的任何工具或應用程式都能夠從所建立知識存放區提取。

+ 為步驟和技能集定義偵錯的同時，精簡 AI 索引管線。 知識存放區會在 AI 索引管線中為您顯示技能集定義的產品。 您可以使用這些結果來設計更好的技能集，因為您可以看到擴充實際的樣子。 您可使用 Azure 儲存體中[儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)來檢視知識存放區的內容。

+ 將資料塑造成新的形式。 重整是在技能集中編寫的，但重點是技能集現在可以提供這項功能。 Azure 認知搜尋中的[塑形器技能](cognitive-search-skill-shaper.md)已經擴充，可因應這項工作。 重整可讓您定義與資料預定用途相符的投影，同時保留關聯性。

> [!Note]
> 不熟悉 AI 擴充和認知技能嗎？ Azure 認知搜尋可與認知服務視覺和語言功能整合，以使用光學字元辨識 (OCR)，透過影像檔、實體辨識以及文字檔中的關鍵片語擷取等等，來擷取並擴充來源資料。 如需詳細資訊，請參閱 [Azure 認知搜尋中的 AI 擴充](cognitive-search-concept-intro.md)。

## <a name="physical-storage"></a>實體儲存體


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=455&end=542]


知識存放區其實體運算式是透過技能集中 `knowledgeStore` 定義的 `projections` 項目來表達。 投影會定義輸出的結構，使其符合預期用途。

投影可以資料表、物件或檔案的形式來表達。

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

您在此結構中指定的投影類型會決定知識存放區所使用儲存體類型。

+ 當定義 `tables` 時，即會使用資料表儲存體。 當需要表格式報告結構以輸入至分析工具，或作為資料框架匯出至其他資料存放區時，請定義資料表投影。 您可指定多個 `tables` 來取得擴充文件的子集或橫剖面。 在相同的投影群組內會保留資料表關聯性，如此即可使用所有的資料表。

+ 當定義 `objects`或 `files` 時，就會使用 Blob 儲存體。 `object` 其實體表示是一種代表擴充文件的階層式 JSON 結構。 `file` 是從文件擷取並完整傳輸至 Blob 儲存體的影像。

單一投影物件包含一組 `tables`、`objects`、`files`，且在許多情況下，建立一個投影可能就已足夠。 

不過，您可建立多組的 `table`-`object`-`file` 投影，且如果需要不同的資料關聯性就可以這麼做。 在集合中，假設這些關聯性存在且可被偵測到，則資料是相關的。 如果您建立其他集合，則每個群組中的文件絕不相關。 使用多個投影群組的範例可能是，如果想要將相同的資料投影以供線上系統使用，且其需要以特定方式表示，則也會想要將相同資料投影以用於透過不同方式表示的資料科學管線。

## <a name="requirements"></a>需求 

需要 [Azure 儲存體](https://docs.microsoft.com/azure/storage/)。 其提供實體記憶體。 您可使用 Blob 儲存體、資料表儲存體或這兩者。 Blob 儲存體用於完整擴充文件，通常是在輸出移至下游處理序時。 資料表儲存體則適用於擴充文件的配量，通常用於分析和報告。

需要[技能集](cognitive-search-working-with-skillsets.md)。 其包含 `knowledgeStore` 定義，且會決定擴充文件的結構和組合。 您無法使用空的技能集來建立知識存放區。 技能集中至少必須有一個技能。

[索引子](search-indexer-overview.md)是必要項。 技能集是由驅動執行的索引子叫用。 索引子會隨附自己的一組需求和屬性。 其中幾個屬性與知識存放區具有直接關係：

+ 索引子需要[支援的 Azure 資料來源](search-indexer-overview.md#supported-data-sources) (最後建立知識存放區的管線會從 Azure 上支援的來源提取資料開始)。 

+ 索引子需要搜尋索引。 索引子會要求提供索引結構描述，即使您不打算加以使用也一樣。 最小索引有一個指定為金鑰的字串欄位。

+ 索引子會提供選擇性的欄位對應，用來將來源欄位的別名更改為目的地欄位。 如果預設欄位對應需要修改 (以使用不同的名稱或類型)，則可在索引子內建立[欄位對應](search-indexer-field-mappings.md)。 針對知識存放區輸出，目的地可以是 Blob 物件或資料表中的欄位。

+ 索引子具有排程和其他屬性 (例如各種資料來源所提供的變更偵測機制)，也可以套用到知識存放區。 例如，您可定期[排程](search-howto-schedule-indexers.md)擴充來重新整理內容。 

## <a name="how-to-create-a-knowledge-store"></a>如何建立知識存放區

若要建立知識存放區，請使用入口網站或 REST API （ `api-version=2020-06-30` ）。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

[匯入資料精靈] 包含用來建立知識存放區的選項。 為了進行初始探索，請[以四個步驟建立第一個知識存放區](knowledge-store-connect-power-bi.md)。

1. 選取支援的資料來源。

1. 指定擴充：附加資源、選取技能，然後指定知識存放區。 

1. 建立索引結構描述。 精靈需要此結構描述，且可為您推斷一個。

1. 執行精靈。 在最後一個步驟中進行擷取、擴充及儲存。

### <a name="use-create-skillset-rest-api"></a>使用 Create 技能集（REST API）]

`knowledgeStore` 是在[技能集](cognitive-search-working-with-skillsets.md)內定義，而[索引子](search-indexer-overview.md)接著會叫用技能集。 在擴充期間，Azure 認知搜尋會在 Azure 儲存體帳戶中建立一個空間，並視設定將擴充文件投影為 Blob 或投影到資料表中，。

REST API 是您可以用程式設計方式建立知識存放區的一種機制。 簡單的探索方法是[使用 Postman 和 REST API 來建立第一個知識存放區](knowledge-store-create-rest.md)。

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>如何與工具和應用程式連線

一旦擴充存在於儲存體之後，連線到 Azure Blob 或資料表儲存體的任何工具或技術都可以用來探索、分析或取用內容。 下列清單是一個起點：

+ [儲存體總管](knowledge-store-view-storage-explorer.md)，可檢視擴充的文件結構及內容。 您可以將此視為基準工具來檢視知識存放區內容。

+ [Power BI](knowledge-store-connect-power-bi.md)，用於報告和分析。 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)，用於進一步操作。

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API 參考資料

REST API 版本 `2020-06-30` 會透過技能集的其他定義來提供知識存放區。 除了參考以外，另請參閱[使用 Postman 建立知識存放區](knowledge-store-create-rest.md)，以取得如何呼叫 API 的詳細資料。

+ [建立技能集（api 版本 = 2020-06-30）](https://docs.microsoft.com/rest/api/searchservice/2020-06-30/create-skillset)
+ [Update 技能集（api 版本 = 2020-06-30）](https://docs.microsoft.com/rest/api/searchservice/2020-06-30/update-skillset)


## <a name="next-steps"></a>後續步驟

知識存放區可提供擴充文件的持續性，在設計技能集時，或是在建立新結構和內容供任何能夠存取 Azure 儲存體帳戶的用戶端應用程式使用時，將可發揮作用。

建立擴充檔的最簡單方法是[透過入口網站](knowledge-store-create-portal.md)，但您也可以使用 Postman 和 REST API，如果您想要深入瞭解如何建立和參考物件，這會更有用。

> [!div class="nextstepaction"]
> [使用 Postman 和 REST 建立知識存放區](knowledge-store-create-rest.md)

深入了解投影、功能，以及如何[在技能集中定義這些項目](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [知識存放區中的投影](knowledge-store-projection-overview.md)

如需涵蓋配量、內嵌塑形及關聯性等進階投影概念的教學課程，請從[在知識存放區中定義投影](knowledge-store-projections-examples.md)開始

> [!div class="nextstepaction"]
> [定義知識存放區中的投影](knowledge-store-projections-examples.md)