---
title: 預測概念
titleSuffix: Azure Cognitive Search
description: 儲存 AI 擴充索引管線中的擴充資料，並將其塑造到知識存放區，以供全文檢索搜尋以外的案例使用。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 22db4f95bacd926208ac7edf3306cd136d81b00e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565185"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure 認知搜尋中的知識存放區「投影」

Azure 認知搜尋可讓您透過內建的認知技能和自訂技能來擴充內容，做為編制索引的一部分。 擴充會建立先前不存在的新資訊：從影像中解壓縮資訊、偵測情感、關鍵字組，以及從文字中找出實體等等。 擴充也會將結構新增至無差異文字。 所有這些程式都會產生檔，讓全文檢索搜尋更有效率。 在許多情況下，擴充的檔適用于搜尋以外的案例，例如知識發掘。

「[知識存放區](knowledge-store-concept-intro.md)」的一個元件是擴充檔的觀點，可以儲存至實體儲存體以供知識挖掘之用。 預測可讓您將資料「投射」到符合您需求的圖形，保留關聯性，讓 Power BI 之類的工具可以讀取資料，而不需要額外的工作。

投影可以是表格式，其中的資料會儲存在 Azure 資料表儲存體中的資料列和資料行，或儲存在 Azure Blob 儲存體中的 JSON 物件。 您可以在擴充資料時，定義多項資料的預測。 當您想要在個別使用案例中以不同方式塑造相同的資料時，多個投射會很有用。

知識存放區支援三種類型的預測：

+ **資料表**：對於最能以資料列和資料行表示的資料，資料表預測可讓您在資料表儲存體中定義架構化圖形或投射。 只有有效的 JSON 物件可以投射為數據表，擴充的檔可以包含未命名為 JSON 物件的節點，而且在投射這些物件時，請建立具有整形程式技能或內嵌成形的有效 JSON 物件。

+ **物件**：當您需要資料和擴充的 JSON 標記法時，物件投影會儲存為 blob。 只有有效的 JSON 物件可以投射為物件，擴充的檔可以包含不是命名為 JSON 物件的節點，而且在投射這些物件時，請建立具有整形程式技能或內嵌成形的有效 JSON 物件。

+ 檔案 **：當**您需要儲存從檔解壓縮的影像時，檔案投射可讓您將正規化映射儲存至 blob 儲存體。

若要查看在內容中定義的投影，請逐步執行[在 REST 中建立知識存放區](knowledge-store-create-rest.md)。

## <a name="projection-groups"></a>投射群組

在某些情況下，您必須將擴充的資料投影到不同的圖形，以符合不同的目標。 知識存放區可讓您定義多個投射群組。 投射群組具有相互獨佔性和關聯的下列重要特性。

### <a name="mutual-exclusivity"></a>相互獨佔性

投影到單一群組中的所有內容，都與投影至其他投射群組的資料無關。
這種獨立性意味著您可以讓相同的資料成形，但在每個投射群組中都有重複的方式。

### <a name="relatedness"></a>關聯

投射群組現在可讓您跨投射類型來投影檔，同時保留跨投影類型的關聯性。 在單一投射群組內投射的所有內容，會跨投影類型保留資料中的關聯性。 在資料表中，關聯性是以產生的索引鍵為基礎，而每個子節點都會保留父節點的參考。 跨類型（資料表、物件和檔案），跨不同類型投射單一節點時，會保留關聯性。 例如，假設您有一個包含影像和文字的檔。 您可以將文字投影至資料表或物件，並將影像放到資料表或物件具有包含檔案 URL 之資料行/屬性的檔案中。

## <a name="input-shaping"></a>輸入成形

取得正確的圖形或結構中的資料，是有效使用的關鍵，也就是資料表或物件。 能夠根據您計畫的存取和使用方式來塑造或結構化您的資料，這是在技能集中以**整形**者技能的形式公開的重要功能。  

當您在擴充樹狀結構中的物件符合投射的架構時，可以更輕鬆地定義投影。 更新的[整形程式技能](cognitive-search-skill-shaper.md)可讓您從擴充樹狀結構的不同節點撰寫物件，並將其父系在新節點底下。 「**整形**」技能可讓您定義具有嵌套物件的複雜類型。

當您定義了一個新的圖形，其中包含您所需的所有專案時，您現在可以使用此圖形做為投影的來源，或做為另一個技能的輸入。

## <a name="projection-slicing"></a>投射切割

定義投射群組時，擴充樹狀結構中的單一節點可以分割成多個相關的資料表或物件。 加入具有現有投射之子系之來源路徑的投影，會導致子節點從父節點切割，並投射到新的相關資料表或物件中。 這項技術可讓您在整形程式技能中定義單一節點，以作為所有投影的來源。

## <a name="table-projections"></a>資料表投射

因為它可讓匯入更容易，所以我們建議使用 Power BI 來探索資料的資料表。 此外，資料表投射允許變更資料表關聯性之間的基數。 

您可以將索引中的單一檔投影成多個資料表，並保留關聯性。 投影到多個資料表時，除非子節點是相同群組內另一個資料表的來源，否則整個圖形都會投射到每個資料表中。

### <a name="defining-a-table-projection"></a>定義資料表投射

在技能集的元素內定義資料表投影時 `knowledgeStore` ，請先將擴充樹狀目錄上的節點對應到資料表來源。 此節點通常是您加入至技能清單中，以產生您需要投影到資料表的特定圖形之**塑造技能的**輸出。 您選擇要加入至專案的節點可以切割成多個資料表。 資料表定義是您想要投影的資料表清單。

每個資料表都需要三個屬性：

+ tableName： Azure 儲存體中的資料表名稱。

+ generatedKeyName：唯一識別此資料列之索引鍵的資料行名稱。

+ 來源：從您的擴充來源的擴充樹狀結構中的節點。 此節點通常是整形者的輸出，但可能是任何技能的輸出。

以下是資料表投影的範例。

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

如本範例所示，主要片語和實體會模型化為不同的資料表，而且將包含每個資料列之父系（MainTable）的參考。

## <a name="object-projections"></a>物件投影

物件投射是擴充樹狀結構的 JSON 標記法，可以從任何節點來源。 在許多情況下，建立資料表投射的同一個**整形**程式技能可以用來產生物件投射。 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

產生物件投射需要一些特定物件屬性：

+ storageContainer：將儲存物件的 blob 容器
+ 來源：擴充樹狀結構節點的路徑，這是投射的根

## <a name="file-projection"></a>檔案投射

檔案投射類似物件投影，而且只對集合採取動作 `normalized_images` 。 與物件投射類似，檔案投影會儲存在 blob 容器中，其中資料夾前置詞為檔識別碼的 base64 編碼值。 檔案投射無法與物件投影共用相同的容器，而且必須投射到不同的容器中。

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>預測生命週期

您的預測有一個生命週期，系結至資料來源中的來源資料。 當您的資料更新並重新建立索引時，您的投影會以擴充的結果進行更新，確保您的預測最終會與資料來源中的資料一致。 投影會繼承您為索引設定的刪除原則。 刪除索引子或搜尋服務本身時，不會刪除投影。

## <a name="using-projections"></a>使用投影

執行索引子之後，您可以讀取透過投影指定的容器或資料表中的預測資料。

針對分析，Power BI 中的探索就像將 Azure 資料表儲存體設定為數據源一樣簡單。 您可以使用中的關聯性，輕鬆地在您的資料上建立一組視覺效果。

或者，如果您需要在資料科學管線中使用擴充的資料，您可以將[資料從 blob 載入至 Pandas 資料框架](../machine-learning/team-data-science-process/explore-data-blob.md)。

最後，如果您需要從知識存放區匯出資料，Azure Data Factory 有連接器可以匯出資料，並將其存放在您選擇的資料庫中。 

## <a name="next-steps"></a>後續步驟

在下一個步驟中，請使用範例資料和指示來建立您的第一個知識存放區。

> [!div class="nextstepaction"]
> [在 REST 中建立知識存放區](knowledge-store-create-rest.md)。

如需涵蓋配量、內嵌塑形及關聯性等進階投影概念的教學課程，請從[在知識存放區中定義投影](knowledge-store-projections-examples.md)開始

> [!div class="nextstepaction"]
> [定義知識存放區中的投影](knowledge-store-projections-examples.md)
