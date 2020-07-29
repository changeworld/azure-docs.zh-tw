---
title: 自訂實體查閱認知搜尋技能
titleSuffix: Azure Cognitive Search
description: 從 Azure 認知搜尋認知搜尋管線中的文字解壓縮不同的自訂實體。 此技能目前為公開預覽狀態。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 00192ab3663944908f282f601396651cdd319df2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987475"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>自訂實體查閱認知技能（預覽）

> [!IMPORTANT] 
> 此技能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前沒有入口網站或 .NET SDK 支援。

**自訂實體查閱**技能會從自訂、使用者定義的單字和片語清單中尋找文字。 使用這份清單，其會以任何相符的實體標記所有文件。 此技能也支援某種程度的模糊比對，可加以套用以尋找類似但不完全精確的相符項目。  

此技能不會系結至認知服務 API，且在預覽期間可免費使用。 不過，您仍應[附加認知服務資源](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)，以覆寫每日擴充限制。 透過 Azure 認知搜尋存取時，每日限制適用于認知服務的免費存取。

## <a name="odatatype"></a>@odata.type  
CustomEntityLookupSkill。 

## <a name="data-limits"></a>資料限制
+ 支援的輸入記錄大小上限為 256 MB。 如果您需要先分割資料，再將它傳送至自訂實體查閱技能，請考慮使用[文字分割技能](cognitive-search-skill-textsplit.md)。
+ 如果使用*entitiesDefinitionUri*參數來提供，則支援的最大實體定義資料表為 10 MB。 
+ 如果實體是以內嵌方式定義，則使用*inlineEntitiesDefinition*參數，支援的大小上限為 10 KB。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 參數名稱     | 描述 |
|--------------------|-------------|
| `entitiesDefinitionUri`    | JSON 或 CSV 檔案的路徑，其中包含要比對的所有目標文字。 此實體定義會在索引子執行的開頭讀取;在後續執行之前，將不會實現對此檔案執行的任何更新。 此設定必須可透過 HTTPS 存取。 請參閱下方的[自訂實體定義](#custom-entity-definition-format)格式，以取得預期的 CSV 或 JSON 架構。|
|`inlineEntitiesDefinition` | 內嵌 JSON 實體定義。 此參數會取代 entitiesDefinitionUri 參數（如果有的話）。 不能以內嵌方式提供超過 10 KB 的設定。 請參閱下方的[自訂實體定義](#custom-entity-definition-format)，以取得預期的 JSON 架構。 |
|`defaultLanguageCode` |    選擇性輸入文字的語言代碼，用來 token 化和描繪輸入文字。 支援下列語言： `da, de, en, es, fi, fr, it, ko, pt` 。 預設值為 [英文] （ `en` ）。 如果您傳遞的是 languagecode-countrycode 格式，則只會使用該格式的 languagecode 部分。  |


## <a name="skill-inputs"></a>技能輸入

| 輸入名稱      | Description                   |
|---------------|-------------------------------|
| `text`          | 要分析的文字。          |
| `languageCode`    | 選擇性。 預設值為 `"en"`。  |


## <a name="skill-outputs"></a>技能輸出


| 輸出名稱      | Description                   |
|---------------|-------------------------------|
| `entities` | 物件的陣列，其中包含找到之相符專案的相關資訊，以及相關的中繼資料。 識別的每個實體都可能包含下欄欄位：  <ul> <li> *名稱*：識別的最上層實體。 實體代表「正規化」表單。 </li> <li> *識別碼：使用者*在「自訂實體定義格式」中所定義之實體的唯一識別碼。</li> <li> *描述*：使用者在「自訂實體定義格式」中所定義的實體描述。 </li> <li> *類型：* 使用者在「自訂實體定義格式」中所定義的實體類型。</li> <li> *子類型：* 實體子類型，如使用者在「自訂實體定義格式」中所定義。</li>  <li> *符合*：描述來源文字上該實體之每個相符專案的集合。 每個相符項都將具有下列成員： </li> <ul> <li> *text*：來源文件中的原始文字相符。 </li> <li> *offset*：在文字中找到相符項的位置。 </li> <li> *長度*：相符文字的長度。 </li> <li> *matchDistance*：此比對與原始機構名稱或別名不同的字元數。  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>自訂實體定義格式

有3種不同的方式可將自訂實體清單提供給自訂實體查閱技能。 您可以在中提供清單。CSV 檔案，a。JSON 檔案或做為技能定義之一部分的內嵌定義。  

如果定義檔為，則為。CSV 或。JSON 檔案，必須提供檔案的路徑做為*entitiesDefinitionUri*參數的一部分。 在此情況下，檔案會在每個索引子執行開始時下載一次。 只要索引子打算執行，就必須能夠存取檔案。 此外，檔案必須以 UTF-8 編碼。

如果以內嵌方式提供定義，則應該將其當做內嵌提供，做為*inlineEntitiesDefinition*技能參數的內容。 

### <a name="csv-format"></a>CSV 格式

藉由提供檔案的路徑並在*entitiesDefinitionUri*技能參數中設定，您可以提供自訂實體的定義，以尋找逗號分隔值（CSV）檔案中的檔案。 路徑應位於 HTTPs 位置。 定義檔的大小最多可達 10 MB。

CSV 格式很簡單。 每一行代表一個唯一的實體，如下所示：

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

在此情況下，有三個實體可在找到的實體（帳單閘道、Satya Nadella、Microsoft）中傳回，但如果該行（別名）上的任何詞彙符合文字，則會加以識別。 例如，如果在檔中找到字串 "William h.264"，則會傳回「帳單閘道」實體的相符專案。

### <a name="json-format"></a>JSON 格式

您也可以提供自訂實體的定義，以便在 JSON 檔案中尋找。 JSON 格式提供更多的彈性，因為它可讓您定義每個詞彙的比對規則。 例如，您可以指定每個詞彙的模糊比對距離（Damerau-levenshtein-Levenshtein 距離），或比對是否應區分大小寫。 

 就像 CSV 檔案一樣，您必須提供 JSON 檔案的路徑，並在*entitiesDefinitionUri*技能參數中加以設定。 路徑應位於 HTTPs 位置。 定義檔的大小最多可達 10 MB。

最基本的 JSON 自訂實體清單定義可以是要符合的實體清單：

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

JSON 定義的更複雜範例可以選擇性地提供每個實體的識別碼、描述、類型和子類型，以及其他*別名*。 如果別名字詞相符，實體也會傳回：

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

下表詳細說明您在定義要比對的實體時可設定的不同設定參數：

|  欄位名稱  |        Description  |
|--------------|----------------------|
| `name` | 最上層實體描述元。 技能輸出中的相符專案會依此名稱分組，而且應該代表所要尋找之文字的「正規化」形式。  |
| `description`  | 選擇性此欄位可以用來當做符合的文字之自訂中繼資料的傳遞。 此欄位的值會顯示在技能輸出中每個符合其實體的專案。 |
| `type` | 選擇性此欄位可以用來當做符合的文字之自訂中繼資料的傳遞。 此欄位的值會顯示在技能輸出中每個符合其實體的專案。 |
| `subtype` | 選擇性此欄位可以用來當做符合的文字之自訂中繼資料的傳遞。 此欄位的值會顯示在技能輸出中每個符合其實體的專案。 |
| `id` | 選擇性此欄位可以用來當做符合的文字之自訂中繼資料的傳遞。 此欄位的值會顯示在技能輸出中每個符合其實體的專案。 |
| `caseSensitive` | 選擇性預設值為 false。 布林值，表示與機構名稱的比較是否應區分字元大小寫。 "Microsoft" 不區分大小寫的範例可能是： microsoft，microsoft，microsoft |
| `fuzzyEditDistance` | 選擇性預設為0。 最大值為5。 表示可接受的分歧字元數，仍然會構成與機構名稱相符的專案。 會傳回任何指定之相符的最小可能值。  比方說，如果 [編輯距離] 設定為3，"Windows 10" 仍會符合 "Windows"、"Windows10" 和 "windows 7"。 <br/> 當區分大小寫設定為 false 時，不會將大小寫差異計入「不確定」容錯的範圍，但也會執行。 |
| `defaultCaseSensitive` | 選擇性變更此實體的預設區分大小寫值。 它可用來變更所有別名 caseSensitive 值的預設值。 |
| `defaultFuzzyEditDistance` | 選擇性變更此實體的預設模糊編輯距離值。 它可以用來變更所有別名 fuzzyEditDistance 值的預設值。 |
| `aliases` | 選擇性複雜物件的陣列，可以用來指定根機構名稱的替代拼寫或同義字。 |

| 別名屬性 | Description |
|------------------|-------------|
| `text`  | 某些目標機構名稱的替代拼寫或標記法。  |
| `caseSensitive` | 選擇性作用與上述根實體 "caseSensitive" 參數相同，但僅適用于這個別名。 |
| `fuzzyEditDistance` | 選擇性作用與上述根實體 "fuzzyEditDistance" 參數相同，但僅適用于這個別名。 |


### <a name="inline-format"></a>內嵌格式

在某些情況下，提供自訂實體清單以直接對應至技能定義可能會比較方便。 在此情況下，您可以使用類似上述的 JSON 格式，但它會在技能定義中內嵌。
只有小於 10 KB 大小（序列化大小）的設定可以用內嵌方式定義。 

##    <a name="sample-definition"></a>範例定義

使用內嵌格式的範例技能定義如下所示：

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
        "type": "Hardware",
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
或者，如果您決定提供實體定義檔的指標，則使用格式的範例技能定義如下 `entitiesDefinitionUri` 所示：

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
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
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

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>警告：已達到相符專案的最大容量，略過所有其他重複的相符專案。

如果偵測到的相符專案數目大於允許的最大值，就會發出此警告。 在此情況下，我們將停止包含重複的相符專案。 如果您無法接受這種情況，請提出[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)，讓我們可以協助您使用個別的使用案例。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [實體辨識技能（用來搜尋已知的實體）](cognitive-search-skill-entity-recognition.md)
