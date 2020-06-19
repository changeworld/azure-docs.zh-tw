---
title: 技能集概念與工作流程
titleSuffix: Azure Cognitive Search
description: 技能集是您在 Azure 認知搜尋中撰寫 AI 擴充管線的位置。 了解技能集組合的重要概念和詳細資料。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8e263d29bc71ac76c374eeda78e5250a0af2095
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744795"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Azure 認知搜尋中的技能集概念和組合

本文適用於需要深入了解擴充管線運作方式的開發人員，並假設您已了解 AI 擴充流程的概念。 如果您剛開始認識這個概念，請先認識：
+ [Azure 認知搜尋中的 AI 擴充](cognitive-search-concept-intro.md)
+ [知識存放區 (預覽)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>指定技能集
技能集是 Azure 認知搜尋中可重複使用的資源，可指定在編制索引期間用來分析、轉換和擴充文字或影像內容的認知技能集合。 建立技能集可讓您在資料擷取階段附加文字和影像擴充，並從原始內容中擷取和建立新的資訊和結構。

技能集有三個屬性：

+    ```skills```，為一種未排序的技能集合，平台會根據每項技能所需的輸入內容來決定技能的執行順序
+    ```cognitiveServices```，為將叫用的認知技能計費所需的認知技能索引鑰
+    ```knowledgeStore```，為要投影已擴充文件所在的儲存體帳戶



技能集是以 JSON 撰寫而成。 您可以使用[運算式語言](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)，以透過迴圈和[分支](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)來組建複雜的技能。 運算式語言會使用 [JSON 指標](https://tools.ietf.org/html/rfc6901) 路徑標記法，並進行一些修改，來識別擴充樹狀結構中的節點。 ```"/"``` 會在樹狀結構中向下周遊一個層級，而 ```"*"``` 則會作為內容中的 for-each 運算子。 用範例最能清楚說明這些概念。 為了說明其中的一些概念和功能，我們將逐步解說[飯店評論範例](knowledge-store-connect-powerbi.md)技能集。 在您遵循匯入資料工作流程後，若要檢視技能集，您必須使用 REST API 用戶端[取得技能集](https://docs.microsoft.com/rest/api/searchservice/get-skillset)。

### <a name="enrichment-tree"></a>擴充樹狀結構

為了想像技能集如何逐漸地擴充您的文件，讓我們從文件在進行任何擴充之前的樣子開始。 文件萃取的輸出取決於資料來源和所選取的特定剖析模式。 這也是在將資料新增至搜尋索引時，[欄位對應](search-indexer-field-mappings.md)會從中取得內容的文件狀態。
![管線圖表中的知識存放區](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "管線圖表中的知識存放區")

一旦文件在擴充管線中，系統就會以內容樹狀結構和相關聯的擴充來表示該文件。 此樹狀結構會具現化為文件萃取的輸出。 擴充樹狀結構格式可讓擴充管線將中繼資料附加至均等基本資料類型，它不是有效的 JSON 物件，但可以投影至有效的 JSON 格式。 下表說明進入擴充管線的文件狀態：

|資料來源\剖析模式|預設|JSON、JSON 行與 CSV|
|---|---|---|
|Blob 儲存體|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/A |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/A|

 當技能執行時，他們會將新節點新增至擴充樹狀結構。 然後，這些新節點可以作為下游技能的輸入、投影到知識存放區，或對應至索引欄位。 擴充不是可變動的：一旦建立，就無法編輯節點。 隨著您的技能變得更複雜，您的擴充樹狀結構也會更加複雜，但並非擴充樹狀結構中的所有節點都需要將它設為索引或知識存放區。 

您可以選擇性地只將擴充的子集保存至索引或知識存放區。
在本文件的其餘部分中，我們會假設在處理[飯店評論範例](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)，但相同的概念也適用於從所有其他資料來源來擴充文件的情況。

### <a name="context"></a>Context
每項技能都需要有內容。 內容會決定：
+    技能根據所選節點執行的次數。 對於類型集合的內容值，在結尾加上 ```/*``` 可讓系統為集合中的每個執行個體叫用技能一次。 
+    在擴充樹狀結構中新增技能輸出的位置。 系統一律將輸出作為內容節點的子系來新增至樹狀結構中。 
+    輸入的圖形。 對於多層集合，將內容設定為父系集合會影響技能輸入的圖形。 例如，如果您的擴充樹狀結構具有國家/地區清單，則每個國家/地區都會以包含郵遞區號清單的州/省清單加以擴充。

|Context|輸入|輸入的圖形|技能引動過程|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |國家/地區中所有郵遞區號的清單 |每個國家/地區一次 |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |該州/省中的郵遞區號清單 | 每個國家/地區和州/省的組合一次|

### <a name="sourcecontext"></a>SourceContext

`sourceContext` 僅用於技能輸入和[投影](knowledge-store-projection-overview.md)。 它是用來建構多層級的巢狀物件。 您可能需要建立新的物件，以將其傳遞為對技能的輸入或投影至知識存放區。 由於擴充節點在擴充樹狀結構中可能不是有效的 JSON 物件，且參考樹狀結構中的節點只會傳回該節點在建立時的狀態，您會需要建立格式正確的 JSON 物件才能使用擴充內容作為技能輸入或投影。 此 `sourceContext` 可讓您建構階層式匿名型別物件，如果您只使用內容，就需要多個技能。 下一節會說明如何使用 `sourceContext`。 查看產生擴充的技能輸出，以判定其是否為有效的 JSON 物件，而非基本類型。

### <a name="projections"></a>投影

投影是一種流程，會從擴充樹狀結構中選取要儲存在知識存放區中的節點。 投影是文件 (內容和擴充) 的自訂圖形，可以作為表格或物件投影的輸出。 若要深入了解如何使用投影，請參閱[使用投影](knowledge-store-projection-overview.md)。

![欄位對應選項](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "擴充管線的欄位對應選項")

上圖根據您在擴充管線中的位置來描述您使用的選取器。

## <a name="generate-enriched-data"></a>產生擴充的資料 

現在讓我們逐步執行飯店評論技能集，您可以遵循[教學課程](knowledge-store-connect-powerbi.md)建立技能集或[檢視](https://github.com/Azure-Samples/azure-search-postman-samples/)技能集。 我們將會了解：

* 擴充樹狀結構如何隨著每項技能的執行而演變 
* 內容和輸入如何判斷技能執行的次數 
* 根據內容所產生的輸入圖形。 

因為我們針對索引子使用分隔的文字剖析模式，所以擴充流程內的文件代表 CSV 檔案內的單一資料列。

### <a name="skill-1-split-skill"></a>技能 #1：分割技能 

![文件萃取後的擴充樹狀結構](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "在文件萃取後和技能執行前的擴充樹狀結構")

此技能利用 ```"/document/reviews_text"``` 的技能內容，會針對 `reviews_text` 執行一次。 技能會輸出為一份清單，將 `reviews_text` 分成 5000 個字元區段。 來自分割技能的輸出會命名為 `pages` 並新增至擴充樹狀結構。 `targetName` 功能可讓您先將技能輸出重新命名，再新增至擴充樹狀結構。

現在擴充樹狀結構在技能內容之下有了新的節點。 這個節點適用於任何技能、投影或輸出欄位對應。


所有擴充的根節點都是 `"/document"`。 使用 blob 索引子時，`"/document"` 節點將會有 `"/document/content"` 和 `"/document/normalized_images"` 的子系節點。 使用 CSV 資料時，如同我們在此範例中所示，資料行名稱將會對應至 `"/document"` 下的節點。 若要存取由技能新增至節點的任何擴充內容，則需要擴充內容的完整路徑。 例如，如果您想要使用 ```pages``` 節點中的文字作為另一個技能的輸入，您必須將其指定為 ```"/document/reviews_text/pages/*"```。
 
 ![在技能 #1 之後的擴充樹狀結構](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "在技能 #1 執行之後的擴充樹狀結構")

### <a name="skill-2-language-detection"></a>技能 #2 語言偵測
 雖然語言偵測技能是技能集中所定義的第三個技能 (技能 #3)，但它是下一個要執行的技能。 由於它不會因要求任何輸入而受到封鎖，因此將會與前一個技能同時執行。 如同之前進行的分割技能，系統也會針對每個文件叫用語言偵測技能一次。 擴充樹狀結構現在具有語言的新節點。
 ![在技能 #2 之後的擴充樹狀結構](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "在技能 #2 執行之後的擴充樹狀結構")
 
 ### <a name="skill-3-key-phrases-skill"></a>技能 #3：關鍵片語技能 

若有 ```/document/reviews_text/pages/*``` 的內容，系統就會針對 `pages` 集合中的每個項目叫用關鍵片語技能一次。 技能的輸出將會是相關聯頁面元素下方的節點。 

 您現在應該能夠查看技能集中的其餘技能，並以視覺化方式呈現擴充的樹狀結構如何隨著每項技能的執行而持續成長。 有些技能 (例如合併技能和塑形器技能) 也會建立新的節點，但只會使用現有節點的資料，而不會建立全新的擴充。

![在所有技能之後的擴充樹狀結構](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "在所有技能執行之後的擴充樹狀結構")

上述樹狀結構中的連接器色彩表示擴充是由不同技能建立的，而節點則必須個別定址，且不會成為選取父系節點時所傳回物件的一部分。

## <a name="save-enrichments-in-a-knowledge-store"></a>將擴充儲存到知識存放區 

技能集也會定義知識存放區，在此可將已擴充的文件投影為資料表或物件。 若要將已擴充的資料儲存在知識存放區中，您可以為擴充的文件定義一組投影。 若要深入了解知識存放區，請參閱[知識存放區概觀](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>切割投影

定義資料表投影群組時，擴充樹狀結構中的單一節點可以切割為多個相關的資料表。 如果您新增的資料表其來源路徑是現有資料表投影的子系，則產生的子系節點將不會是現有資料表投影的子系，而是會投影到新的相關資料表。 這項切割技術可讓您在塑形器技能中定義單一節點，該節點會是所有資料表投影的來源。 

### <a name="shaping-projections"></a>塑形投影

有兩種方式可定義投影。 您可以使用塑形器技能來建立新節點，該節點會是所有您要投影的擴充的根節點。 然後，在您的投影中，您只會參考塑形器技能的輸出。 您也可以將投影內嵌塑形在投影定義本身中。

塑形器方法比內嵌塑形冗長，但可確保擴充樹狀結構的所有變動都包含在技能內，而且輸出是可以重複使用的物件。 內嵌塑形可讓您建立所需的圖形，但它是匿名物件，只適用於所定義的投影。 這些方法可以一起使用或分開使用。 在入口網站工作流程中為您建立的技能集包含這兩者。 它將塑形器技能用於資料表投影，但也使用內嵌塑形來投影關鍵片語資料表。

若要進一步擴充範例，您可以選擇移除內嵌塑形，並使用塑形器技能來為關鍵片語建立新的節點。 若要建立投影到三個資料表的圖形，也就是 `hotelReviewsDocument`、`hotelReviewsPages` 和 `hotelReviewsKeyPhrases`，以下幾節會說明這兩個選項。


#### <a name="shaper-skill-and-projection"></a>塑形器技能和投影 

> [!Note]
> 為了簡潔起見，已將文件資料表中的一些資料行從這個範例中移除。
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

透過在前述 `outputs` 一節中定義的 `tableprojection` 節點，我們現在可以使用切割功能將 `tableprojection` 節點的部分投影到不同的資料表：

> [!Note]
> 這只是知識存放區設定內投影的程式碼片段。
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>內嵌塑形投影

內嵌塑形方法不需要塑形器技能，因為投影所需的所有圖形都是在需要時才建立。 若要投影與前一個範例相同的資料，內嵌投影選項會如下所示：

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
值得注意的一點是，這兩種方法是如何使用 `"sourceContext"` 來投影 `"Keyphrases"` 的值。 `"Keyphrases"` 節點，包含字串集合，本身為頁面文字的子系。 不過，因為投影需要 JSON 物件，而頁面是一個基本類型 (字串)，所以會使用 `"sourceContext"` 將關鍵片語包裝到具有命名屬性的物件中。 這項技術可以獨立地投影均等基本類型。

## <a name="next-steps"></a>後續步驟

在下一個步驟中，使用認知技能建立您的第一個技能集。

> [!div class="nextstepaction"]
> [建立您的第一個技能集](cognitive-search-defining-skillset.md)。
