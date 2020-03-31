---
title: 知識存放區簡介 (預覽)
titleSuffix: Azure Cognitive Search
description: 將富集文檔發送到 Azure 存儲，您可以在 Azure 認知搜索和其他應用程式中查看、重塑和使用富集文檔。 這項功能處於公開預覽狀態。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942988"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Azure 認知搜尋中的知識存放區簡介

> [!IMPORTANT] 
> 知識存放區目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供預覽功能。 目前的入口網站支援有限，而且沒有 .NET SDK 支援。

知識存儲是 Azure 認知搜索的一項功能，它保留來自[AI 擴充管道](cognitive-search-concept-intro.md)的輸出，以便獨立分析或下游處理。 *擴充的文件*是管線的輸出，從使用 AI 程序進行擷取、結構化及分析的內容建立而來。 在標準 AI 管線中，擴充的文件是暫時性的，只會在編製索引期間使用，其後即捨棄。 透過知識存放區，擴充的文件得以保留。 

如果你過去使用過認知技能，你已經知道*技能集*通過一系列充實來移動文檔。 其結果可能會產生搜尋索引，或 (此預覽版中的新功能) 知識存放區中的投射。 搜索索引和知識存儲這兩個輸出是同一管道的產品;派生自相同的輸入，但產生以非常不同的方式結構化、存儲和使用輸出。

實際上，知識存放區是 [Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-account-overview)，可能會以 Azure 資料表儲存體和 (或) Azure Blob 儲存體的形式存在。 任何可連線至 Azure 儲存體的工具或程序都可以取用知識存放區的內容。

![管線圖表中的知識存放區](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "管線圖表中的知識存放區")

## <a name="benefits-of-knowledge-store"></a>知識存放區的優點

知識存儲為您提供結構、上下文和實際內容 - 從非結構化和半結構化資料檔案（如 Blob、已進行分析的影像檔，甚至結構化資料）中收集，重新塑造為新表單。 在[分步演練](knowledge-store-create-rest.md)中，您可以親眼看到密集的 JSON 文檔如何被劃分為子結構，重新構建為新結構，並以其他方式提供給下游流程（如機器學習和資料科學工作負載）。

儘管瞭解 AI 擴充管道可以產生什麼非常有用，但知識存儲的真正潛力是重塑資料的能力。 您可以從基本的技能集開始，然後逐一查看以便逐漸新增結構層級，然後再結合到新的結構中，就可在 Azure 認知搜尋以外的其他應用程式中取用。

列舉的知識存放區優點包括：

+ 使用搜尋以外的[分析和報告工具](#tools-and-apps)取用擴充文件。 使用 Power 查詢 Power BI 是一個引人注目的選擇，但任何可以連接到 Azure 存儲的工具或應用都可以從您創建的知識存儲中提取。

+ 為步驟和技能集定義偵錯的同時，精簡 AI 索引管線。 知識存放區會在 AI 索引管線中為您顯示技能集定義的產品。 您可以使用這些結果來設計更好的技能集，因為您可以看到擴充實際的樣子。 您可以使用 Azure[存儲中的存儲資源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)來查看知識存儲的內容。

+ 將資料塑造成新的形式。 重整是在技能集中編寫的，但重點是技能集現在可以提供這項功能。 Azure 認知搜尋中的[塑形器技能](cognitive-search-skill-shaper.md)已經擴充，可因應這項工作。 重整可讓您定義與資料預定用途相符的投影，同時保留關聯性。

> [!Note]
> AI 豐富和認知技能的新增功能？ Azure 認知搜尋可與認知服務視覺和語言功能整合，以使用光學字元辨識 (OCR)，透過影像檔、實體辨識以及文字檔中的關鍵片語擷取等等，來擷取並擴充來源資料。 如需詳細資訊，請參閱 [Azure 認知搜尋中的 AI 擴充](cognitive-search-concept-intro.md)。

## <a name="physical-storage"></a>物理存儲

知識存儲的物理運算式通過技能集中`projections``knowledgeStore`定義的元素進行闡明。 投影定義輸出的結構，使其與預期用途相匹配。

投影可以表述為表、物件或檔。

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

在此結構中指定的投影類型決定了知識存儲使用的存儲類型。

+ 在定義`tables`時使用表存儲。 當您需要用於分析工具輸入的表格式報告結構或作為資料幀匯出到其他資料存儲時，定義表投影。 您可以指定多個以`tables`獲取富集文檔的子集或橫截面。 在同一投影組中，將保留表關係，以便可以使用所有這些關係。

+ 在定義`objects`或`files`時使用 Blob 存儲。 的物理表示`object`形式是表示富集文檔的分層 JSON 結構。 A`file`是從文檔中提取的圖像，完整地傳輸到 Blob 存儲。

單個投影物件包含一組`tables`，`objects``files`對於許多方案，創建一個投影可能就足夠了。 

`table` - `object` - `file`但是，可以創建多組投影，如果需要不同的資料關係，則可以這樣做。 在集內，如果存在這些關係並可以檢測到這些關係，資料是相關的。 如果創建其他集，則每個組中的文檔永遠不會相關。 使用多個投影組的一個示例可能是，如果您希望將相同的資料投影用於連線系統，並且需要以特定方式表示，則還需要相同的投影資料，以便用於表示的資料科學管道不同。

## <a name="requirements"></a>需求 

[Azure 存儲](https://docs.microsoft.com/azure/storage/)是必需的。 它提供物理存儲。 您可以使用 Blob 存儲、表存儲或兩者。 Blob 存儲用於完整豐富的文檔，通常在輸出進入下游進程時。 表存儲用於富集文檔切片，通常用於分析和報告。

[技能集](cognitive-search-working-with-skillsets.md)是必需的。 它包含定義`knowledgeStore`，並確定豐富文檔的結構和組成。 不能使用空技能集創建知識存儲。 技能集中必須至少有一項技能。

索引[器](search-indexer-overview.md)是必需的。 技能集由索引子調用，從而驅動執行。 索引子附帶自己的一組要求和屬性。 其中幾個屬性與知識存儲有直接關係：

+ 索引子需要[受支援的 Azure 資料來源](search-indexer-overview.md#supported-data-sources)（最終創建知識存儲的管道首先從 Azure 上受支援的源提取資料）。 

+ 索引子需要搜索索引。 索引子要求您提供索引架構，即使您從未計畫使用它。 最小索引具有一個字串欄位，指定為鍵。

+ 索引子提供可選的欄位映射，用於將源欄位別名到目標欄位。 如果預設欄位映射需要修改（以使用其他名稱或類型），則可以在索引子中創建[欄位映射](search-indexer-field-mappings.md)。 對於知識存儲輸出，目標可以是 blob 物件或表中的欄位。

+ 索引子具有計劃，其他屬性（如由各種資料來源提供的更改檢測機制）也可以應用於知識存儲。 例如，您可以定期[計畫](search-howto-schedule-indexers.md)充實以刷新內容。 

## <a name="how-to-create-a-knowledge-store"></a>如何創建知識存儲

要創建知識存儲，請使用門戶或預覽 REST API`api-version=2019-05-06-Preview`（ 。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

**"導入資料**"嚮導包括用於創建知識存儲的選項。 對於初始探索，[請分四個步驟創建第一個知識存儲](knowledge-store-connect-power-bi.md)。

1. 選擇受支援的資料來源。

1. 指定擴充：附加資源、選擇技能並指定知識存儲。 

1. 創建索引架構。 嚮導需要它，可以為您推斷出一個。

1. 運行嚮導。 提取、濃縮和存儲發生在最後一步中。

### <a name="use-create-skillset-and-the-preview-rest-api"></a>使用創建技能集和預覽 REST API

在`knowledgeStore`[技能集中](cognitive-search-working-with-skillsets.md)定義 a，而索引[器](search-indexer-overview.md)又調用它。 在充實期間，Azure 認知搜索會在 Azure 存儲帳戶中創建一個空間，並根據配置將富集文檔作為 blob 或專案專案到表中。

目前，預覽 REST API 是您可以以程式設計方式創建知識存儲的唯一機制。 一個簡單的探索方法是[創建你的第一個知識存儲使用郵遞員和REST API。](knowledge-store-create-rest.md)

此預覽功能的參考內容位於本文的[API 參考](#kstore-rest-api)部分。 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>如何與工具和應用程式連接

一旦擴充存在於儲存體之後，連線到 Azure Blob 或資料表儲存體的任何工具或技術都可以用來探索、分析或取用內容。 下列清單是一個起點：

+ [儲存體總管](knowledge-store-view-storage-explorer.md)，可檢視擴充的文件結構及內容。 您可以將此視為基準工具來檢視知識存放區內容。

+ [用於報告和分析的 POWER BI。](knowledge-store-connect-power-bi.md) 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)，用於進一步操作。

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>應用程式開發介面參考

REST API`2019-05-06-Preview`版本通過技能集的其他定義提供知識存儲。 除了參考之外，請參閱使用[Postman 創建知識存儲](knowledge-store-create-rest.md)，瞭解有關如何調用 API 的詳細資訊。

+ [建立技能集 (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [更新技能集（api 版本=2019-05-06-預覽版）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>後續步驟

知識存放區可提供擴充文件的持續性，在設計技能集時，或是在建立新結構和內容供任何能夠存取 Azure 儲存體帳戶的用戶端應用程式使用時，將可發揮作用。

創建富集文檔的最簡單方法是[通過門戶](knowledge-store-create-portal.md)，但您也可以使用 Postman 和 REST API，如果您希望深入瞭解如何創建和引用物件，則這種方法更有用。

> [!div class="nextstepaction"]
> [使用郵遞員和 REST 創建知識存儲](knowledge-store-create-rest.md)

要瞭解有關預測、功能以及如何[在技能集中定義它們](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [知識商店中的投影](knowledge-store-projection-overview.md)

有關高級投影概念（如切片、內聯整形和關係）的教程，請從[知識存儲中定義投影](knowledge-store-projections-examples.md)開始

> [!div class="nextstepaction"]
> [定義知識存儲中的投影](knowledge-store-projections-examples.md)