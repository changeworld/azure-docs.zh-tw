---
title: 技能集概念與工作流程
titleSuffix: Azure Cognitive Search
description: 技能集是您在 Azure 認知搜尋中撰寫 AI 擴充管線的位置。 了解技能集組合的重要概念和詳細資料。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: f1d8715fcadeda5ccd1a98192a70939b0c359c88
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976671"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Azure 認知搜尋中的技能集概念

本文適用于需要更深入瞭解技能集概念和撰寫的開發人員，並假設您已熟悉 AI 擴充流程。 如果您不熟悉此概念，請從[Azure 認知搜尋中的 AI 擴充](cognitive-search-concept-intro.md)開始。

## <a name="introducing-skillsets"></a>技能集簡介

技能集是 Azure 認知搜尋中的可重複使用資源，會連接到索引子，並指定在編制索引期間用來分析、轉換和充實文字或影像內容的技能集合。 技能具有輸入和輸出，而一個技能的輸出通常會成為一或多個流程的輸入。

技能集有三個主要屬性：

+ `skills`，這是一種未排序的技能集合，平臺會根據每項技能所需的輸入來決定執行順序。
+ `cognitiveServices`，認知服務資源的索引鍵，它會針對包含內建技能的技能集執行影像和文字處理。
+ `knowledgeStore`，（選擇性）將會投射擴充檔的 Azure 儲存體帳戶。 搜尋索引也會耗用擴充的檔。

技能集是以 JSON 撰寫而成。 下列範例是此[飯店評論技能集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)的稍微簡化版本，用來說明本文中的概念。 

前兩個技能如下所示：

+ 技能 #1 是[文字分割技能](cognitive-search-skill-textsplit.md)，可接受 "reviews_text" 欄位的內容做為輸入，並將該內容分割成5000個字元的「頁面」作為輸出。
+ 技能 #2 是[情感偵測技能](cognitive-search-skill-sentiment.md)會接受「頁面」做為輸入，並產生稱為「情感」的新欄位，做為包含情感分析結果的輸出。


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> 您可以使用[條件式技能](cognitive-search-skill-conditional.md)來建立運算式，以透過迴圈和分支建立複雜的技能集。 語法是以[JSON 指標](https://tools.ietf.org/html/rfc6901)路徑標記法為基礎，只有一些修改來識別擴充樹狀結構中的節點。 `"/"` 會在樹狀結構中向下周遊一個層級，而 `"*"` 則會作為內容中的 for-each 運算子。 本文中的許多範例將說明語法。 

### <a name="enrichment-tree"></a>擴充樹狀結構

在[擴充管線的步驟](cognitive-search-concept-intro.md#enrichment-steps)進展中，內容處理會遵循*檔破解*階段，其中文字和影像會從來源解壓縮。 然後，影像內容可以路由至指定影像處理的技能，而文字內容則會排入佇列以進行文字處理。 對於包含大量文字的來源文件，您可以在索引子上設定*剖析模式*，將文字分割成較小的區塊，以進行更理想的處理。 

![管線圖表中的知識存放區](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "管線圖表中的知識存放區")

一旦文件在擴充管線中，系統就會以內容樹狀結構和相關聯的擴充來表示該文件。 此樹狀結構會具現化為文件萃取的輸出。  擴充樹狀結構格式可讓擴充管線將中繼資料附加至均等基本資料類型，它不是有效的 JSON 物件，但可以投影至有效的 JSON 格式。 下表說明進入擴充管線的文件狀態：

|資料來源\剖析模式|預設|JSON、JSON 行與 CSV|
|---|---|---|
|Blob 儲存體|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/A |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/A|

 當技能執行時，他們會將新節點新增至擴充樹狀結構。 然後，這些新節點可以作為下游技能的輸入、投影到知識存放區，或對應至索引欄位。 擴充不是可變動的：一旦建立，就無法編輯節點。 隨著您的技能變得更複雜，您的擴充樹狀結構也會更加複雜，但並非擴充樹狀結構中的所有節點都需要將它設為索引或知識存放區。 

您可以選擇性地只將擴充的子集保存至索引或知識存放區。

### <a name="context"></a>Context

每項技能都需要有內容。 內容會決定：

+ 技能根據所選節點執行的次數。 對於類型集合的內容值，在結尾加上 `/*` 可讓系統為集合中的每個執行個體叫用技能一次。 

+ 在擴充樹狀結構中新增技能輸出的位置。 系統一律將輸出作為內容節點的子系來新增至樹狀結構中。 

+ 輸入的圖形。 對於多層集合，將內容設定為父系集合會影響技能輸入的圖形。 例如，如果您的擴充樹狀目錄中有一份國家/地區清單，每個都有一份包含 ZIP 代碼清單的州清單。

|Context|輸入|輸入的圖形|技能引動過程|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |國家/地區中所有郵遞區號的清單 |每個國家/地區一次 |
|`/document/countries/*/states/*` |'/document/countries/*/states/*/zipcodes/* ' ' |狀態中的 ZIP 代碼清單 | 每個國家/地區和州/省的組合一次|

## <a name="generate-enriched-data"></a>產生擴充的資料 

使用[飯店評論技能集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)做為參考點，我們將探討：

+ 擴充樹狀結構如何隨著每項技能的執行而演變
+ 內容和輸入如何用於判斷技能執行的次數
+ 輸入的形式是根據內容而定

擴充程式內的「檔」代表 hotel_reviews.csv 原始程式檔內的單一資料列（飯店評論）。

### <a name="skill-1-split-skill"></a>技能 #1：分割技能

當來源內容包含大量文字時，將其分成較小的元件，以提高語言、情感和關鍵字組偵測的正確性，會很有説明。 有兩個可用的粒紋：頁面和句子。 頁面包含大約5000個字元。

文字分割技能通常是在技能集中的第一個。

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

在的技能內容 `"/document/reviews_text"` 中，分割技能會針對執行一次 `reviews_text` 。 技能會輸出為一份清單，將 `reviews_text` 分成 5000 個字元區段。 分割技能的輸出會命名為 `pages` ，並加入至擴充樹狀結構。 `targetName` 功能可讓您先將技能輸出重新命名，再新增至擴充樹狀結構。

現在擴充樹狀結構在技能內容之下有了新的節點。 這個節點適用於任何技能、投影或輸出欄位對應。 就概念而言，樹狀結構看起來如下：

![文件萃取後的擴充樹狀結構](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "在文件萃取後和技能執行前的擴充樹狀結構")

所有擴充的根節點都是 `"/document"`。 使用 blob 索引子時，`"/document"` 節點將會有 `"/document/content"` 和 `"/document/normalized_images"` 的子系節點。 使用 CSV 資料時，如同我們在此範例中所示，資料行名稱將會對應至 `"/document"` 下的節點。 

若要存取由技能新增至節點的任何擴充內容，則需要擴充內容的完整路徑。 例如，如果您想要使用 ```pages``` 節點中的文字作為另一個技能的輸入，您必須將其指定為 ```"/document/reviews_text/pages/*"```。
 
 ![在技能 #1 之後的擴充樹狀結構](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "在技能 #1 執行之後的擴充樹狀結構")

### <a name="skill-2-language-detection"></a>技能 #2 語言偵測

飯店審查檔包含以多種語言表示的客戶意見反應。 語言偵測技能會決定所使用的語言。 然後，結果會傳遞至關鍵字組的解壓縮和情感偵測，並在偵測情感和片語時，將語言納入考慮。

雖然語言偵測技能是技能集中所定義的第三個技能 (技能 #3)，但它是下一個要執行的技能。 由於它不會因要求任何輸入而受到封鎖，因此將會與前一個技能同時執行。 如同之前進行的分割技能，系統也會針對每個文件叫用語言偵測技能一次。 擴充樹狀結構現在具有語言的新節點。

 ![在技能 #2 之後的擴充樹狀結構](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "在技能 #2 執行之後的擴充樹狀結構")
 
 ### <a name="skill-3-key-phrases-skill"></a>技能 #3：關鍵片語技能 

若有 `/document/reviews_text/pages/*` 的內容，系統就會針對 `pages` 集合中的每個項目叫用關鍵片語技能一次。 技能的輸出將會是相關聯頁面元素下方的節點。 

 您現在應該能夠查看技能集中的其餘技能，並以視覺化方式呈現擴充的樹狀結構如何隨著每項技能的執行而持續成長。 有些技能 (例如合併技能和塑形器技能) 也會建立新的節點，但只會使用現有節點的資料，而不會建立全新的擴充。

![在所有技能之後的擴充樹狀結構](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "在所有技能執行之後的擴充樹狀結構")

上述樹狀結構中的連接器色彩表示擴充是由不同技能建立的，而節點則必須個別定址，且不會成為選取父系節點時所傳回物件的一部分。

## <a name="save-enrichments"></a>儲存擴充

在 Azure 認知搜尋中，索引子會儲存它所建立的輸出。 其中一個輸出一律是可搜尋的[索引](search-what-is-an-index.md)。 指定索引是一項需求，而且當您附加技能集時，索引所內嵌的資料就會包含擴充的物質。 通常，特定技能的輸出（例如關鍵字組或情感分數），會內嵌至針對該用途所建立之欄位中的索引。

索引子也可以選擇性地將輸出傳送到[知識存放區](knowledge-store-concept-intro.md)，以供在其他工具或進程中取用。 知識存放區會定義為技能集的一部分。 其定義會決定是否將您的擴充檔投射為數據表或物件（檔案或 blob）。 表格式預測非常適合用於 Power BI 這類工具中的互動式分析，而檔案和 blob 通常用於資料科學或類似的進程。 在本節中，您將瞭解技能集組合可以如何塑造您想要投影的資料表或物件。

### <a name="projections"></a>投影

對於以知識存放區為目標的內容，您會想要考慮內容的結構。 *投射*是從擴充樹狀結構中選取節點，並在知識存放區中建立其實體運算式的程式。 投影是文件 (內容和擴充) 的自訂圖形，可以作為表格或物件投影的輸出。 若要深入了解如何使用投影，請參閱[使用投影](knowledge-store-projection-overview.md)。

![欄位對應選項](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "擴充管線的欄位對應選項")

### <a name="sourcecontext"></a>SourceContext

`sourceContext`元素僅用於技能輸入和投影。 它是用來建構多層級的巢狀物件。 您可能需要建立新的物件，以將其傳遞為對技能的輸入或投影至知識存放區。 由於擴充節點在擴充樹狀結構中可能不是有效的 JSON 物件，且參考樹狀結構中的節點只會傳回該節點在建立時的狀態，您會需要建立格式正確的 JSON 物件才能使用擴充內容作為技能輸入或投影。 此 `sourceContext` 可讓您建構階層式匿名型別物件，如果您只使用內容，就需要多個技能。 

使用 `sourceContext` 如下列範例所示。 查看產生擴充的技能輸出，以判定其是否為有效的 JSON 物件，而非基本類型。

### <a name="slicing-projections"></a>切割投影

定義資料表投影群組時，擴充樹狀結構中的單一節點可以切割為多個相關的資料表。 如果您新增的資料表其來源路徑是現有資料表投影的子系，則產生的子系節點將不會是現有資料表投影的子系，而是會投影到新的相關資料表。 這項切割技術可讓您在塑形器技能中定義單一節點，該節點會是所有資料表投影的來源。 

### <a name="shaping-projections"></a>塑形投影

有兩種方式可以定義投射：

+ 使用文字整形程式技能來建立新節點，這是您要投射之所有擴充的根節點。 然後，在您的投影中，您只會參考塑形器技能的輸出。

+ 在投影定義本身中使用內嵌圖形做為投射。

塑形器方法比內嵌塑形冗長，但可確保擴充樹狀結構的所有變動都包含在技能內，而且輸出是可以重複使用的物件。 相反地，內嵌成形可讓您建立所需的圖形，但是是匿名物件，而且只適用于其定義所在的投影。 這些方法可以一起使用或分開使用。 在入口網站工作流程中為您建立的技能集包含這兩者。 它將塑形器技能用於資料表投影，但也使用內嵌塑形來投影關鍵片語資料表。

若要進一步擴充範例，您可以選擇移除內嵌塑形，並使用塑形器技能來為關鍵片語建立新的節點。 若要建立投影到三個資料表的圖形，也就是 `hotelReviewsDocument`、`hotelReviewsPages` 和 `hotelReviewsKeyPhrases`，以下幾節會說明這兩個選項。

#### <a name="shaper-skill-and-projection"></a>塑形器技能和投影

此 

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
