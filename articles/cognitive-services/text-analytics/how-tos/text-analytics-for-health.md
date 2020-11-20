---
title: 如何使用文字分析的健全狀況
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用文字分析的健康狀態，從非結構化臨床文字中解壓縮及標示醫療資訊。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: e7f017c1f3dc189af2b0fc053912decca3459478
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952755"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>How to：使用文字分析的健全狀況 (預覽) 

> [!IMPORTANT] 
> 健康情況的文字分析是以「原樣」和「有所有錯誤」提供的預覽功能。 因此， **文字分析的健全狀況 (預覽) 不應該在任何生產環境中執行或部署。** 健康情況的文字分析不適合或無法作為醫療裝置、臨床支援、診斷工具或其他技術使用，以供診斷、硬化、緩和、治療或預防疾病或其他條件使用，而且 Microsoft 不會授與任何授權或權利以使用這項功能做為這項用途。 這項功能並非設計或部署來取代專業醫療建議或醫療保健專業人員的醫療保健意見、診斷、治療或臨床判斷，也不應該使用這些功能。 客戶須負責任何使用文字分析的健康情況。 Microsoft 不保證文字分析的健康情況或任何與此功能連線所提供的資料，對任何醫療用途都已足夠，或符合任何人的健康或醫療需求。 


健康情況的文字分析是文字分析 API 服務的一項功能，可從非結構化文字（例如醫生的附注、放電摘要、臨床檔和電子健康情況記錄）中，將相關的醫療資訊解壓縮並標示出來。  有兩種方式可以使用此服務： 

* 以 web 為基礎的 API (非同步)  
* Docker 容器 (同步)    

## <a name="features"></a>特性

健康情況的文字分析會執行名為實體辨識的 (NER) 、關聯性解壓縮、實體否定和英文文字上的實體連結，以發掘非結構化臨床和生物醫學文字的見解。

### <a name="named-entity-recognition"></a>[具名實體辨識](#tab/ner)

命名實體辨識會偵測非結構化文字中所提及的單字和片語，這些文字可以與一或多個語義類型相關聯，例如診斷、藥物名稱、徵兆/正負號或年齡。

> [!div class="mx-imgBorder"]
> ![健全狀況 NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[關聯性解壓縮](#tab/relation-extraction)

關聯性解壓縮會識別文字中所述概念之間有意義的連接。 例如，藉由建立條件名稱與時間的關聯，即可找到「條件」關聯。 

> [!div class="mx-imgBorder"]
> ![健全狀況 RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[實體連結](#tab/entity-linking)

實體連結會厘清不同的實體，方法是將文字中提及的命名實體與概念的預先定義資料庫中所找到的概念產生關聯。 例如，統一的醫療語言系統 (UMLS) 。

> [!div class="mx-imgBorder"]
> ![Health EL](../media/ta-for-health/health-entity-linking.png)

文字分析 for health 支援連結至整合醫療語言系統 ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) Metathesaurus 知識來源中找到的健康情況和生物醫學詞彙。

### <a name="negation-detection"></a>[否定偵測](#tab/negation-detection) 

醫療內容的意義會受到負的修飾詞高度影響，如果 misdiagnosed，可能會有重大的含意。 Health 的文字分析支援對文字中提及的不同實體進行否定偵測。 

> [!div class="mx-imgBorder"]
> ![健全狀況 >NEG](../media/ta-for-health/health-negation.png)

---

如需完整的支援實體清單，請參閱文字分析所傳回的 [實體類別](../named-entity-types.md?tabs=health) 以取得健全狀況。

### <a name="supported-languages-and-regions"></a>支援的語言和區域

健康情況的文字分析僅支援英文語言檔。 

健康情況託管 web API 的文字分析目前僅適用于下欄區域：美國西部2、美國東部2、美國中部、北歐和西歐。

## <a name="request-access-to-the-public-preview"></a>要求存取公開預覽

填寫並提交 [認知服務要求表單](https://aka.ms/csgate) ，以要求存取文字分析的健康情況公開預覽。 您將不需支付文字分析的健康情況。 

該表格需要有關您本身、您的公司，以及您將會使用該容器之使用者情節的資訊。 在您提交表單之後，Azure 認知服務小組將會進行審查，並以電子郵件傳送您的決策。

> [!IMPORTANT]
> * 在表單上，您必須使用與 Azure 訂用帳戶識別碼相關聯的電子郵件地址。
> * 您所使用的 Azure 資源必須使用已核准的 Azure 訂用帳戶識別碼來建立。 
> * 檢查電子郵件 (收件匣和垃圾資料夾) ，以取得 Microsoft 應用程式狀態的更新。

## <a name="using-the-docker-container"></a>使用 Docker 容器 

若要在您自己的環境中執行健康情況容器的文字分析，請依照下列 [指示下載並安裝容器](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare)。

## <a name="using-the-client-library"></a>使用用戶端程式庫

文字分析用戶端程式庫的最新發行前版本可讓您使用用戶端物件，呼叫文字分析的健全狀況。 請參閱參考檔，並參閱 GitHub 上的範例：
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>傳送 REST API 要求 

### <a name="preparation"></a>準備

當您提供較少量的文字來處理時，健康情況的文字分析會產生較高品質的結果。 這與某些其他文字分析功能相反，例如關鍵字組解壓縮，在較大的文字區塊上執行效能較佳。 若要取得這些作業的最佳結果，請考慮據以重建輸入。

您必須具有此格式的 JSON 文件：識別碼、文字和語言。 

每份文件的大小必須低於 5,120 個字元。 如需集合中允許的檔數目上限，請參閱概念底下的 [資料限制](../concepts/data-limits.md?tabs=version-3) 文章。 集合會在要求本文中提交。

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>為託管的非同步 web API 的 API 要求結構

針對容器和託管的 web API，您必須建立 POST 要求。 您可以 [使用 Postman](text-analytics-how-to-call-api.md)、捲曲命令或文字分析中的 **api 測試主控台**[來取得健全狀況託管 API 參考](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health)，以在您想要的區域中快速建立 POST 要求並傳送至裝載的 web API。 

以下是附加至健康情況 API 要求的 POST 主體文字分析的 JSON 檔案範例：

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>託管的非同步 web API 回應 

因為此 POST 要求會用來提交非同步作業的作業，所以回應物件中不會有任何文字。  不過，您需要回應標頭中的作業位置索引鍵值，才能提出 GET 要求以檢查作業的狀態和輸出。  以下是 POST 要求的回應標頭中，作業位置金鑰的值範例：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

若要檢查作業狀態，請在 POST 回應的作業位置金鑰標頭值中，對 URL 提出 GET 要求。  下列狀態會用來反映工作的狀態： `NotStarted` 、 `running` 、 `succeeded` 、 `failed` 、、 `rejected` `cancelling` 和 `cancelled` 。  

您可以取消具有或狀態的作業， `NotStarted` `running` 並使用與 GET 要求相同的 URL 的 DELETE HTTP 呼叫。  如需有關刪除呼叫的詳細資訊，請 [參閱文字分析中的健康情況託管 API 參考](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob)。

以下是 GET 要求的回應範例。  請注意，輸出可供抓取，直到 `expirationDateTime` 從) 建立作業開始 (24 小時之後，才會在該時間內清除輸出。

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>為容器的 API 要求結構

您可以 [使用下列 Postman](text-analytics-how-to-call-api.md) 或以下的範例捲曲要求，將查詢提交至您所部署的容器， `serverURL` 並將變數取代為適當的值。  請注意，容器的 URL 中的 API 版本與託管的 API 不同。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

下列 JSON 是附加至健康情況 API 要求的 POST 主體文字分析的 JSON 檔案範例：

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>容器回應主體

下列 JSON 是來自容器化同步呼叫的健康情況 API 回應主體文字分析範例：

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>否定偵測輸出

使用負偵測時，在某些情況下，單一的負詞彙可能會一次處理數個詞彙。 在 JSON 輸出中，已辨識實體的否定會以旗標的布林值表示 `isNegated` ，例如：

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>關聯性解壓縮輸出

關聯性解壓縮輸出包含關聯性 *來源* 及其 *目標* 的 URI 參考。 具有關聯角色的實體 `ENTITY` 會指派給 `target` 欄位。 具有關聯角色的實體 `ATTRIBUTE` 會指派給 `source` 欄位。 縮寫關聯包含雙向 `source` 和 `target` 欄位，而且 `bidirectional` 會設定為 `true` 。 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>另請參閱

* [文字分析概觀](../overview.md)
* [命名實體類別](../named-entity-types.md)
* [新功能](../whats-new.md)
