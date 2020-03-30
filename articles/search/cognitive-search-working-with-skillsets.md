---
title: 技能集概念和工作流
titleSuffix: Azure Cognitive Search
description: 技能集是在 Azure 認知搜索中創作 AI 擴充管道的位置。 瞭解有關技能集組合的重要概念和詳細資訊。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191035"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Azure 認知搜索中的技能集概念和組合

本文面向需要更深入地瞭解濃縮管道工作原理並假定您對 AI 濃縮過程有概念理解的開發人員。 如果您是新概念，則從：
+ [Azure 認知搜索中的 AI 擴充](cognitive-search-concept-intro.md)
+ [知識存放區 (預覽)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>指定技能集
技能集是 Azure 認知搜索中的可重用資源，用於指定用於在索引期間分析、轉換和豐富文本或圖像內容的認知技能集合。 通過創建技能集，您可以在資料引入階段附加文本和圖像擴充，從原始內容中提取和創建新資訊和結構。

技能集有三個屬性：

+   ```skills```，平臺根據每種技能所需的輸入確定執行順序的無序技能集合
+   ```cognitiveServices```，計費調用的認知技能所需的認知服務金鑰
+   ```knowledgeStore```，將投影豐富文檔的存儲帳戶



技能集在 JSON 中編寫。 您可以使用[運算式語言](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)使用迴圈和[分支](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)構建複雜的技能集。 運算式語言使用[JSON 指標](https://tools.ietf.org/html/rfc6901)路徑符號法進行一些修改來標識擴充樹中的節點。 遍```"/"```曆樹中較低的級別，並在```"*"```上下文中充當 for-每個運算子。 這些概念最好用示例來描述。 為了說明一些概念和功能，我們將演練[酒店評論示例](knowledge-store-connect-powerbi.md)技能集。 要在遵循導入資料工作流後查看技能集，您需要使用 REST API 用戶端[來獲取技能集](https://docs.microsoft.com/rest/api/searchservice/get-skillset)。

### <a name="enrichment-tree"></a>濃縮樹

為了設想技能集如何逐步豐富文檔，讓我們從文檔在任何豐富之前的外觀開始。 文檔開裂的輸出取決於資料來源和所選的特定分析模式。 這也是[欄位映射](search-indexer-field-mappings.md)在將資料添加到搜索索引時可以從中獲取內容的文檔狀態。
![管線圖表中的知識存放區](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "管線圖表中的知識存放區")

文檔在擴充管道中後，將表示為內容樹和相關富集樹。 此樹作為文檔開裂的輸出具現化。 擴充樹格式使擴充管道能夠將中繼資料附加到甚至原始資料類型，它不是有效的 JSON 物件，但可以投影為有效的 JSON 格式。 下表顯示了進入濃縮管道的文檔的狀態：

|資料來源\分析模式|預設|JSON， JSON 線& CSV|
|---|---|---|
|Blob 儲存體|/文檔/內容<br>/文檔/normalized_images/*<br>…|/文檔/{鍵1}<br>/文檔/{鍵 2}<br>…|
|SQL|/文檔/[列1]<br>/文檔/[列2]<br>…|N/A |
|Cosmos DB|/文檔/{鍵1}<br>/文檔/{鍵 2}<br>…|N/A|

 當技能執行時，它們會向富集樹添加新節點。 然後，這些新節點可用作下游技能、投影到知識存儲或映射到索引欄位的輸入。 富集不可修改：一旦創建，就無法編輯節點。 隨著技能集變得越來越複雜，您的富集樹也會變得更加複雜，但富集樹中的所有節點都需要將其添加到索引或知識存儲。 

可以有選擇地僅將富集的子集持久化到索引或知識存儲。
對於本文檔的其餘部分，我們將假設我們使用[酒店評論示例](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)，但相同的概念適用于從所有其他資料來源中豐富的文檔。

### <a name="context"></a>Context
每個技能都需要一個上下文。 上下文確定：
+   技能執行的次數，基於所選節點。 對於類型集合的上下文值，在末尾```/*```添加 a 將導致對集合中的每個實例調用一次技能。 
+   在富集樹中添加技能輸出的位置。 輸出始終作為上下文節點的子級添加到樹中。 
+   輸入的形狀。 對於多級集合，將上下文設置為父集合將影響技能的輸入形狀。 例如，如果您有一個包含國家/地區清單的富集樹，則每個國家/地區都包含包含郵遞區號清單的國家清單。

|Context|輸入|輸入形狀|技能調用|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |國家所有郵遞區號的清單 |每個國家/地區一次 |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |狀態中的郵遞區號清單 | 每個國家和國家組合一次|

### <a name="sourcecontext"></a>源上下文

`sourceContext`僅用於技能投入和[預測](knowledge-store-projection-overview.md)。 它用於構造多級嵌套物件。 您可能需要創建新物件，以便將其作為技能的輸入傳遞到知識存儲中。 由於擴充節點可能不是擴充樹中的有效 JSON 物件，並且引用樹中的節點僅在創建節點時返回該狀態，因此使用富集作為技能輸入或投影需要創建格式良好的 JSON 物件。 `sourceContext`使您能夠構造階層式匿名型別物件，如果只使用上下文，該物件將需要多種技能。 下`sourceContext`一節將顯示使用。 查看生成擴充的技能輸出，以確定它是否是有效的 JSON 物件，而不是基元類型。

### <a name="projections"></a>投影

投影是從富集樹中選擇要保存在知識存儲中的節點的過程。 投影是文檔的自訂形狀（內容和富集），可以作為表或物件投影輸出。 要瞭解有關使用投影的更多資訊，請參閱[使用投影](knowledge-store-projection-overview.md)。

![欄位映射選項](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "擴充管道的欄位映射選項")

上圖描述您根據富集管道中的位置使用的選擇器。

## <a name="generate-enriched-data"></a>生成豐富的資料 

現在，讓我們逐步瞭解酒店審核技能集，您可以按照[本教程](knowledge-store-connect-powerbi.md)創建技能集或[查看](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json)技能集。 我們將研究：

* 濃縮樹如何隨著每個技能的執行而演變 
* 上下文和輸入如何工作以確定技能執行的次數 
* 輸入的形狀基於上下文。 

由於我們使用索引子的分隔文本解析模式，因此擴充進程中的文檔表示 CSV 檔中的單個行。

### <a name="skill-1-split-skill"></a>技能#1：拆分技能 

![文檔開裂後的濃縮樹](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "文檔開裂後和技能執行前的濃縮樹")

使用 的 技能```"/document/reviews_text"```上下文，此技能將為 執行一`reviews_text`次。 技能輸出是一個清單，其中`reviews_text`將塊塊到 5000 個字元段。 拆分技能的輸出被命名`pages`並添加到富集樹中。 該`targetName`功能允許您在添加到富集樹之前重命名技能輸出。

富集樹現在具有一個新節點放在技能的上下文中。 此節點可用於任何技能、投影或輸出欄位映射。


所有富集的根節點為`"/document"`。 使用 blob 索引子時，`"/document"`節點將具有 和`"/document/content"``"/document/normalized_images"`的子節點。 使用 CSV 資料時，正如我們在此示例中一樣，列名稱將映射到 下面的`"/document"`節點。 要訪問通過技能添加到節點的任何富集，需要富集的完整路徑。 例如，如果要將節點中```pages```的文本用作其他技能的輸入，則需要將其指定為```"/document/reviews_text/pages/*"```。
 
 ![技能#1後濃縮樹](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "技能執行後#1濃縮樹")

### <a name="skill-2-language-detection"></a>技能#2語言檢測
 雖然語言檢測技能是技能集中定義的第三個（技能#3）技能，但它是下一個執行的技能。 由於它不會因需要任何輸入而阻塞，它將與以前的技能並存執行。 與它前面的拆分技能一樣，對於每個文檔，語言檢測技能也會調用一次。 擴充樹現在有一個新的語言節點。
 ![技能#2後濃縮樹](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "技能執行後#2濃縮樹")
 
 ### <a name="skill-3-key-phrases-skill"></a>技能#3：關鍵短語技能 

給定關鍵短語的```/document/reviews_text/pages/*```上下文，對於`pages`集合中的每個項，將調用一次技能。 技能的輸出將是關聯頁面元素下的節點。 

 現在，您應該能夠查看技能集中的其他技能，並視覺化富集樹將如何隨著每個技能的執行而繼續生長。 某些技能（如合併技能和掃描器技能）也會創建新節點，但僅使用現有節點的資料，並且不會創建淨新擴充。

![濃縮樹後所有技能](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "濃縮樹後所有技能")

上面樹中的連接器的顏色表示擴充是由不同的技能創建的，節點需要單獨定址，並且在選擇父節點時不會返回的物件的一部分。

## <a name="save-enrichments-in-a-knowledge-store"></a>在知識存儲中保存富集 

技能集還定義了知識存儲，其中豐富了文檔可以投影為表或物件。 要在知識庫中保存富集資料，請為富集文檔定義一組投影。 要瞭解有關知識存儲的詳細資訊，請參閱[知識商店概述](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>切片投影

定義表投影組時，可以將擴充樹中的單個節點分割到多個相關表中。 如果添加的表具有源路徑，該路徑是現有表投影的子節點，則生成的子節點將不是現有表投影的子節點，而是投影到新的相關表中。 這種切片技術允許您在 Shaper 技能中定義單個節點，該節點可以是所有表投影的源。 

### <a name="shaping-projections"></a>塑造投影

有兩種方法可以定義投影。 您可以使用掃描器技能創建新節點，該節點是要投影的所有擴充的根節點。 然後，在您的投影中，您只引用沙佩爾技能的輸出。 您還可以在投影定義本身內聯形狀投影。

沙波方法比內聯整形更詳細，但可確保濃縮樹的所有突變都包含在技能中，並且輸出是可以重用的物件。 內聯整形允許您創建所需的形狀，但是一個匿名物件，並且僅對為其定義的投影可用。 這些方法可以一起使用，也可以單獨使用。 在門戶工作流中為您創建的技能集包含這兩個技能集。 它使用用於表投影的沙線技能，但也使用內聯整形來投影關鍵短語表。

要擴展示例，可以選擇刪除內聯整形，並使用 shaper 技能為關鍵短語創建新節點。 要創建投影到三個表中的形狀，`hotelReviewsDocument`即 、`hotelReviewsPages`和`hotelReviewsKeyPhrases`， 下面各節將介紹這兩個選項。


#### <a name="shaper-skill-and-projection"></a>沙珀技能和投影 

> [!Note]
> 為了簡潔起見，文檔表中的某些列已從此示例中刪除。
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

使用上述`tableprojection``outputs`部分中定義的節點後，我們現在可以使用切片功能將`tableprojection`節點的某些部分投影到不同的表中：

> [!Note]
> 這只是知識存儲配置中投影片段。
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

#### <a name="inline-shaping-projections"></a>內聯整形投影

內聯整形方法不需要塑造器技能，因為投影所需的所有形狀都是在需要時創建的。 要投影與上一個示例相同的資料，內聯投影選項如下所示：

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
  
這兩種方法的一個觀察是如何使用 投影`"Keyphrases"`的值`"sourceContext"`。 包含`"Keyphrases"`字串集合的節點本身就是頁面文本的子節點。 但是，由於投影需要 JSON 物件，並且頁面是基元（字串），`"sourceContext"`因此使用 將鍵短語包裝到具有命名屬性的物件中。 此技術使甚至基元能夠獨立投影。

## <a name="next-steps"></a>後續步驟

作為下一步，創建具有認知技能的第一個技能集。

> [!div class="nextstepaction"]
> [創建您的第一個技能集](cognitive-search-defining-skillset.md)。
