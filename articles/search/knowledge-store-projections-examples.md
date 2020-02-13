---
title: 定義知識存放區中的投影
titleSuffix: Azure Cognitive Search
description: 有關如何將擴充的檔投影到知識存放區，以搭配 Power BI 或 Azure ML 使用的常見模式範例。
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165510"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>知識存放區投影：如何塑造擴充並將其匯出至知識存放區

> [!IMPORTANT] 
> 知識存放區目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供預覽功能。 目前的入口網站支援有限，而且沒有 .NET SDK 支援。

投影是知識存放區中擴充檔的實體運算式。 有效使用擴充的檔需要結構。 在本文中，您將探索結構和關聯性、學習如何建立投射屬性，以及如何在您建立的各種投影類型之間建立資料的關聯。 

若要建立投影，您必須使用造型技能來塑造資料，以建立自訂物件或使用內嵌成形語法。 資料圖形包含您想要投影的所有資料。 本檔提供每個選項的範例，您可以選擇使用您所建立之投影的其中一個選項。


有三種類型的預測：
+ 資料表
+ 物件
+ 檔案

資料表投射會儲存在 Azure 資料表儲存體中。 物件和檔案投射會寫入至 blob 儲存體，物件投影會儲存為 JSON 檔案，而且可以包含檔中的內容，以及任何技能輸出或擴充。 擴充管線也可以將二進位檔案（例如影像）解壓縮，這些二進位檔會投射為檔案投射。 當二進位物件投射為物件投射時，只有與其相關聯的中繼資料會儲存為 JSON blob。 

為了瞭解資料成形與投影之間的交集，我們將使用下列技能集作為探索各種設定的基礎。 此技能集會處理原始影像和文字內容。 系統會根據我們想要支援的案例，從檔內容和技能的輸出中定義預測。

或者，您也可以在本逐步解說中下載並使用[REST API 範例](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)，以及所有的呼叫。

> [!IMPORTANT] 
> 試驗投影時，[設定索引子](search-howto-incremental-index.md)快取屬性以確保成本控制會很有説明。 如果未設定索引子快取，編輯投影會導致再次擴充整個檔。 當快取已設定且只有已更新的投影時，先前擴充之檔的技能集執行不會導致任何認知服務費用。


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

我們現在可以新增 `knowledgeStore` 物件，並視需要設定每個案例的投影。 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>投射到資料表以尋找如 Power BI 的案例

> [!NOTE] 
> 由於「知識存放區」是 Azure 儲存體帳戶，因此資料表投射是 Azure 儲存體資料表，並受到資料表的儲存體限制所控制。如需詳細資訊，請參閱[資料表儲存體限制](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)。 知道實體大小不能超過 1 MB，且單一屬性不能大於 64 KB，這會很有説明。 這些條件約束讓資料表成為儲存大量小型實體的絕佳解決方案。

Power BI 可以從資料表讀取，並根據知識存放區投射所建立的索引鍵來探索關聯性，這讓資料表成為當您嘗試在擴充的資料上建立儀表板時，適合用來投影資料的選項。 假設我們想要建立一個儀表板，讓我們可以將從檔中解壓縮的關鍵字組視覺化為單字雲端，我們可以在技能集中加入塑造器技能，以建立具有檔特定詳細資料和主要片語的自訂圖形。 將整形人員技能加入至技能集，以在 ```document```上建立名為 ```pbiShape``` 的新擴充。

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>使用整形人員技能來建立自訂形狀

建立您可以投影到資料表儲存體的自訂圖形。 如果沒有自訂形狀，投射只能參考單一節點（每個輸出一個投射）。 建立自訂圖形可讓您將各種元素匯總成新的邏輯，這可以投射成單一資料表，或在資料表集合之間進行切割和散發。 在此範例中，「自訂」圖形會結合中繼資料和已識別的實體和主要片語。 物件稱為 pbiShape，而且在 `/document`之下是父代。 

> [!IMPORTANT] 
> 擴充的來源路徑必須是格式正確的 JSON 物件，才能進行預測。 擴充樹狀結構可以代表格式不正確的擴充，例如，當擴充是基本的父代時（如字串）。 請注意，`KeyPhrases` 和 `Entities` 如何包裝到具有 `sourceContext`的有效 JSON 物件中，這是必要的，因為 `keyphrases` 和 `entities` 會在基本專案上擴充，而且需要先轉換成有效的 JSON，才能進行預測。

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
將我們剛才定義的整形者技能加入至技能集中的 [技能] 清單。 

現在，我們已擁有將專案投影到資料表所需的所有資料，請使用資料表定義來更新 knowledgeStore 物件。 

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

將 ```storageConnectionString``` 屬性設為有效的 V2 一般目的儲存體帳戶連接字串。 在此案例中，我們藉由設定 ```tableName```、```source``` 和 ```generatedKeyName``` 屬性，在投射物件中定義三個數據表。 您現在可以發出 PUT 要求來更新技能集。

### <a name="slicing"></a>切割 

從合併圖形開始，其中所有需要投射的內容都是在單一圖形（或擴充節點）中，配量可讓您將單一節點分割成多個資料表或物件。 在這裡，```pbiShape``` 物件會切割成多個資料表。 [切割] 功能可讓您將圖形的各個部分拉出，```keyPhrases``` 和 ```Entities``` 放入不同的資料表。 這很有用，因為多個實體和 keyPhrases 都與每個檔相關聯。 切割 implicity 會使用父資料表中的 ```generatedKeyName```，在子資料工作表中建立具有相同名稱的資料行，以產生父資料表與子資料工作表之間的關聯性。 

### <a name="naming-relationships"></a>命名關聯性
```generatedKeyName``` 和 ```referenceKeyName``` 屬性是用來關聯資料表間的資料，甚至是跨投影類型。 子資料工作表/投射中的每個資料列都有指向父系的屬性。 子系中的資料行或屬性名稱是來自父系的 ```referenceKeyName```。 未提供 ```referenceKeyName``` 時，服務會將其預設為來自父系的 ```generatedKeyName```。 PowerBI 依賴這些產生的金鑰來探索資料表中的關聯性。 如果您需要以不同方式命名的子資料工作表中的資料行，請在父資料表上設定 ```referenceKeyName``` 屬性。 其中一個範例是在 pbiDocument 資料表上將 ```generatedKeyName``` 設定為 ID，並將 ```referenceKeyName``` 設為 DocumentID。 這會導致 pbiEntities 和 pbiKeyPhrases 資料表中的資料行包含名為 DocumentID 的檔識別碼。

儲存已更新的技能集並執行索引子，您現在有一個具有三個數據表的運作中投影。 將這些資料表匯入 Power BI 應該會導致 Power BI 自動探索關聯性。

## <a name="projecting-to-objects"></a>投射至物件

物件投射與資料表投影沒有相同的限制，較適合用來投射大型檔。 在此範例中，我們會將整份檔投影至物件投射。 物件投射僅限於容器中的單一投射。
為了定義物件投射，我們將在投影中使用 ```objects``` 陣列。 您可以使用造型技能或使用物件投射的內嵌成形，產生新的圖形。 雖然資料表範例示範了建立形狀和配量的方法，但此範例示範內嵌成形的用法。 內嵌成形是您在投影的輸入定義中建立新圖形的能力。 內嵌成形所建立的匿名物件，與類似的建立者會產生的內容完全相同。 如果您要定義不打算重複使用的圖形，內嵌成形就很有用。
投射屬性是陣列，在此範例中，我們會將新的投影實例新增至陣列。 以內嵌定義的投影來更新 knowledgeStore 定義，使用內嵌投影時，您不需要整形的技能。

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
## <a name="file-projections"></a>檔案投射

檔案投射是從來源文件中解壓縮的影像，或可從擴充程式中預測的擴充輸出。 檔案投射，類似于物件投影，會實作為 blob 並包含影像。 為了產生檔案投射，我們使用投射物件中的 ```files``` 陣列。 這個範例會將所有從檔解壓縮的影像投射到名為 `samplefile`的容器。

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

## <a name="projecting-to-multiple-types"></a>投射到多個類型

較複雜的案例可能會要求您跨投影類型來投影內容。 例如，如果您需要將一些資料（例如關鍵字組和實體）投影到資料表，請將文字和版面配置文字的 OCR 結果儲存為物件，並將影像投影成檔案。 此技能集的更新將會：

1. 建立每個檔都有一個資料列的資料表。
2. 建立與檔資料表相關的資料表，並將每個主要片語識別為此資料表中的資料列。
3. 建立與檔資料表相關的資料表，並將每個實體識別為此資料表中的資料列。
4. 建立具有每個影像版面配置文字的物件投射。
5. 建立檔案投射，並投射每個已解壓縮的影像。
6. 建立包含檔資料表參考的交互參考資料表、具有版面配置文字的物件投射和檔案投射。

首先，將新的造型技能加入至建立成形物件的技能陣列。 

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

### <a name="relationships"></a>關聯性

這個範例也會反白顯示投影的另一項功能，方法是在同一個投射物件內定義多個投射類型，並在投影的不同類型（資料表、物件、檔案）之間表示關聯性，允許您要從檔的資料表資料列開始，並在物件投射中尋找該檔中影像的所有 OCR 文字。 如果您不想要相關資料，請在不同的投射物件中定義投影，例如，下列程式碼片段會產生相關的資料表，但不會有資料表與 OCR 文字投影之間的關聯性。 當您想要針對不同的需求投影不同圖形中的相同資料時，投射群組會很有用。 例如，Power BI 儀表板的投射群組和另一個投射群組，用於使用資料來定型技能的 AI 模型。
建立不同類型的投影時，會先產生檔案和物件投影，並將路徑加入至資料表。

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

這些範例示範如何使用投影的常見模式，您現在也可以充分瞭解針對特定案例建立投影的概念。

## <a name="common-issues"></a>常見的問題

定義投影時，有幾個常見的問題可能會導致非預期的結果。

1. 不塑造字串擴充。 當擴充字串時（例如 ```merged_content``` 使用主要片語擴充），擴充的屬性會表示為擴充樹狀結構中 merged_content 的子系。 但是在投射時間，這必須轉換成具有名稱和值的有效 JSON 物件。
2. 省略來源路徑結尾的 ```/*```。 例如，如果投影的來源是 ```/document/pbiShape/keyPhrases``` 主要片語陣列投射為單一物件/資料列。 將來源路徑設定為 ```/document/pbiShape/keyPhrases/*``` 會針對每個主要片語產生單一資料列或物件。
3. 路徑選取器會區分大小寫，如果您未對選取器使用正確的大小寫，可能會導致遺漏輸入警告。

