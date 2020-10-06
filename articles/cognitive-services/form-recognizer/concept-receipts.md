---
title: 收據-表單辨識器
titleSuffix: Azure Cognitive Services
description: 瞭解使用表單辨識器 API 的接收分析相關概念-使用方式與限制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 0382c7c7f7d068ea227397ae7accf4bc410de04a
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761442"
---
# <a name="receipt-concepts"></a>回條概念

Azure 表單辨識器可以使用其中一個預先建立的模型來分析收據。 回條 API 會以英文從銷售收據中解壓縮重要資訊，例如商家名稱、交易日期、交易總計、明細專案等等。 

## <a name="understanding-receipts"></a>瞭解收據 

許多企業和個人仍依賴手動將資料從其銷售收據中解壓縮，不論是企業支出報表、償還、審核、稅務用途、預算、行銷或其他用途。 通常在這些情況下，需要實體回條的影像以供驗證之用。  

從這些收據自動解壓縮資料可能很複雜。 收據可能會 crumpled 且難以閱讀、列印或手寫的零件，以及收據的智慧型手機影像可能低品質。 此外，每個市場、區域和商家的收據範本和欄位可能會有極大的差異。 這兩種資料提取和現場偵測的挑戰，是因為有一個獨特的問題。  

使用光學字元辨識 (OCR) 和預建的收據模型，收據 API 會啟用這些收據處理案例，並從收據中提取資料，例如商家名稱、秘訣、總計、明細專案等等。 使用此 API 時，您不需要定型模型，您只需要將回條傳送給分析收據 API，資料就會被解壓縮。

![範例收據](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>收據 API 有哪些功能？ 

預建的收據 API 會將銷售收據的內容 &mdash; ，與您通常會在餐廳、零售商或雜貨商店取得的收據類型進行解壓縮。

### <a name="fields-extracted"></a>已解壓縮的欄位

* 商家名稱 
* 商家位址 
* 商家電話號碼 
* 交易日期 
* 異動時間 
* 小計 
* 稅金 
* 總計 
* 提示 
* 明細專案的解壓縮 (例如專案數量、專案價格、專案名稱) 

### <a name="additional-features"></a>其他功能

收據 API 也會傳回下列資訊：

* 收據類型 (例如明細、信用卡等) 
* 欄位信賴等級 (每個欄位都會傳回相關聯的信賴值) 
* OCR 原始文字 (OCR 將整個收據的文字輸出解壓縮) 
* 每個值、行和單字的周框方塊

## <a name="input-requirements"></a>輸入需求

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>支援的地區設定 

* **預先建立** 的回條2.0 版 (GA) 支援 en-us 地區設定的銷售收據
* **預先建立的收據 2.1-preview。 1** (公開預覽) 新增下列 EN 回條地區設定的額外支援： 
  * EN-US 
  * EN-CA 
  * EN-GB 
  * EN-US 

  > [!NOTE]
  > 語言輸入 
  >
  > 預建的收據 2.1-preview。1有選擇性的要求參數，可指定其他英文市場的收據地區設定。 若為美式英文的銷售收據 (EN-US) 、加拿大 (EN-US) 、英國 (半 GB) 和印度 () ，您可以指定地區設定以取得改進的結果。 如果在 2.1-preview. 1 中未指定地區設定，此模型將會預設為 EN-US 模型。


## <a name="the-analyze-receipt-operation"></a>分析收據操作

「 [分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeReceiptAsync) 回條」會使用收據的影像或 PDF 作為輸入，並解壓縮感興趣的值和文字。 呼叫會傳回稱為的回應標頭欄位 `Operation-Location` 。 `Operation-Location`值是包含要在下一個步驟中使用之結果識別碼的 URL。

|回應標頭| 結果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>取得分析收據結果操作

第二個步驟是呼叫「 [取得分析收據結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeReceiptResult) 」作業。 這項作業會以「分析回條」作業所建立的結果識別碼作為輸入。 它會傳回 JSON 回應，其中包含具有下列可能值的 **狀態** 欄位。 您會反復呼叫此操作，直到它傳回 **成功** 的值為止。 使用3到5秒的間隔，以避免每秒超過要求 (RPS) 速率。

|欄位| 類型 | 可能值 |
|:-----|:----:|:----|
|status | 字串 | notStarted：分析作業尚未啟動。 |
| |  | 正在執行：分析作業正在進行中。 |
| |  | 失敗：分析作業已失敗。 |
| |  | 成功：分析作業已成功。 |

當 [ **狀態** ] 欄位的值為 [ **成功** ] 時，JSON 回應會包含接收理解和文字辨識結果。 接收理解結果會組織為命名域值的字典，其中每個值都包含已解壓縮的文字、正規化值、周框方塊、信賴度和對應的單字元素。 文字辨識結果會組織成行和單字的階層，其中包含文字、周框方塊和信賴資訊。

![範例收據結果](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>範例 JSON 輸出

請參閱下列成功 JSON 回應的範例： "readResults" 節點包含所有已辨識的文字。 文字會依頁面彙整，然後依文字行，再依個別字組彙整。 "DocumentResults" 節點包含模型所發現的特定名片值。 您可以在這裡找到有用的索引鍵/值組，例如名字、姓氏、公司名稱等等。

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```


## <a name="customer-scenarios"></a>客戶案例  

使用接收 API 所解壓縮的資料可以用來執行各種工作。 以下是我們的客戶使用收據 API 完成的一些範例。 

### <a name="business-expense-reporting"></a>商務支出報告  

提出業務費用通常需要花費時間從收據的影像手動輸入資料。 使用接收 API，您可以使用已解壓縮的欄位來部分自動化此程式，並快速分析您的收據。  

因為接收 API 具有簡單的 JSON 輸出，所以您可以用多種方式使用已解壓縮的域值。 與內部費用應用程式整合，以預先填入 expense reports。 如需此案例的詳細資訊，請參閱 Acumatica 如何運用回條 API， [讓支出回報較不困難的流程](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure)。  

### <a name="auditing-and-accounting"></a>審核和帳戶處理 

收據 API 輸出也可以用來在支出報告和補償程式中的各個點上，執行大量費用的分析。 您可以處理收據以進行手動審核或快速核准。  

收據輸出也適用于一般書籍，以供商務或個人使用。 使用回條 API，將任何原始收據影像/PDF 資料轉換成可採取動作的數位輸出。

### <a name="consumer-behavior"></a>消費者行為 

回條包含有用的資料，可供您用來分析消費者行為和購物趨勢。

收據 API 也會為 [>aibuilder 收據處理功能](https://docs.microsoft.com/ai-builder/prebuilt-receipt-processing)提供技術支援。

## <a name="next-steps"></a>後續步驟

- 完成 [表單辨識器用戶端程式庫快速入門](quickstarts/client-library.md) ，以使用您所選語言的表單辨識器來開始撰寫收據處理應用程式。
- 或者，遵循 [收據 API Python 快速入門](./quickstarts/python-receipts.md) ，使用 REST API 辨識收據。

## <a name="see-also"></a>請參閱

* [什麼是表單辨識器？](./overview.md)
* [REST API 參考檔](https://docs.microsoft.com/azure/cognitive-services/form-recognizer)
