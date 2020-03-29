---
title: 定義知識存儲中的投影
titleSuffix: Azure Cognitive Search
description: 有關如何將富集文檔投影到知識存儲中以便與 Power BI 或 Azure ML 一起使用的常見模式示例。
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943668"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>知識庫預測：如何塑造和匯出濃縮

> [!IMPORTANT] 
> 知識存放區目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供預覽功能。 目前的入口網站支援有限，而且沒有 .NET SDK 支援。

投影是知識存儲中豐富文檔的物理表達。 有效利用富集文檔需要結構。 在本文中，您將探討結構和關係，瞭解如何構建投影屬性，以及如何跨您創建的投影類型關聯資料。 

要創建投影，必須使用[Shaper 技能](cognitive-search-skill-shaper.md)來創建自訂物件或使用投影定義中的內聯整形語法來塑造資料。 

資料形狀包含您要投影的所有資料，這些資料形成為節點層次結構。 本文介紹幾種塑造資料的技術，以便將其投影到有利於報告、分析或下游處理的物理結構中。 

本文仲介紹的示例可以在此 REST API[示例中](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)找到，您可以在 HTTP 用戶端中下載和運行該示例。

## <a name="introduction-to-the-examples"></a>示例簡介

如果您熟悉[投影](knowledge-store-projection-overview.md)，您會記得有三種類型：

+ 資料表
+ 物件
+ 檔案

表投影存儲在 Azure 表存儲中。 物件和檔投影寫入 blob 存儲，其中物件投影保存為 JSON 檔，可以包含來源文件中的內容以及任何技能輸出或擴充。 擴充管道還可以提取二進位檔案，如圖像，這些二進位檔案投影為檔投影。 當二進位物件投影為物件投影時，只有與其關聯的中繼資料保存為 JSON Blob。 

為了瞭解資料整形和投影之間的交集，我們將使用以下技能集作為探索各種配置的基礎。 此技能集處理原始圖像和文本內容。 投影將從文檔的內容和技能的輸出中定義，用於我們想要支援的方案。

> [!IMPORTANT] 
> 試驗投影時，[設置索引子緩存屬性](search-howto-incremental-index.md)以確保成本控制非常有用。 如果未設置索引子緩存，則編輯投影將導致整個文檔再次豐富。 設置緩存且僅更新投影時，以前豐富的文檔的技能集執行不會導致任何新的認知服務費用。

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

使用此技能集（以 null`knowledgeStore`為基礎），我們的第一個示例填充`knowledgeStore`物件，配置了創建可在其他方案中使用的表格資料結構的預測。 

## <a name="projecting-to-tables"></a>投影到表

投影到 Azure 存儲中的表對於使用 Power BI 等工具進行報告和分析非常有用。 Power BI 可以從表中讀取，並根據投影期間生成的鍵發現關係。 如果您嘗試構建儀表板，則擁有相關資料將簡化該任務。 

假設我們正在嘗試構建一個儀表板，您可以在其中將從文檔中提取的關鍵短語視覺化為單詞雲。 為了創建正確的資料結構，我們可以向技能集中添加 Shaper 技能，以創建具有文檔特定詳細資訊和關鍵短語的自訂形狀。 將在`pbiShape``document`根節點上調用自訂形狀。

> [!NOTE] 
> 表投影是 Azure 存儲表，受 Azure 存儲施加的存儲限制的約束。 有關詳細資訊，請參閱[表存儲限制](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)。 瞭解實體大小不能超過 1 MB 且單個屬性不能大於 64 KB 是很有用的。 這些約束使表成為存儲大量小實體的良好解決方案。

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>使用沙珀技能創建自訂形狀

創建可投影到表存儲的自訂形狀。 如果沒有自訂形狀，投影只能引用單個節點（每個輸出一個投影）。 通過創建自訂形狀，可以將各種元素聚合到一個新的邏輯整體中，該集合可以投影為單個表，也可以在表集合中切片和分發。 

在此示例中，自訂形狀結合了中繼資料和標識的實體和關鍵短語。 物件在 下`pbiShape``/document`調用並處於父級。 

> [!IMPORTANT] 
> 整形的一個目的是確保所有富集節點都以格式良好的 JSON 表示，這是投影到知識存儲所必需的。 當富集樹包含格式不正確的 JSON 的節點（例如，當富集父為像字串這樣的基元時），尤其如此。
>
> 請注意最後兩個節點`KeyPhrases`和`Entities`。 這些將用 打包到有效的 JSON`sourceContext`物件中。 這在基元上`keyphrases`需要`entities`並且是擴充，並且需要轉換為有效的 JSON，然後才能投影它們。
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

將上述沙珀技能添加到技能集中。 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

現在，我們已經擁有投影到表所需的所有資料，請使用表定義更新知識存儲物件。 在此示例中，我們有三個表，由 設置`tableName`和`source``generatedKeyName`屬性定義。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

您可以按照以下步驟處理您的工作：

1. 將```storageConnectionString```屬性設置為有效的 V2 通用存儲帳戶連接字串。  

1. 通過發出 PUT 請求更新技能集。

1. 更新技能集後，運行索引子。 

現在，您有一個包含三個表的工作投影。 將這些表導入 Power BI 應會導致 Power BI 自動探索關係。

在繼續下一個示例之前，讓我們重新訪問表投影的各個方面，以瞭解切片和關聯資料的機制。

### <a name="slicing"></a>切片 

切片是將整個合併形狀細分為各個組成部分的技術。 結果由單獨但相關的表組成，您可以單獨使用這些表。

在此示例中，`pbiShape`是合併形狀（或擴充節點）。 在投影定義中，`pbiShape`將切片到其他表中，這使您能夠拉出形狀的部分，```keyPhrases```和```Entities```。 在 Power BI 中，這非常有用，因為多個實體和鍵短語與每個文檔相關聯，如果您可以看到實體和鍵短語作為分類資料，您將獲得更多見解。

隱式切片會在父表和子表之間生成關係，使用```generatedKeyName```父表中的 創建子表中具有相同名稱的列。 

### <a name="naming-relationships"></a>命名關係

和```generatedKeyName``````referenceKeyName```屬性用於跨表甚至跨投影類型關聯資料。 子表/投影中的每個行都有一個指向父行的屬性。 子列或子屬性的名稱是從```referenceKeyName```父項。 如果未提供```referenceKeyName```時，服務將預設為 父服務中的```generatedKeyName```。 

Power BI 依賴于這些生成的鍵來發現表中的關係。 如果需要以不同方式命名的子表中的列，則在```referenceKeyName```父表中設置該屬性。 例如，```generatedKeyName```在 pbiDocument 表上設置作為 ID，```referenceKeyName```將 設置為文件識別碼。 這將導致 pbi實體和 pbiKeyPhrases 表中包含文件識別碼 的列名為 DocumentID。

## <a name="projecting-to-objects"></a>投影到物件

物件投影與表投影沒有相同的限制，更適合投影大型文檔。 在此示例中，我們將整個文檔投影到物件投影。 物件投影僅限於容器中的單個投影，不能切片。

要定義物件投影，我們將在投影中使用```objects```陣列。 您可以使用 Shaper 技能生成新形狀，或使用物件投影的內聯整形。 雖然表示例演示了創建形狀和切片的方法，但此示例演示了內聯整形的使用。 

內聯整形是在投影的輸入定義中創建新形狀的能力。 內聯整形創建一個匿名物件，該物件與 Shaper 技能將生成的內容相同（`pbiShape`在我們的案例中， ）。 如果要定義不打算重用的形狀，則內聯整形非常有用。

投影屬性是陣列。 在此示例中，我們將向陣列添加新的投影實例，其中知識存儲定義包含內聯投影。 使用內聯投影時，可以省略 Shaper 技能。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>投影到檔

檔投影是從來源文件中提取的圖像，也可以是從濃縮過程中投影的擴充輸出。 檔投影（類似于物件投影）在 Azure 存儲中作為 blob 實現，並包含映射。 

要生成檔投影，我們使用投影物件中的`files`陣列。 本示例將從文檔中提取的所有圖像專案到名為 的`samplefile`容器。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>投影到多種類型

更複雜的方案可能需要跨投影類型投影內容。 例如，如果需要將某些資料（如關鍵短語和實體）投影到表中，則將文本和佈局文本的 OCR 結果另存為物件，然後將圖像投影為檔。 

在此示例中，對技能集的更新包括以下更改：

1. 為每個文檔創建具有行的表。
1. 創建與文檔表相關的表，每個鍵短語在此表中標識為行。
1. 創建與文檔表相關的表，每個實體在此表中標識為一行。
1. 使用每個圖像的佈局文本創建物件投影。
1. 創建檔投影，投影每個提取的圖像。
1. 創建一個交叉引用表，其中包含對文檔表的引用、帶有佈局文本的物件投影和檔投影。

這些更改將反映在知識存儲定義中。 

### <a name="shape-data-for-cross-projection"></a>用於交叉投影的圖形資料

要獲取這些投影所需的形狀，首先添加一個新的 Shaper 技能，該技能創建一個稱為`crossProjection`的形狀物件。 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>定義表、物件和檔投影

從整合的 crossProjection 物件中，我們可以將物件切片到多個表中，將 OCR 輸出捕獲為 Blob，然後將圖像另存為檔（也保存在 Blob 存儲中）。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

物件投影需要每個投影的容器名稱，物件投影或檔投影不能共用容器。 

### <a name="relationships-among-table-object-and-file-projections"></a>表、物件和檔投影之間的關係

此示例還突出顯示了投影的另一個特徵。 通過在同一投影物件中定義多種類型的投影，在不同類型的類型（表、物件、檔）內和跨類型（表、物件、檔）中表達了一種關係，允許您從文檔的表行開始，並查找圖像的所有 OCR 文本在物件投影中的文檔中。 

如果不希望與資料相關，請在不同的投影物件中定義投影。 例如，以下程式碼片段將導致表相關，但表和物件 （OCR 文本） 投影之間沒有關系。 

當您希望針對不同需求以不同形狀投影相同資料時，投影組非常有用。 例如，Power BI 儀表板的投影組以及用於捕獲用於訓練以自訂技能包裝的機器學習模型的資料的另一個投影組。

生成不同類型的投影時，將首先生成檔和物件投影，並將路徑添加到表中。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>常見的問題

定義投影時，有幾個常見問題可能會導致意外的結果。 如果知識存儲中的輸出不是您期望的，請檢查這些問題。

+ 不將字串擴充成有效的 JSON。 當字串富集時（例如`merged_content`，用關鍵短語進行豐富）時，富集屬性工作表示為`merged_content`富集樹中的子項。 預設表示形式格式不正確。 因此，在投影時，請確保將富集轉換為具有名稱和值的有效 JSON 物件。

+ 省略源```/*```路徑末尾的 。 如果投影的源為`/document/pbiShape/keyPhrases`，則關鍵短語陣列將投影為單個物件/行。 相反，將源路徑設置為`/document/pbiShape/keyPhrases/*`為每個關鍵短語生成單個行或物件。

+ 路徑語法錯誤。 直接選取器區分大小寫，如果不對選擇器使用確切大小寫，則可能導致缺少輸入警告。

## <a name="next-steps"></a>後續步驟

本文中的示例演示了有關如何創建投影的常見模式。 現在，您已經很好地理解了這些概念，因此您更有能力為特定方案構建投影。

在探索新功能時，請考慮增量充實作為下一步。 增量擴充基於緩存，它允許您重用任何不受技能集修改影響的富集。 這對於包含 OCR 和圖像分析的管道特別有用。

> [!div class="nextstepaction"]
> [增量濃縮和緩存簡介](cognitive-search-incremental-indexing-conceptual.md)

有關投影的概述，請詳細瞭解組和切片等功能，以及如何[在技能集中定義它們](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [知識商店中的投影](knowledge-store-projection-overview.md)

