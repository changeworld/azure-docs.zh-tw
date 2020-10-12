---
title: 將輸入對應至輸出欄位
titleSuffix: Azure Cognitive Search
description: 將源資料欄位解壓縮並擴充，並將其對應至 Azure 認知搜尋索引中的輸出欄位。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935359"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>如何將 AI 擴充的欄位對應至可搜尋的索引

![索引子階段](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "索引子階段")

在本文中，您可以了解如何將擴充的輸入欄位對應至可搜尋索引中的輸出欄位。 一旦具備[定義的技能集](cognitive-search-defining-skillset.md)，即必須將直接提供值之任何技能的輸出欄位，對應至搜尋索引中的指定欄位。

從擴充的檔將內容移至索引時，需要輸出欄位對應。  擴充的檔其實是一種資訊樹狀結構，即使在索引中支援複雜類型，有時候您可能會想要將擴充樹狀結構中的資訊轉換成更簡單的類型 (例如，字串的陣列) 。 輸出欄位對應可讓您依簡維資訊來執行資料圖形轉換。 輸出欄位對應一律會在技能集執行之後發生，不過即使沒有定義任何技能集，這個階段仍可能執行。

輸出欄位對應的範例：

* 在技能集的過程中，您已解壓縮檔中每個頁面所提及的組織名稱。 現在您想要將這些組織的每個名稱對應至 edm 類型之索引中的欄位， (Edm. 字串) 。

* 在技能集的過程中，您產生了名為 "document/translated_text" 的新節點。 您想要將這個節點上的資訊對應到索引中的特定欄位。

* 您沒有技能集，但正在從 Cosmos DB 資料庫編制複雜類型的索引。 您想要取得該複雜型別的節點，並將它對應至索引中的欄位。

> [!NOTE]
> 我們最近在輸出欄位對應上啟用了對應函式的功能。 如需對應函數的詳細資訊，請參閱[欄位對應](./search-indexer-field-mappings.md#field-mapping-functions)函式

## <a name="use-outputfieldmappings"></a>使用 outputFieldMappings

若要對應欄位，請將 `outputFieldMappings` 新增至您的索引子定義，如下所示：

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

要求主體已結構化，如下所示：

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

針對每個輸出欄位對應，在擴充的檔樹狀結構中設定資料的位置 (sourceFieldName) ，並在索引 (targetFieldName) 中參考的欄位名稱。

## <a name="flattening-information-from-complex-types"></a>複雜類型的簡維資訊 

sourceFieldName 中的路徑可以代表一個元素或多個元素。 在上述範例中，```/document/content/sentiment``` 代表單一數值，而 ```/document/content/organizations/*/description``` 代表數個組織描述。 

如果有數個元素，這些元素會「壓平合併」至包含每個元素的陣列。 

更具體地說，對於 ```/document/content/organizations/*/description``` 範例，[描述]** 欄位中的資料在進行編製索引之前，看起來像描述的平面陣列：

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

這是很重要的準則，因此我們將提供另一個範例。 假設您有一個複雜類型的陣列，做為擴充樹狀結構的一部分。 假設有一個名為 customEntities 的成員，其中有一個複雜類型的陣列，如下所述。

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

讓我們假設您的索引有一個名為 ' 疾病 ' 的欄位，其類型為 Collection () ，您想要在其中儲存每個實體的名稱。 

您可以使用 "" 符號來輕鬆完成此動作，如下所示 \* ：

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

這項作業只會將每個 customEntities 元素的名稱「簡維化」成單一字串陣列，如下所示：

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>接下來的步驟
一旦您將擴充欄位對應至可搜尋欄位，即可為每個可搜尋欄位將欄位屬性設定為[索引定義的一部分](search-what-is-an-index.md)。

如需欄位對應的詳細資訊，請參閱 [Azure 認知搜尋索引子中的欄位](search-indexer-field-mappings.md)對應。