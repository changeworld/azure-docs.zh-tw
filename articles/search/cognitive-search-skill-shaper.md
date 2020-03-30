---
title: 塑形器認知技能
titleSuffix: Azure Cognitive Search
description: 從非結構化資料中提取中繼資料和結構化資訊，並將其塑造成 Azure 認知搜索中的 AI 擴充管道中的複雜類型。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754128"
---
# <a name="shaper-cognitive-skill"></a>塑形器認知技能

**Shaper**技能將多個輸入合併到[一個複雜的類型](search-howto-complex-data-types.md)中，可在以後在擴充管道中引用。 **Shaper**技能允許您基本上創建一個結構，定義該結構的成員的名稱，並將值分配給每個成員。 在搜索方案中有用的合併欄位的示例包括將名字和姓氏合併到單個結構、城市和州到單個結構中，或將名稱和出生日期合併到單個結構中以建立唯一標識。

此外，[方案 3](#nested-complex-types)中所示的**Shaper**技能向輸入添加了可選的*源上下文*屬性。 *源**內容屬性*是互斥的。 如果輸入位於技能的上下文中，只需使用*源*。 如果輸入與技能上下文*的上下文不同*，請使用*sourceCoNtext*。 *sourceCoNtext*要求您定義一個嵌套輸入，其中特定元素被作為源定址。 

輸出名稱始終為"輸出"。 在內部，管道可以映射不同的名稱，如下面的示例所示的"分析文本"，但**Shaper**技能本身在回應中返回"輸出"。 如果您正在對擴充文件進行偵錯，並發現命名差異，或如果您建置自訂技能並自行建構回應，這可能十分重要。

> [!NOTE]
> **Shaper**技能不受認知服務 API 的約束，您也不會因使用它而付費。 不過，您仍然應該[連結認知服務資源](cognitive-search-attach-cognitive-services.md)，以覆寫限制您每天少量擴充的**免費**資源選項。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>方案 1：複雜類型

假設您要建立一個稱為 *analyzedText* 的結構，此結構有兩個成員，分別為：*text* 和 *sentiment*。 在索引中，多部分可搜索欄位稱為*複雜類型*，並且通常在來源資料具有映射到它的相應複雜結構時創建該欄位。

但是，創建複雜類型的另一種方法是通過**Shaper**技能。 通過將此技能包括在技能集中，技能集處理期間的記憶體中操作可以使用嵌套結構輸出資料形狀，然後這些結構可以映射到索引中的複雜類型。 

以下示例技能定義提供成員名稱作為輸入。 


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

### <a name="sample-index"></a>樣本索引

索引子調用技能集，索引子需要索引。 索引中複雜的欄位表示可能類似于以下示例。 

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

為此**Shaper**技能提供可用輸入的傳入 JSON 文檔可以是：

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

**沙珀**技能生成一個新的元素，稱為*分析文本*與*文本**和情緒*的組合元素。 此輸出符合索引架構。 它將在 Azure 認知搜索索引中導入和編制索引。

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

## <a name="scenario-2-input-consolidation"></a>方案 2：輸入整合

在另一個範例中，假設在不同的管線處理階段，您已經擷取書名和書本不同頁面上的章節標題。 您現在可以建立由這些各種輸入所組成的單一結構。

此方案的**Shaper**技能定義可能類似于以下示例：

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
在這種情況下 **，Shaper**會平展所有章節標題以創建單個陣列。 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>方案 3：來自嵌套上下文的輸入整合

假設您擁有書籍的標題、章節和內容，並在內容上運行實體識別和關鍵短語，現在需要將不同技能的結果聚合為具有章節名稱、實體和關鍵短語的單個形狀。

此方案的**Shaper**技能定義可能類似于以下示例：

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
在這種情況下 **，Shaper**將創建一個複雜的類型。 此結構存在於記憶體中。 如果要將其保存到[知識存儲，](knowledge-store-concept-intro.md)則應在技能集中創建定義存儲特徵的投影。

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