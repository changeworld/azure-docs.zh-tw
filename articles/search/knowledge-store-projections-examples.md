---
title: 定義知識存放區中的投影
titleSuffix: Azure Cognitive Search
description: 有關如何將擴充的檔投影至知識存放區，以搭配 Power BI 或 Azure ML 使用的常見模式範例。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f40841887b8116ad1384dc2b827d8215ea659490
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537492"
---
# <a name="how-to-shape-and-export-enrichments"></a>如何塑造和匯出擴充

投影是知識存放區中擴充檔的實體運算式。 有效使用擴充的檔需要結構。 在本文中，您將會探索結構和關聯性、瞭解如何建立投影屬性，以及如何在建立的投射類型之間建立關聯資料的關聯性。 

若要建立投射，資料會使用 [整形器技能](cognitive-search-skill-shaper.md) 來建立自訂物件，或在投影定義中使用內嵌成形語法來成形。 

資料圖形包含預定要投射的所有資料，並以節點階層形式形成。 本文說明各種用來塑造資料的技術，以便將資料投射至採用遭利用至報表、分析或下游處理的實體結構。 

您可以在此 [REST API 範例](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)中找到本文所提供的範例，您可以在 HTTP 用戶端中下載並執行此範例。

## <a name="introduction-to-projection-examples"></a>投影範例簡介

有三種類型的 [投射](knowledge-store-projection-overview.md)：

+ 資料表
+ 物件
+ 檔案儲存體

資料表投射會儲存在 Azure 資料表儲存體中。 物件和檔案投射會寫入 blob 儲存體，其中物件投影會儲存為 JSON 檔案，而且可以包含來自來源文件的內容，以及任何技能輸出或擴充。 擴充管線也可以解壓縮二進位檔（例如影像），這些二進位檔會投射為檔案投射。 當二進位物件投射為物件投射時，只有與其相關聯的中繼資料會儲存為 JSON blob。 

為了瞭解資料成形和投影之間的交集，我們將使用下列技能集作為探索各種設定的基礎。 此技能集會處理原始影像和文字內容。 系統會根據所需的案例，從檔的內容和技能的輸出中定義投影。

> [!IMPORTANT] 
> 試驗投影時， [設定索引子](search-howto-incremental-index.md) 快取屬性以確保成本控制會很有用。 如果未設定索引子快取，編輯投影將會再次擴充整份檔。 當您設定快取且只更新預測時，先前擴充檔的技能集執行不會產生任何新的認知服務費用。

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

使用這個技能集，並以 null `knowledgeStore` 作為基礎，我們的第一個範例會填滿 `knowledgeStore` 物件，並設定投影來建立可在其他案例中使用的表格式資料結構。 

## <a name="projecting-to-tables"></a>投射至資料表

投影到 Azure 儲存體中的資料表，對於使用 Power BI 之類的工具進行報告和分析相當有用。 Power BI 可以從資料表讀取，並根據投射期間產生的索引鍵來探索關聯性。 如果您想要建立儀表板，則擁有相關的資料將會簡化該工作。 

讓我們建立一個儀表板，將從檔中解壓縮的關鍵字組視覺化為單字雲端。 若要建立正確的資料結構，請在技能集中加入塑造器技能，以建立包含檔專屬詳細資料和主要片語的自訂圖形。 將 `pbiShape` 在根節點上呼叫自訂圖形 `document` 。

> [!NOTE] 
> 資料表投射是 Azure 儲存體的資料表，由 Azure 儲存體所加諸的儲存體限制所控管。 如需詳細資訊，請參閱 [資料表儲存體限制](/rest/api/storageservices/understanding-the-table-service-data-model)。 知道實體大小不能超過 1 MB，而且單一屬性不能大於 64 KB，會很有用。 這些條件約束讓資料表成為儲存大量小型實體的絕佳解決方案。

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>使用成形技能來建立自訂圖形

建立可投影至資料表儲存體的自訂圖形。 如果沒有自訂圖形，投射只能參考單一節點， (每個輸出) 一個投射。 建立自訂圖形會將各種元素匯總成一個新的邏輯，以投射為單一資料表，或是在資料表集合中切割和散發。 

在此範例中，自訂圖形結合了中繼資料和已識別的實體和主要片語。 呼叫物件 `pbiShape` ，且其父代 `/document` 。 

> [!IMPORTANT] 
> 成形的其中一個目的是確保所有擴充節點都以正確格式的 JSON 表示，這是投射到知識存放區時所需的。 當擴充樹狀結構包含的節點不是格式正確的 (JSON 時（例如，當擴充的父代像是字串) ）時，更是如此。
>
> 請注意最後兩個節點， `KeyPhrases` 以及 `Entities` 。 這些會使用來包裝成有效的 JSON 物件 `sourceContext` 。 這是必要的 `keyphrases` ，而且 `entities` 會在基本專案上擴充，而且必須先轉換成有效的 JSON，才能投射它們。
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

將上述的整形技能新增至技能集。 

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

既然我們已擁有投影至資料表所需的所有資料，請以資料表定義更新 knowledgeStore 物件。 在此範例中，我們有三個數據表，透過設定 `tableName` 和屬性來定義 `source` `generatedKeyName` 。

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

您可以遵循下列步驟來處理您的工作：

1. 將 ```storageConnectionString``` 屬性設為有效的 V2 一般用途儲存體帳戶連接字串。  

1. 藉由發出 PUT 要求來更新技能集。

1. 更新技能集之後，請執行索引子。 

您現在有三個數據表的工作投射。 將這些資料表匯入 Power BI 應該會導致 Power BI 自動探索關聯性。

繼續進行下一個範例之前，讓我們先回顧一下資料表投射的各個層面，以瞭解切割和關聯資料的機制。

### <a name="slicing"></a>切片 

配量是一種技術，可將整個合併圖形細分組成零件。 結果包含個別但相關的資料表，您可以個別使用這些資料表。

在此範例中， `pbiShape` 是合併圖形 (或擴充節點) 。 在投射定義中， `pbiShape` 會切割成其他資料表，讓您提取圖形的各部分， ```keyPhrases``` 以及 ```Entities``` 。 在 Power BI 中，這項功能很有用，因為有多個實體和 keyPhrases 與每個檔相關聯，而如果您可以將實體和 keyPhrases 視為分類的資料，您將會獲得更多的見解。

配量會以隱含方式產生父資料表與子資料工作表之間的關聯性，並使用 ```generatedKeyName``` 父資料表中的，在子資料工作表中建立具有相同名稱的資料行。 

### <a name="naming-relationships"></a>命名關聯性

```generatedKeyName```和 ```referenceKeyName``` 屬性是用來在資料表之間建立關聯，甚至是跨投影類型。 子資料工作表/投射中的每個資料列都有一個指向父系的屬性。 子系中的資料行或屬性名稱是 ```referenceKeyName``` 來自父系的。 當 ```referenceKeyName``` 未提供時，服務會將其預設為 ```generatedKeyName``` 來自父系的。 

Power BI 依賴這些產生的金鑰來探索資料表內的關聯性。 如果您需要以不同方式命名的子資料工作表中的資料行，請在 ```referenceKeyName``` 父資料表上設定屬性。 其中一個範例是在 ```generatedKeyName``` pbiDocument 資料表上設定 AS 識別碼，並將設定為 ```referenceKeyName``` DocumentID。 這會產生 pbiEntities 和 pbiKeyPhrases 資料表中的資料行，其中包含名為 DocumentID 的檔識別碼。

## <a name="projecting-to-objects"></a>投射至物件

物件投影與資料表投影沒有相同的限制，而且更適合用來投射大型檔。 在此範例中，會以物件投射的形式傳送整份檔。 物件投影僅限於容器中的單一投射，無法進行切割。

若要定義物件投射，請使用 ```objects``` 投影中的陣列。 您可以使用造型技能或使用物件投影的內嵌成形來產生新的圖形。 雖然表格範例示範了建立圖案和配量的方法，但此範例示範如何使用內嵌成形。 

內嵌成形是能夠在投影的輸入定義中建立新的圖形。 內嵌成形所建立的匿名物件，與塑造器技能在我們的案例中所產生的 (一樣， `pbiShape`) 。 如果您要定義的圖形不打算重複使用，則內嵌成形會很有用。

投影屬性是陣列。 這個範例會將新的投射實例加入至陣列，其中 knowledgeStore 定義包含內嵌投射。 使用內嵌投影時，您可以省略整形人員技能。

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

## <a name="projecting-to-file"></a>投射至檔案

檔案投射是從來源文件解壓縮的影像，或可投影出擴充程式的擴充輸出。 檔案投影與物件投影類似，會在 Azure 儲存體中實作為 blob，並包含影像。 

若要產生檔案投射，請使用 `files` 投影物件中的陣列。 此範例會將從檔解壓縮的所有影像投影至稱為的容器 `samplefile` 。

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

## <a name="projecting-to-multiple-types"></a>投射至多個類型

更複雜的案例可能需要您在投影類型之間投射內容。 例如，如果您需要將某些資料（例如主要片語和實體）投射到資料表，請將文字和版面配置文字的 OCR 結果儲存為物件，然後將影像投影為檔案。 

此範例會使用下列變更來更新技能集：

1. 建立每個檔都有一個資料列的資料表。
1. 建立與 document 資料表相關的資料表，其中每個主要片語都識別為此資料表中的一個資料列。
1. 建立與 document 資料表相關的資料表，其中每個實體都識別為此資料表中的一個資料列。
1. 使用每個影像的版面配置文字來建立物件投射。
1. 建立檔案投射，並投射每個已解壓縮的影像。
1. 建立包含檔資料表參考、具有版面配置文字和檔案投射之物件投影的交叉參考資料表。

這些變更會立即反映在 knowledgeStore 定義中。 

### <a name="shape-data-for-cross-projection"></a>針對交叉投射塑造資料

若要取得這些投影所需的圖形，請先加入新的成形技能，以建立稱為的成形物件 `crossProjection` 。 

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

### <a name="define-table-object-and-file-projections"></a>定義資料表、物件和檔案投影

從合併的 crossProjection 物件，將物件分割成多個資料表，將 OCR 輸出視為 blob，然後將映射儲存為檔案 (也儲存在 Blob 儲存體) 中。

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

物件投射需要每個投射的容器名稱、物件投影或檔案投射無法共用容器。 

### <a name="relationships-among-table-object-and-file-projections"></a>資料表、物件和檔案投影之間的關聯性

此範例也強調投影的另一項功能。 藉由在相同的投射物件內定義多個預測類型，就可以在不同的類型之間和不同的類型之間表示關聯性 (資料表、物件、檔案) 。 這可讓您從檔的資料表資料列開始，並在物件投影中尋找該檔內影像的所有 OCR 文字。 

如果您不想要相關資料，請在不同的投射物件中定義投影。 例如，下列程式碼片段會導致資料表相關，但資料表與物件之間沒有關聯性 (OCR 文字) 投影。 

當您想要在不同的圖形中針對不同需求投射相同的資料時，投射群組會很有用。 例如，Power BI 儀表板的投射群組，以及用來將用來定型以自訂技能包裝之機器學習模型的資料的另一個投射群組。

建立不同類型的投影時，會先產生檔案和物件投影，然後將路徑加入至資料表。

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

在定義投影時，有幾個常見的問題可能會導致非預期的結果。 如果知識存放區中的輸出不是您預期的結果，請檢查這些問題。

+ 請勿將字串擴充成形為有效的 JSON。 擴充字串時（例如使用主要片語擴充），擴充 `merged_content` 的屬性會以 `merged_content` 擴充樹狀結構內的子系表示。 預設標記法的 JSON 格式不正確。 因此在投射時，請務必將擴充轉換為具有名稱和值的有效 JSON 物件。

+ 省略 ```/*``` 來源路徑結尾的。 如果投射的來源是 `/document/pbiShape/keyPhrases` ，則會將主要片語陣列投射為單一物件/資料列。 相反地，請將來源路徑設定為， `/document/pbiShape/keyPhrases/*` 以針對每個主要片語產生單一資料列或物件。

+ 路徑語法錯誤。 路徑選取器會區分大小寫，如果您未使用選取器的確切大小寫，可能會導致遺漏輸入警告。

## <a name="next-steps"></a>後續步驟

本文中的範例將示範如何建立投影的一般模式。 現在您已充分瞭解這些概念，您可以更妥善地針對特定案例建立投影。

當您探索新功能時，請考慮增量擴充作為下一個步驟。 增量擴充是以快取為基礎，可讓您重複使用任何不受技能集修改影響的擴充。 這特別適用于包含 OCR 和影像分析的管線。

> [!div class="nextstepaction"]
> [增量擴充和快取簡介](cognitive-search-incremental-indexing-conceptual.md)

如需預測的總覽，請深入瞭解群組和配量等功能，以及如何[在技能集中定義這些](knowledge-store-projection-overview.md)功能

> [!div class="nextstepaction"]
> [知識存放區中的投影](knowledge-store-projection-overview.md)