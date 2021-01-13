---
title: 名片-表單辨識器
titleSuffix: Azure Cognitive Services
description: 瞭解使用表單辨識器 API 的名片分析相關概念-使用方式與限制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 1fd4279cd35e54e2e04f88973c4a825218a75142
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131115"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>表單辨識器預建名片模型 

Azure 表單辨識器可以使用其預先建立的名片模型，分析和解壓縮名片中的連絡人資訊。 它結合了強大的光學字元辨識 (OCR) 功能，以及我們的名片理解模型，以英文的方式從名片中取出重要資訊。 它會解壓縮個人連絡人資訊、公司名稱、職稱等等。 預先建立的名片 API 可在表單辨識器2.1 版 preview 中公開使用。 

## <a name="what-does-the-business-card-service-do"></a>名片服務的用途為何？

預先建立的名片 API 會將名片中的索引鍵欄位解壓縮，並在組織的 JSON 回應中傳回這些欄位。

![來自 FOTT + JSON 輸出的 Contoso 明細影像](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>已解壓縮的欄位：

|名稱| 類型 | 描述 | 文字 | 
|:-----|:----|:----|:----|
| ContactNames | 物件的陣列 | 從名片解壓縮的連絡人名稱 | [{"FirstName"： "John"，"LastName"： "Doe"}] |
| FirstName | 字串 | 第一個 (指定的 contact) 名稱 | "John" | 
| LastName | 字串 | 連絡人的最後 (系列) 名稱 |   "Doe" | 
| CompanyNames | 字串陣列 | 從商業智慧卡解壓縮的公司名稱 | ["Contoso"] | 
| 部門 | 字串陣列 | 連絡人的部門或組織 | ["R&D"] | 
| JobTitles | 字串陣列 | 列出連絡人的職稱 | [軟體工程師] | 
| 電子郵件 | 字串陣列 | 從商業智慧卡解壓縮的連絡人電子郵件 | ["johndoe@contoso.com"] | 
| 網站 | 字串陣列 | 從名片解壓縮的網站 | ["https://www.contoso.com"] | 
| 位址 | 字串陣列 | 從名片解壓縮的位址 | ["123 的主要街道，Redmond，WA 98052"] | 
| MobilePhones | 電話號碼陣列 | 從名片解壓縮的行動電話號碼 | ["+ 19876543210"] |
| 傳真 | 電話號碼陣列 | 從名片解壓縮的傳真呼叫號碼 | ["+ 19876543211"] |
| WorkPhones | 電話號碼陣列 | 從名片解壓縮的公司電話號碼 | ["+ 19876543231"] |
| OtherPhones    | 電話號碼陣列 | 從名片中解壓縮的其他電話號碼 | ["+ 19876543233"] |


名片 API 也可以從名片傳回所有已辨識的文字。 此 OCR 輸出包含在 JSON 回應中。  

### <a name="input-requirements"></a>輸入需求 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>分析名片操作

「 [分析名片](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync) 」會使用名片的影像或 PDF 作為輸入，並將感興趣的值解壓縮。 呼叫會傳回稱為的回應標頭欄位 `Operation-Location` 。 `Operation-Location`值是包含要在下一個步驟中使用之結果識別碼的 URL。

|回應標頭| 結果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>「取得分析商務卡片」結果操作

第二個步驟是呼叫「 [取得分析商務卡片」結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult) 作業。 這項作業會以「分析名片」作業所建立的結果識別碼作為輸入。 它會傳回 JSON 回應，其中包含具有下列可能值的 **狀態** 欄位。 您會反復呼叫此操作，直到它傳回 **成功** 的值為止。 使用3到5秒的間隔，以避免每秒超過要求 (RPS) 速率。

|欄位| 類型 | 可能值 |
|:-----|:----:|:----|
|status | 字串 | notStarted：分析作業尚未啟動。<br /><br />正在執行：分析作業正在進行中。<br /><br />失敗：分析作業已失敗。<br /><br />成功：分析作業已成功。|

當 [ **狀態** ] 欄位的值為 [ **成功** ] 時，如果要求的話，JSON 回應會包含名片理解和選擇性的文字辨識結果。 名片理解結果會組織為命名域值的字典，其中每個值都包含已解壓縮的文字、正規化值、周框方塊、信賴度和對應的單字元素。 文字辨識結果會組織成行和單字的階層，其中包含文字、周框方塊和信賴資訊。

![範例名片輸出](./media/business-card-results.png)

### <a name="sample-json-output"></a>範例 JSON 輸出

請參閱下列成功 JSON 回應的範例： "readResults" 節點包含所有已辨識的文字。 文字會依頁面彙整，然後依文字行，再依個別字組彙整。 "DocumentResults" 節點包含模型所發現的特定名片值。 您可以在這裡找到有用的連絡人資訊，例如名字、姓氏、公司名稱等等。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

遵循 [快速](./QuickStarts/client-library.md) 入門快速入門，使用 Python 和 REST API 來執行名片資料解壓縮。

## <a name="customer-scenarios"></a>客戶案例  

使用名片 API 所解壓縮的資料可以用來執行各種工作。 將此連絡人資訊解壓縮可自動為用戶端對向角色中的時間節省時間。 以下是我們的客戶使用名片 API 完成的一些範例：

* 從名片中取出連絡人資訊，並快速建立電話連絡人。 
* 與 CRM 整合，以使用名片影像自動建立連絡人。 
* 追蹤銷售潛在客戶。  
* 從現有的名片影像大量解壓縮連絡人資訊。 

名片 API 也可 [AI Builder 名片處理功能](/ai-builder/prebuilt-business-card)。

## <a name="next-steps"></a>後續步驟

- 遵循 [快速入門](./quickstarts/client-library.md) 以開始辨識名片。

## <a name="see-also"></a>請參閱

* [什麼是表單辨識器？](./overview.md)
* [REST API 參考檔](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
