---
title: 將輸入對應至輸出欄位
titleSuffix: Azure Cognitive Search
description: 將源資料欄位解壓縮並擴充，並對應至 Azure 認知搜尋索引中的輸出欄位。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c9b0b34202f35babcaa3dce37331d31edf641254
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85557259"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>如何將 AI 擴充的欄位對應至可搜尋的索引

在本文中，您可以了解如何將擴充的輸入欄位對應至可搜尋索引中的輸出欄位。 一旦具備[定義的技能集](cognitive-search-defining-skillset.md)，即必須將直接提供值之任何技能的輸出欄位，對應至搜尋索引中的指定欄位。 

將內容從擴充的檔移至索引時，需要輸出欄位對應。  擴充的檔其實是一種資訊的樹狀結構，即使索引中有複雜類型的支援，有時您可能會想要將資訊從擴充的樹狀結構轉換成更簡單的類型（例如，字串陣列）。 輸出欄位對應可讓您透過簡維資訊來執行資料圖形轉換。

> [!NOTE]
> 我們最近啟用了輸出欄位對應的對應函數功能。 如需對應函數的詳細資訊，請參閱[欄位對應函數](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions)

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

針對每個輸出欄位對應，設定擴充的檔樹狀結構（Sourcefieldname 路徑）中的資料位置，以及索引中所參考之欄位的名稱（targetFieldName）。

## <a name="flattening-information-from-complex-types"></a>從複雜類型簡維資訊 

sourceFieldName 中的路徑可以代表一個元素或多個元素。 在上述範例中，```/document/content/sentiment``` 代表單一數值，而 ```/document/content/organizations/*/description``` 代表數個組織描述。 

如果有數個元素，這些元素會「壓平合併」至包含每個元素的陣列。 

更具體地說，對於 ```/document/content/organizations/*/description``` 範例，[描述]** 欄位中的資料在進行編製索引之前，看起來像描述的平面陣列：

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

這是一項重要的原則，因此我們將提供另一個範例。 假設您在擴充樹狀結構中有複雜類型的陣列。 假設有一個名為 customEntities 的成員，其中包含複雜類型的陣列，如下所述。

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

讓我們假設您的索引有一個名為 ' 疾病 ' 的欄位，其類型為 Collection （Edm. String），您想要在其中儲存實體的每個名稱。 

這可以使用 " \* " 符號輕鬆完成，如下所示：

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

這項作業只會將 customEntities 元素的每個名稱「簡維」成單一字串陣列，如下所示：

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>後續步驟
一旦您將擴充欄位對應至可搜尋欄位，即可為每個可搜尋欄位將欄位屬性設定為[索引定義的一部分](search-what-is-an-index.md)。

如需欄位對應的詳細資訊，請參閱[Azure 認知搜尋索引子中的欄位](search-indexer-field-mappings.md)對應。
