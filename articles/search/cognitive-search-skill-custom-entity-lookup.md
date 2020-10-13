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
ms.openlocfilehash: 5511551f240fe4fdd2f2aa3bc8a3a2615505f35f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936107"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>自訂實體查閱認知技能 (預覽) 

> [!IMPORTANT] 
> 此技能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前沒有入口網站或 .NET SDK 支援。

**自訂實體查閱**技能會從自訂、使用者定義的單字和片語清單中尋找文字。 使用這份清單，其會以任何相符的實體標記所有文件。 此技能也支援某種程度的模糊比對，可加以套用以尋找類似但不完全精確的相符項目。  

這項技能未系結至認知服務 API，在預覽期間可免費使用。 不過，您仍然應該 [附加認知服務資源](./cognitive-search-attach-cognitive-services.md)，以覆寫每日擴充限制。 每日限制適用于透過 Azure 認知搜尋存取認知服務的免費存取。

## <a name="odatatype"></a>@odata.type  
CustomEntityLookupSkill。 

## <a name="data-limits"></a>資料限制
+ 支援的輸入記錄大小上限為 256 MB。 如果您需要先分割資料，再將其傳送至自訂實體查閱技能，請考慮使用 [文字分割技能](cognitive-search-skill-textsplit.md)。
+ 如果使用 *entitiesDefinitionUri* 參數提供，支援的最大實體定義資料表為 10 MB。 
+ 如果實體是以內嵌方式定義，則使用 *inlineEntitiesDefinition* 參數，支援的大小上限為 10 KB。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 參數名稱     | 描述 |
|--------------------|-------------|
| `entitiesDefinitionUri`    | JSON 或 CSV 檔案的路徑，其中包含要符合的所有目標文字。 此實體定義會在索引子執行的開頭讀取;在後續執行之前，將不會實現此檔案的任何更新。 此設定必須可透過 HTTPS 存取。 請參閱下方的 [自訂實體定義](#custom-entity-definition-format) 格式 "，以瞭解預期的 CSV 或 JSON 架構。|
|`inlineEntitiesDefinition` | 內嵌 JSON 實體定義。 此參數會取代 entitiesDefinitionUri 參數（如果有的話）。 內嵌最多可提供 10 KB 的設定。 請參閱下方的 [自訂實體定義](#custom-entity-definition-format) ，以取得預期的 JSON 架構。 |
|`defaultLanguageCode` |     (用來 token 化和描繪輸入文字之輸入文字的選擇性) 語言代碼。 支援下列語言： `da, de, en, es, fi, fr, it, ko, pt` 。 預設值是英文 (`en`) 。 如果您傳遞的是 languagecode-countrycode 格式，則只會使用該格式的 languagecode 部分。  |


## <a name="skill-inputs"></a>技能輸入

| 輸入名稱      | 說明                   |
|---------------|-------------------------------|
| `text`          | 要分析的文字。          |
| `languageCode`    | 選擇性。 預設值為 `"en"`。  |


## <a name="skill-outputs"></a>技能輸出


| 輸出名稱      | 說明                   |
|---------------|-------------------------------|
| `entities` | 物件的陣列，其中包含找到之相符專案的相關資訊，以及相關的中繼資料。 每個識別的實體可能包含下欄欄位：  <ul> <li> *名稱*：已識別的最上層實體。 實體代表「正規化」表單。 </li> <li> *識別碼*：實體的唯一識別碼，由使用者以「自訂實體定義格式」定義。</li> <li> *描述*：使用者以「自訂實體定義格式」定義的實體描述。 </li> <li> *輸入：* 由使用者以「自訂實體定義格式」定義的實體類型。</li> <li> *子類型：* 由使用者以「自訂實體定義格式」定義的實體子類型。</li>  <li> *符合*：描述來源文字上該實體之每個相符專案的集合。 每個相符項都會有下列成員： </li> <ul> <li> *text*：來源文件中的原始文字相符。 </li> <li> *offset*：在文字中找到相符項的位置。 </li> <li> *長度*：相符文字的長度。 </li> <li> *matchDistance*：這個相符的字元數與原始機構名稱或別名不同。  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>自訂實體定義格式

有3種不同的方式可提供自訂實體查閱技能的自訂實體清單。 您可以在中提供清單。CSV 檔案，a。JSON 檔案或作為技能定義一部分的內嵌定義。  

如果定義檔是，則為。CSV 或。JSON 檔案時，必須提供檔案的路徑作為 *entitiesDefinitionUri* 參數的一部分。 在此情況下，會在每次執行索引子的開頭一次下載檔案。 只要索引子要執行，檔案就必須可以存取。 此外，檔案必須編碼為 UTF-8。

如果定義是以內嵌方式提供，則應該以內嵌方式提供，做為 *inlineEntitiesDefinition* 技能參數的內容。 

### <a name="csv-format"></a>CSV 格式

您可以提供檔案路徑，並在 *entitiesDefinitionUri*  技能參數中進行設定，以提供自訂實體的定義，以在 Comma-Separated 值 (CSV) 檔中尋找。 路徑應位於 HTTPs 位置。 定義檔大小最多可達 10 MB。

CSV 格式很簡單。 每一行代表一個唯一的實體，如下所示：

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

在此情況下，有三個實體可傳回為 (帳單閘道、Satya Nadella、Microsoft) 所找到的實體，但如果該行上的任何字詞 (別名) 符合文字，則會加以識別。 比方說，如果在檔中找到 "William h." 字串，就會傳回「帳單閘道」實體的相符項。

### <a name="json-format"></a>JSON 格式

您也可以提供自訂實體的定義，以在 JSON 檔案中尋找。 JSON 格式可提供您更多的彈性，因為它可讓您定義每個詞彙的比對規則。 例如，您可以指定每個詞彙的模糊比對距離)  (Damerau-Levenshtein 距離，或比對是否區分大小寫。 

 就像 CSV 檔案一樣，您需要提供 JSON 檔案的路徑，並在 *entitiesDefinitionUri* 技能參數中進行設定。 路徑應位於 HTTPs 位置。 定義檔大小最多可達 10 MB。

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

更複雜的 JSON 定義範例可以選擇性地提供每個實體的識別碼、描述、類型和子類型，以及其他 *別名*。 如果別名詞彙相符，則也會傳回實體：

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

下表詳細說明您在定義要符合的實體時可以設定的不同設定參數：

|  欄位名稱  |        描述  |
|--------------|----------------------|
| `name` | 最上層實體描述項。 技能輸出中的相符專案將依此名稱分組，並應表示所要尋找之文字的「正規化」形式。  |
| `description`  |  (選擇性) 此欄位可用來作為相符文字 () 的自訂中繼資料的傳遞。 此欄位的值會顯示在技能輸出中的每個實體相符項。 |
| `type` |  (選擇性) 此欄位可用來作為相符文字 () 的自訂中繼資料的傳遞。 此欄位的值會顯示在技能輸出中的每個實體相符項。 |
| `subtype` |  (選擇性) 此欄位可用來作為相符文字 () 的自訂中繼資料的傳遞。 此欄位的值會顯示在技能輸出中的每個實體相符項。 |
| `id` |  (選擇性) 此欄位可用來作為相符文字 () 的自訂中繼資料的傳遞。 此欄位的值會顯示在技能輸出中的每個實體相符項。 |
| `caseSensitive` |  (選擇性) 預設為 false。 布林值，表示與機構名稱的比較是否應區分字元大小寫。 "Microsoft" 的不區分大小寫範例可能是： microsoft、microsoft、MICROSOFT |
| `fuzzyEditDistance` |  (選擇性) 預設為0。 最大值為5。 表示仍會構成符合機構名稱之相符字元的可接受數目。 傳回任何指定之相符的最小可能的顏色。  比方說，如果編輯距離設定為3，"Windows 10" 仍會符合 "Windows"、"Windows10" 和 "windows 7"。 <br/> 當區分大小寫設定為 false 時，大小寫差異不會計入調整大小容限，但也不會計。 |
| `defaultCaseSensitive` |  (選擇性) 變更此實體的預設區分大小寫值。 它是用來變更所有別名 caseSensitive 值的預設值。 |
| `defaultFuzzyEditDistance` |  (選擇性) 變更此實體的預設模糊編輯距離值。 可以用來變更所有別名 fuzzyEditDistance 值的預設值。 |
| `aliases` |  (選擇性) 可以用來指定根機構名稱之替代拼寫或同義字的複雜物件陣列。 |

| 別名屬性 | 說明 |
|------------------|-------------|
| `text`  | 某些目標機構名稱的替代拼寫或標記法。  |
| `caseSensitive` |  (選擇性的) 與上述的根實體 "caseSensitive" 參數相同，但只適用于此一個別名。 |
| `fuzzyEditDistance` |  (選擇性的) 與上述的根實體 "fuzzyEditDistance" 參數相同，但只適用于此一個別名。 |


### <a name="inline-format"></a>內嵌格式

在某些情況下，提供自訂實體清單以直接內嵌到技能定義，可能會比較方便。 在此情況下，您可以使用類似上述的 JSON 格式，但會在技能定義中內嵌。
只有小於 10 KB 大小 (序列化大小) 的設定才能以內嵌方式定義。 

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
或者，如果您決定要提供實體定義檔的指標，則使用格式的範例技能定義如下 `entitiesDefinitionUri` 所示：

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

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>警告：已達到符合的最大容量，即將略過所有更重複的相符專案。

如果偵測到的相符數目大於允許的最大值，就會發出此警告。 在此情況下，我們將停止包含重複的相符專案。 如果您無法接受這項功能，請提出 [支援票證](https://ms.portal.azure.com/#create/Microsoft.Support) ，讓我們可以協助您使用個別的使用案例。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [實體辨識技能 (，以搜尋知名的實體) ](cognitive-search-skill-entity-recognition.md)