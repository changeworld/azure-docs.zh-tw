---
title: 投射概念
titleSuffix: Azure Cognitive Search
description: 將您的擴充資料從 AI 擴充索引管線儲存並塑造到知識存放區，以供全文檢索搜尋以外的案例使用。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 22db4f95bacd926208ac7edf3306cd136d81b00e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85565185"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure 認知搜尋中的知識存放區「投射」

Azure 認知搜尋可透過內建的認知技能和自訂技能，在編制索引過程中擴充內容。 擴充建立先前未存在的新資訊：從影像中解壓縮資訊、從文字偵測情感、關鍵字組和實體等等。 擴充也會將結構新增至無差異文字。 所有這些程式都會產生可讓全文檢索搜尋更有效率的檔。 在許多情況下，擴充的檔適用于搜尋以外的案例，例如知識挖掘。

[知識存放區](knowledge-store-concept-intro.md)的投射是擴充檔的視圖，可儲存至實體儲存體以供知識挖掘之用。 投射可讓您將資料「投射」成符合您需求的圖形，保留關聯性，讓 Power BI 之類的工具可以讀取資料，而不需要額外投入。

投影可以是表格式，其中的資料會儲存在 Azure 資料表儲存體中的資料列和資料行中，或儲存在 Azure Blob 儲存體中的 JSON 物件。 您可以定義資料在擴充時的多個預測。 當您想要讓個別使用案例的資料形成不同時，多個投射會很有用。

知識存放區支援三種類型的投射：

+ **資料表**：對於最能表示為數據列和資料行的資料，資料表投射可讓您定義資料表儲存體中的架構化圖形或投影。 只有有效的 JSON 物件可以投射為數據表，擴充的檔可以包含未命名為 JSON 物件的節點，而在投射這些物件時，請使用造型技能或內嵌成形來建立有效的 JSON 物件。

+ **物件**：當您需要資料和擴充的 JSON 標記法時，會將物件投影儲存為 blob。 只有有效的 JSON 物件可以投射為物件，擴充的檔可以包含未命名為 JSON 物件的節點，並在投影這些物件時，使用造型技能或內嵌成形來建立有效的 JSON 物件。

+ 檔案：當您需要儲存**從檔解壓縮**的影像時，檔案投影可讓您將正規化的影像儲存至 blob 儲存體。

若要查看在內容中定義的投射，請逐步 [建立 REST 中的知識存放區](knowledge-store-create-rest.md)。

## <a name="projection-groups"></a>投射群組

在某些情況下，您必須在不同的圖形中投影擴充的資料，以符合不同的目標。 知識存放區可讓您定義多個投射群組。 投射群組具有下列相互獨佔性和關聯的主要特性。

### <a name="mutual-exclusivity"></a>相互獨佔性

所有投射到單一群組的內容都與投射到其他投射群組的資料無關。
這種獨立性表示您可以使用不同的方式來形成相同的資料，但在每個投射群組中都是重複的。

### <a name="relatedness"></a>關聯

投射群組現在可讓您在投影類型之間投影檔，同時保留投影類型之間的關聯性。 投射在單一投射群組內的所有內容都會保留資料在投影類型之間的關聯性。 在資料表中，關聯性是以產生的索引鍵為基礎，而每個子節點會保留父節點的參考。 跨類型 (資料表、物件和檔案) ，當單一節點投射到不同類型時，就會保留關聯性。 例如，假設您有一個包含影像和文字的檔。 您可以將文字投射到資料表或物件，並將影像投影至檔案，其中資料表或物件的資料行/屬性包含檔案 URL。

## <a name="input-shaping"></a>輸入成形

在正確的圖形或結構中取得您的資料是有效使用的關鍵，也就是資料表或物件。 根據您計畫存取和使用資料的方式來塑造或結構資料的能力，是在技能集中公開為塑造器 **技能的** 主要功能。  

當您在擴充樹狀結構中的物件符合投影的架構時，可以更輕鬆地定義投影。 更新的 [整形技能](cognitive-search-skill-shaper.md) 可讓您從擴充樹狀結構的不同節點撰寫物件，並將其父代在新節點下。 **整形**人員技能可讓您使用嵌套物件來定義複雜類型。

當您定義的新圖形包含您需要投影的所有專案時，您現在可以使用此圖形作為投影的來源或作為其他技能的輸入。

## <a name="projection-slicing"></a>投射切割

定義投射群組時，擴充樹狀結構中的單一節點可以分割成多個相關的資料表或物件。 加入具有現有投射之子系來源路徑的投射，會導致子節點從父節點切割出來，並投射至新的關聯資料表或物件。 這項技術可讓您在整形程式技能中定義單一節點，以作為所有投影的來源。

## <a name="table-projections"></a>資料表投影

因為它可讓匯入變得更容易，我們建議使用 Power BI 進行資料探索的資料表預測。 此外，資料表投射還可讓您變更資料表關聯性之間的基數。 

您可以將索引中的單一檔投影至多個資料表，並保留關聯性。 投影至多個資料表時，除非子節點是相同群組內另一個資料表的來源，否則整個圖形都會投射到每個資料表中。

### <a name="defining-a-table-projection"></a>定義資料表投射

在技能集的元素內定義資料表投射時 `knowledgeStore` ，首先要將擴充樹狀結構上的節點對應至資料表來源。 一般而言，此節點是您加入至技能清單中，以產生您需要投影至資料表之特定圖形的 **整形** 器技能輸出。 您選擇要投影的節點可以分割成多個資料表。 資料表定義是您想要投影的資料表清單。

每個資料表都需要三個屬性：

+ tableName： Azure 儲存體中的資料表名稱。

+ generatedKeyName：可唯一識別此資料列之索引鍵的資料行名稱。

+ 來源：您的擴充來源擴充樹狀結構中的節點。 此節點通常是整形程式的輸出，但可以是任何技能的輸出。

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

如本範例所示，主要片語和實體會模型化為不同的資料表，且將包含每個資料列的父 (MainTable) 的參考。

## <a name="object-projections"></a>物件投影

物件投射是擴充樹狀結構的 JSON 標記法，可從任何節點來源。 在許多情況下，建立資料表投射的相同 **整形** 程式技能可以用來產生物件投射。 

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

產生物件投射需要一些特定物件的屬性：

+ storageContainer：將儲存物件的 blob 容器
+ 來源：擴充樹狀結構的節點路徑，也就是投射的根目錄

## <a name="file-projection"></a>檔案投射

檔案投影與物件投影類似，而且只對集合採取行動 `normalized_images` 。 與物件投影類似，檔案投影會儲存在 blob 容器中，並以檔識別碼的 base64 編碼值的資料夾前置詞。 檔案投射無法與物件投射共用相同的容器，必須投射到不同的容器。

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

## <a name="projection-lifecycle"></a>投射生命週期

您的投射具有與資料來源中的來源資料系結的生命週期。 當您的資料更新並重新建立索引時，您的投射會以擴充的結果更新，以確保您的預測最終與資料來源中的資料一致。 預測會繼承您為索引設定的刪除原則。 刪除索引子或搜尋服務本身時，不會刪除投影。

## <a name="using-projections"></a>使用投影

執行索引子之後，您可以在透過投影指定的容器或資料表中讀取投射的資料。

針對分析，Power BI 中的探索就像是將 Azure 資料表儲存體設定為數據源一樣簡單。 您可以使用中的關聯性，輕鬆地在您的資料上建立一組視覺效果。

或者，如果您需要在資料科學管線中使用擴充的資料，您可以將 [資料從 blob 載入至 Pandas 資料框架](../machine-learning/team-data-science-process/explore-data-blob.md)。

最後，如果您需要從知識存放區匯出資料，Azure Data Factory 具有可匯出資料的連接器，並將資料存放在您選擇的資料庫中。 

## <a name="next-steps"></a>接下來的步驟

在下一個步驟中，請使用範例資料和指示來建立您的第一個知識存放區。

> [!div class="nextstepaction"]
> [建立 REST 中的知識存放區](knowledge-store-create-rest.md)。

如需涵蓋配量、內嵌塑形及關聯性等進階投影概念的教學課程，請從[在知識存放區中定義投影](knowledge-store-projections-examples.md)開始

> [!div class="nextstepaction"]
> [定義知識存放區中的投影](knowledge-store-projections-examples.md)
