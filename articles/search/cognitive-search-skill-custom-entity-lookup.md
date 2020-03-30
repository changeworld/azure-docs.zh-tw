---
title: 自訂實體查找認知搜索技能
titleSuffix: Azure Cognitive Search
description: 從 Azure 認知搜索認知搜索管道中的文本中提取不同的自訂實體。 此技能當前處於公共預覽版中。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 8674438032ebd925296c95e9ffa0a2a0b95322f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369772"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>自訂實體查找認知技能（預覽版）

> [!IMPORTANT] 
> 此技能當前處於公共預覽版中。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 當前沒有門戶或 .NET SDK 支援。

**自訂實體查找**技能從自訂、使用者定義的單詞和片語清單中查找文本。 使用此清單，它將使用任何匹配的實體標記所有文檔。 該技能還支援一定程度的模糊比對，可用於查找相似但不精確的匹配。  

此技能不綁定到認知服務 API，可在預覽期間免費使用。 但是，您仍應[附加認知服務資源](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)，以覆蓋每日擴充限制。 每日限制適用于通過 Azure 認知搜索訪問時對認知服務的自由訪問。

## <a name="odatatype"></a>@odata.type  
微軟.技能.文本.自訂實體查找技能 

## <a name="data-limits"></a>資料限制
+ 支援的最大輸入記錄大小為 256 MB。 如果需要在將資料發送到自訂實體查找技能之前拆分資料，請考慮使用[文本拆分技能](cognitive-search-skill-textsplit.md)。
+ 如果使用*實體DefitionUri*參數提供，則支援的最大實體定義表為 10 MB。 
+ 如果內聯定義實體，則使用*內聯實體定義*參數，則支援的最大大小為 10 KB。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 參數名稱     | 描述 |
|--------------------|-------------|
| 實體 定義Uri    | JSON 或 CSV 檔的路徑，其中包含要匹配的所有目標文本。 此實體定義在索引子運行開始時讀取;在索引子運行開始時，將讀取此實體定義。在運行中，此檔的任何更新在後續運行之前不會實現。 此配置必須通過 HTTPS 訪問。 有關預期的 CSV 或 JSON 架構，請參閱下面的[自訂實體定義](#custom-entity-definition-format)格式。|
|內聯定義 | 內聯 JSON 實體定義。 如果存在，此參數將取代實體定義Uri 參數。 內聯配置不能超過 10 KB。 有關預期的 JSON 架構，請參閱下面的[自訂實體定義](#custom-entity-definition-format)。 |
|defaultLanguageCode |    （可選）用於標記和描述輸入文本的輸入文本的語言代碼。 支援以下語言： `da, de, en, es, fi, fr, it, ko, pt`. 預設值為英語 （）。`en` 如果您傳遞的是 languagecode-countrycode 格式，則只會使用該格式的 languagecode 部分。  |


## <a name="skill-inputs"></a>技能輸入

| 輸入名稱      | 描述                   |
|---------------|-------------------------------|
| text          | 要分析的文字。          |
| languageCode    | 選擇性。 預設值為 `"en"`。  |


## <a name="skill-outputs"></a>技能輸出


| 輸出名稱      | 描述                   |
|---------------|-------------------------------|
| 實體 | 包含有關找到的匹配項的資訊和相關中繼資料的物件陣列。 標識的每個實體可能包含以下欄位：  <ul> <li> *名稱*： 標識的頂級實體。 實體表示"正常化"表單。 </li> <li> *id*：使用者在"自訂實體定義格式"中定義的實體的唯一識別碼。</li> <li> *說明*：使用者在"自訂實體定義格式"中定義的實體描述。 </li> <li> *類型：* 由使用者在"自訂實體定義格式"中定義的實體類型。</li> <li> *子類型：* 由使用者在"自訂實體定義格式"中定義的實體子類型。</li>  <li> *匹配*： 描述源文本上該實體的每個匹配項的集合。 每個匹配將具有以下成員： </li> <ul> <li> *文本*： 原始文本與來源文件匹配。 </li> <li> *偏移*：在文本中找到匹配的位置。 </li> <li> *長度*：匹配文本的長度。 </li> <li> *匹配距離*：此匹配的字元數與原始機構名稱或別名不同。  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>自訂實體定義格式

有 3 種不同的方法向自訂實體查找技能提供自訂實體的清單。 您可以在 中提供清單。CSV 檔， .JSON 檔或作為技能定義的內聯定義。  

如果定義檔為 。CSV 或 。JSON 檔，檔的路徑需要作為*實體DefitionUri*參數的一部分提供。 在這種情況下，該檔在每個索引子運行開始時下載一次。 只要索引子打算運行，該檔必須是可訪問的。 此外，檔必須編碼 UTF-8。

如果定義是內聯提供的，則應與*內聯身份定義*技能參數的內容一樣提供內聯。 

### <a name="csv-format"></a>CSV 格式

通過提供檔的路徑並將其設置在*實體DefitionUri*技能參數中，可以提供要在 Comma 分隔值 （CSV） 檔中查找的自訂實體的定義。 路徑應位於 HTTPs 位置。 定義檔的大小可達 10 MB。

CSV 格式很簡單。 每行表示一個唯一實體，如下所示：

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

在這種情況下，有三個實體可以作為找到的實體（比爾·蓋茨、薩蒂亞·納德拉、微軟）返回，但如果文本上匹配行中的任何術語（別名），則將標識它們。 例如，如果在文檔中找到字串"威廉·蓋茨"，則返回"比爾·蓋茨"實體的匹配項。

### <a name="json-format"></a>JSON 格式

也可以提供要在 JSON 檔中查找的自訂實體的定義。 JSON 格式為您提供了更大的靈活性，因為它允許您定義每個術語的匹配規則。 例如，您可以為每個術語指定模糊比對距離（Damerau-Levenshtein 距離），或者匹配是否應區分大小寫。 

 與 CSV 檔一樣，您需要提供 JSON 檔的路徑，並將其設置在*實體DefitionUri*技能參數中。 路徑應位於 HTTPs 位置。 定義檔的大小可達 10 MB。

最基本的 JSON 自訂實體清單定義可以是要匹配的實體清單：

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

JSON 定義的更複雜的示例可以選擇提供每個實體的 ID、描述、類型和子類型 ，以及其他*別名*。 如果匹配別名術語，則實體也將返回：

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

下表詳細介紹了在定義要匹配的實體時可以設置的不同配置參數：

|  欄位名稱  |        描述  |
|--------------|----------------------|
| NAME | 頂級實體描述項。 技能輸出中的匹配項將按此名稱分組，並且它應表示正在找到的文本的"正常化"形式。  |
| description  | （可選）此欄位可用作有關匹配文本的自訂中繼資料的傳遞。 此欄位的值將與其實體在技能輸出中的每個匹配項一起顯示。 |
| type | （可選）此欄位可用作有關匹配文本的自訂中繼資料的傳遞。 此欄位的值將與其實體在技能輸出中的每個匹配項一起顯示。 |
| subtype | （可選）此欄位可用作有關匹配文本的自訂中繼資料的傳遞。 此欄位的值將與其實體在技能輸出中的每個匹配項一起顯示。 |
| id | （可選）此欄位可用作有關匹配文本的自訂中繼資料的傳遞。 此欄位的值將與其實體在技能輸出中的每個匹配項一起顯示。 |
| 區分大小寫 | （可選）預設值為 false。 布林值表示與機構名稱的比較是否應對字元大小寫敏感。 "微軟"的樣本大小寫不敏感匹配可以是：微軟、微軟、微軟 |
| 模糊編輯距離 | （可選）預設值為 0。 最大值為 5。 表示仍構成與機構名稱匹配的可接受的發散字元數。 返回任何給定匹配中盡可能小的模糊性。  例如，如果編輯距離設置為 3，"Windows 10"仍將匹配"Windows"、"Windows10"和"視窗 7"。 <br/> 當區分大小寫設置為 false 時，大小寫差異不會計入模糊容差，但並非如此。 |
| 預設事件敏感 | （可選）更改此實體的預設區分大小寫值。 它用於更改所有別名的預設值區分值。 |
| 預設模糊編輯距離 | （可選）更改此實體的預設模糊編輯距離值。 它可用於更改所有別名模糊編輯距離值的預設值。 |
| 別名 | （可選）可用於指定根機構名稱的替代拼寫或同義字的複雜物件的陣列。 |

| 別名屬性 | 描述 |
|------------------|-------------|
| text  | 某些目標機構名稱的替代拼寫或表示形式。  |
| 區分大小寫 | （可選）行為與上面的根實體"區分"參數相同，但僅適用于此別名。 |
| 模糊編輯距離 | （可選）作用與上面的根實體"模糊編輯距離"參數相同，但僅適用于此別名。 |


### <a name="inline-format"></a>內聯格式

在某些情況下，提供自訂實體清單以直接匹配到技能定義中可能更方便。 在這種情況下，您可以使用與上述格式類似的 JSON 格式，但它在技能定義中內聯。
只能內聯定義大小小於 10 KB（序列化大小）的配置。 

##    <a name="sample-definition"></a>範例定義

使用內聯格式的示例技能定義如下所示：

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
或者，如果您決定提供指向實體定義檔的指標，則下面將顯示使用實體定義庫格式的示例技能定義：

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>範例輸入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>範例輸出

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>錯誤和警告

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>警告： 達到匹配的最大容量，跳過所有進一步的重複匹配項。

如果檢測到的匹配數大於允許的最大值，將發出此警告。 在這種情況下，我們將停止包括重複的匹配項。 如果您不能接受，請提交[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)，以便我們説明您處理您的個人使用案例。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [實體識別技能（搜索已知實體）](cognitive-search-skill-entity-recognition.md)
