---
title: 塑形器認知技能
titleSuffix: Azure Cognitive Search
description: 從非結構化資料中解壓縮中繼資料和結構化資訊，並在 Azure 認知搜尋的 AI 擴充管線中將其塑造為複雜類型。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75754128"
---
# <a name="shaper-cognitive-skill"></a>塑形器認知技能

**整形**程式技能會將數個輸入合併成[複雜類型](search-howto-complex-data-types.md)，稍後可在擴充管線中加以參考。 **整形**程式技能可讓您建立結構、定義該結構成員的名稱，並將值指派給每個成員。 在搜尋案例中有用的匯總欄位範例包括將名字和姓氏結合成單一結構、城市和州/省成單一結構，或名稱和出生日組成單一結構，以建立唯一的身分識別。

此外，[案例 3](#nested-complex-types)中所述的**整形**程式技能會在輸入中加入選擇性的*sourceCoNtext*屬性。 *Source*和*sourceCoNtext*屬性互斥。 如果輸入是在技能的內容，請直接使用*source*。 如果輸入的內容與技能內容*不同*，請使用*sourceCoNtext*。 *SourceCoNtext*會要求您定義具有特定元素的嵌套輸入，而該專案會被定址為來源。 

輸出名稱一律為 "output"。 就內部而言，管線可以對應不同的名稱（例如 "analyzedText"），如下列範例所示，但**整形**人員技能本身會在回應中傳回 "output"。 如果您正在對擴充文件進行偵錯，並發現命名差異，或如果您建置自訂技能並自行建構回應，這可能十分重要。

> [!NOTE]
> **整形**程式技能不會系結至認知服務 API，您不需支付使用它的費用。 不過，您仍然應該[連結認知服務資源](cognitive-search-attach-cognitive-services.md)，以覆寫限制您每天少量擴充的**免費**資源選項。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>案例1：複雜類型

假設您要建立一個稱為 *analyzedText* 的結構，此結構有兩個成員，分別為：*text* 和 *sentiment*。 在索引中，多部分可搜尋的欄位稱為*複雜型*別，而且通常會在來源資料具有對應的複雜結構時建立。

不過，建立複雜類型的另一種方法是透過**整形**程式技能。 藉由在技能集中包含此技能，在技能集處理期間的記憶體中作業可以輸出具有嵌套結構的資料圖形，然後可以對應至索引中的複雜類型。 

下列範例技能定義會提供成員名稱做為輸入。 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>範例索引

技能集是由索引子叫用，而且索引子需要索引。 索引中的複雜欄位標記法看起來可能如下列範例所示。 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>技能輸入

為此**整形**程式技能提供可用輸入的傳入 JSON 檔可能是：

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>技能輸出

「**整形**」技能會產生名為*analyzedText*的新元素，其中包含*text*和*情感*的組合元素。 此輸出符合索引架構。 它將會在 Azure 認知搜尋索引中匯入和編制索引。

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>案例2：輸入匯總

在另一個範例中，假設在不同的管線處理階段，您已經擷取書名和書本不同頁面上的章節標題。 您現在可以建立由這些各種輸入所組成的單一結構。

此案例的**整形**程式技能定義看起來可能像下列範例：

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>技能輸出
在此情況下，**整形**會將所有章節標題壓平合併，以建立單一陣列。 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>案例3：來自嵌套內容的輸入匯總

假設您有一本書的標題、章節和內容，並已針對內容執行實體辨識和關鍵字組，現在需要將不同技能的結果匯總成單一圖形，其中包含章節名稱、實體和關鍵字組。

此案例的**整形**程式技能定義看起來可能像下列範例：

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>技能輸出
在此情況下，**整形**會建立複雜型別。 此結構存在於記憶體中。 如果您想要將它儲存到[知識存放區](knowledge-store-concept-intro.md)，您應該在技能集中建立可定義儲存特性的投影。

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [如何使用複雜類型](search-howto-complex-data-types.md)
+ [知識存放區 (預覽)](knowledge-store-concept-intro.md)
+ [在 REST 中建立知識存放區](knowledge-store-create-rest.md)