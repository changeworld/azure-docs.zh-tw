---
title: 將輸入映射到輸出欄位
titleSuffix: Azure Cognitive Search
description: 提取和豐富源資料欄位，並映射到 Azure 認知搜索索引中的輸出欄位。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280972"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>如何將 AI 豐富的欄位映射到可搜索索引

在本文中，您可以了解如何將擴充的輸入欄位對應至可搜尋索引中的輸出欄位。 一旦具備[定義的技能集](cognitive-search-defining-skillset.md)，即必須將直接提供值之任何技能的輸出欄位，對應至搜尋索引中的指定欄位。 

將內容從富集文檔移動到索引中需要輸出欄位映射。  富集文檔實際上是一個資訊樹，即使索引中支援複雜類型，有時您可能希望將資訊從富集樹轉換為更簡單的類型（例如，字串陣列）。 輸出欄位映射允許您通過拼平資訊來執行資料形狀轉換。

## <a name="use-outputfieldmappings"></a>使用 outputFieldMappings
若要對應欄位，請將 `outputFieldMappings` 新增至您的索引子定義，如下所示：

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
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
            "targetFieldName": "descriptions"
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

對於每個輸出欄位映射，設置富集文檔樹（sourceFieldName）中資料的位置，以及索引（目標欄位名稱）中引用的欄位的名稱。

## <a name="flattening-information-from-complex-types"></a>從複雜類型拼合資訊 

sourceFieldName 中的路徑可以代表一個元素或多個元素。 在上述範例中，```/document/content/sentiment``` 代表單一數值，而 ```/document/content/organizations/*/description``` 代表數個組織描述。 

如果有數個元素，這些元素會「壓平合併」至包含每個元素的陣列。 

更具體地說，對於 ```/document/content/organizations/*/description``` 範例，[描述]** 欄位中的資料在進行編製索引之前，看起來像描述的平面陣列：

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

這是一個重要的原則，所以我們將提供另一個例子。 假設您有一個複雜類型的陣列作為擴充樹的一部分。 假設有一個稱為 custom實體的成員具有類似下面描述的複雜類型的陣列。

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

假設索引具有一個稱為"疾病"類型的"疾病"的欄位集合 （Edm.String），您希望在其中存儲實體的每個名稱。 

這可以通過使用""\*符號輕鬆完成，如下所示：

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

此操作將簡單地將自訂實體元素的每個名稱"拼平"到單個字串陣列中，如下所示：

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>後續步驟
一旦您將擴充欄位對應至可搜尋欄位，即可為每個可搜尋欄位將欄位屬性設定為[索引定義的一部分](search-what-is-an-index.md)。

有關欄位映射的詳細資訊，請參閱[Azure 認知搜索索引子中的欄位映射](search-indexer-field-mappings.md)。
