---
title: 知識存儲中的投影（預覽）
titleSuffix: Azure Cognitive Search
description: 將豐富的資料從 AI 濃縮索引管道中保存並成形狀到知識存儲中，用於全文檢索搜尋以外的方案。 知識存放區目前為公開預覽狀態。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942977"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Azure 認知搜索中知識存儲中的投影

> [!IMPORTANT] 
> 知識存放區目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供預覽功能。 目前的入口網站支援有限，而且沒有 .NET SDK 支援。

Azure 認知搜索通過內置的認知技能和自訂技能實現內容豐富，作為索引的一部分。 富集創建以前不存在的新資訊：從圖像中提取資訊、從文本中檢測情緒、關鍵短語和實體，僅舉幾例。 富集還會將結構添加到未區分的文本。 所有這些過程都會導致文檔使全文檢索搜尋更加有效。 在許多情況下，豐富的文檔可用於搜索以外的方案，例如知識挖掘。

投影是[知識存儲](knowledge-store-concept-intro.md)的組成部分，是豐富的文檔視圖，可以保存到物理存儲中，用於知識挖掘目的。 投影允許您將資料"專案"為符合您需求的形狀，從而保留關係，以便 Power BI 等工具無需額外精力即可讀取資料。

投影可以是表格的，資料存儲在 Azure 表存儲中的行和列中，或者存儲在 Azure Blob 存儲中的 JSON 物件。 您可以在資料進行豐富時定義資料的多個投影。 當您希望單個用例的相同資料形狀不同時，多個投影非常有用。

知識庫支援三種類型的投影：

+ **表**：對於最能表示為行和列的資料，表投影允許您在表存儲中定義架構化形狀或投影。 只有有效的 JSON 物件才能投影為表，富集文檔可以包含不命名為 JSON 物件的節點，並且在投影這些物件時，使用掃描器技能或內聯整形創建有效的 JSON 物件。

+ **物件**：當您需要資料並擴充的 JSON 表示形式時，物件投影將另存為 blob。 只有有效的 JSON 物件才能投影為物件，富集文檔可以包含不命名為 JSON 物件的節點，並且在投影這些物件時，使用掃描器技能或內聯整形創建有效的 JSON 物件。

+ **檔**：當您需要保存從文檔中提取的圖像時，檔投影允許您將正常化圖像保存到 Blob 存儲。

要查看在上下文中定義的投影，請逐步在[REST 中創建知識存儲](knowledge-store-create-rest.md)。

## <a name="projection-groups"></a>投影組

在某些情況下，您需要以不同形狀投影富集資料，以滿足不同的目標。 知識存儲允許您定義多個投影組。 投影組具有相互排他性和關聯性以下關鍵特徵。

### <a name="mutual-exclusivity"></a>相互排他性

投影到單個組的所有內容都獨立于投影到其他投影組中的資料。
這種獨立性意味著可以以不同的形狀使用相同的資料，但在每個投影組中重複。

### <a name="relatedness"></a>相關性

投影組現在允許您跨投影類型投影文檔，同時保留跨投影類型的關係。 在單個投影組中投影的所有內容都保留跨投影類型的資料中的關係。 在表中，關係基於生成的金鑰，每個子節點保留對父節點的引用。 跨類型（表、物件和檔）在跨不同類型投影單個節點時，將保留關係。 例如，請考慮一個包含圖像和文本的文檔的方案。 您可以將文本投影到表或物件，將圖像投影到表或物件具有包含檔 URL 的列/屬性的檔。

## <a name="input-shaping"></a>輸入整形

使資料的形狀或結構正確是有效使用的關鍵，無論是表還是物件。 根據計畫訪問和使用資料的方式塑造或構建資料的能力是技能集中中作為**Shaper**技能公開的關鍵功能。  

當富集樹中具有與投影架構匹配的物件時，投影更易於定義。 更新的[Shaper 技能](cognitive-search-skill-shaper.md)允許您從擴充樹的不同節點組成物件，並在新節點下對其進行父級。 **Shaper**技能允許您使用嵌套物件定義複雜類型。

當您定義了包含投影所需的所有元素的新形狀時，現在可以使用此形狀作為投影的源或作為其他技能的輸入。

## <a name="projection-slicing"></a>投影切片

定義投影組時，可以將擴充樹中的單個節點分割到多個相關的表或物件中。 使用源路徑添加是現有投影的子節點的投影將導致子節點被從父節點中切片並投影到新的但相關的表或物件中。 此技術允許您在掃描器技能中定義單個節點，該節點可以是所有投影的源。

## <a name="table-projections"></a>表預測

因為它使導入更容易，我們建議使用 Power BI 進行資料流覽的表投影。 此外，表投影允許更改表關係之間的基數。 

您可以將索引中的單個文檔投影到多個表中，從而保留關係。 投影到多個表時，完整形狀將投影到每個表中，除非子節點是同一組中另一個表的源。

### <a name="defining-a-table-projection"></a>定義表投影

在技能集`knowledgeStore`的元素中定義表投影時，首先將富集樹上的節點映射到表源。 通常，此節點是**沙珀**技能的輸出，您添加到技能清單中，以生成要投影到表中的特定形狀。 可以選擇投影的節點可以切片到多個表中。 表定義是要投影的表的清單。

每個表需要三個屬性：

+ 表名稱：Azure 存儲中的表的名稱。

+ 生成 KeyName：唯一標識此行的鍵的列名。

+ 源：從您從源源源源的濃縮樹中的節點。 此節點通常是沙棘的輸出，但可以是任何技能的輸出。

下面是表投影的示例。

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

如本示例所示，關鍵短語和實體將建模到不同的表中，並將包含對每行父（MainTable）的引用。

## <a name="object-projections"></a>物件投影

物件投影是可以從任何節點獲取的富集樹的 JSON 表示形式。 在許多情況下，創建表投影的相同**Shaper**技能可用於生成物件投影。 

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

生成物件投影需要一些特定于物件的屬性：

+ 存儲容器：將保存物件的 blob 容器
+ 源：作為投影根的富集樹節點的路徑

## <a name="file-projection"></a>檔投影

檔投影與物件投影類似，僅對`normalized_images`集合執行。 與物件投影類似，檔投影保存在 Blob 容器中，其資料夾首碼為文件識別碼 的 base64 編碼值。 檔投影不能與物件投影共用同一容器，需要投影到其他容器中。

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

## <a name="projection-lifecycle"></a>投影生命週期

您的投影的生命週期與資料來源中的來源資料相關聯。 更新資料並重新編制索引後，投影會隨著擴充結果進行更新，確保您的預測最終與資料來源中的資料一致。 投影將繼承已為索引配置的刪除策略。 刪除索引子或搜索服務本身時，不會刪除投影。

## <a name="using-projections"></a>使用投影

運行索引子後，可以讀取通過投影指定的容器或表中的投影資料。

對於分析，Power BI 中的探索與將 Azure 表存儲設置為數據源一樣簡單。 您可以使用 其中的關係輕鬆地在資料上創建一組視覺化效果。

或者，如果需要在資料科學管道中使用富集資料，則可以[將來自 Blob 的資料載入到熊貓資料框架](../machine-learning/team-data-science-process/explore-data-blob.md)中。

最後，如果需要從知識存儲匯出資料，Azure 資料工廠具有用於匯出資料的連接器並將其放在您選擇的資料庫中。 

## <a name="next-steps"></a>後續步驟

作為下一步，使用示例資料和說明創建第一個知識存儲。

> [!div class="nextstepaction"]
> [在 REST 中創建知識存儲](knowledge-store-create-rest.md)。

有關高級投影概念（如切片、內聯整形和關係）的教程，請從[知識存儲中定義投影](knowledge-store-projections-examples.md)開始

> [!div class="nextstepaction"]
> [定義知識存儲中的投影](knowledge-store-projections-examples.md)
