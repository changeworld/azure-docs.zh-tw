---
title: 快速入門：使用 REST API 和 Python 來以標籤定型 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 了解如何搭配使用表單辨識器加上標籤的資料功能與 REST API 和 Python 來定型自訂模型。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: f944a793d721e93d818723eae25a9ce80d9c15bc
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005075"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>使用 REST API 和 Python 以標籤定型表單辨識器模型

在本快速入門中，您將搭配使用表單辨識器 REST API 與 Python，來以手動加上標籤的資料定型自訂模型。 若要深入了解此功能，請參閱概觀的[以標籤定型](../overview.md#train-with-labels)一節。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>必要條件

若要完成此快速入門，您必須：
- 已安裝 [Python](https://www.python.org/downloads/) (如果您想要在本機執行此範例)。
- 至少有六個相同類型的表單。 您將使用此資料來定型模型和測試表單。 您可以使用本快速入門的[範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)。 下載 *sample_data .zip* 並將其解壓縮。 將訓練檔案上傳至標準效能層級 Azure 儲存體帳戶中 Blob 儲存體容器的根目錄。

> [!NOTE]
> 本快速入門使用 URL 存取的遠端文件。 若要改為使用本機檔案，請參閱 [v2.0 的參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)和 [v2.1 的參考文件](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/TrainCustomModelAsync)。

## <a name="create-a-form-recognizer-resource"></a>建立表單辨識器資源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>設定定型資料

接下來，您必須設定必要的輸入資料。 加上標籤的資料功能，除了用以定型無標籤自訂模型的需求外，還有特殊的輸入需求。

請確定所有定型文件都有相同的格式。 如果您的表單有多種格式，請根據共同的格式將這些表單組織成子資料夾。 在定型時，您必須將 API 導向子資料夾。

若要使用加上標籤的資料來定型模型，您需要擁有下列檔案來作為子資料夾中的輸入。 您會在下面了解如何建立這些檔案。

* **來源表單** – 要從中擷取資料的表單。 支援的類型有 JPEG、PNG、PDF 或 TIFF。
* **OCR 版面配置檔案** - 為 JSON 檔案，這些檔案可描述每個來源表單中所有可讀文字的大小與位置。 您將使用表單辨識器版面配置 API 來產生此資料。 
* **標籤檔案** - 為 JSON 檔案，這些檔案可描述使用者手動輸入的資料標籤。

上述這些檔案全都應該佔用相同的子資料夾，並採用下列格式：

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> 當您使用表單辨識器的[範例標籤工具](./label-tool.md)來為表單加上標籤時，此工具會自動建立這些標籤和 OCR 版面配置檔案。

### <a name="create-the-ocr-output-files"></a>建立 OCR 輸出檔案

您需要有 OCR 結果檔案，服務才會考慮將對應的輸入檔用於加上標籤的定型。 若要取得指定來源表單的 OCR 結果，請遵循下列步驟：

1. 在讀取的版面配置容器上，讓輸入檔作為要求本文的一部分來呼叫 **[分析版面配置](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** API。 儲存在回應的 **Operation-Location** 標頭中找到的識別碼。
1. 使用上一個步驟中的作業識別碼，呼叫 **[取得分析版面配置結果](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** API。
1. 取得回應並將內容寫入檔案中。 針對每個來源表單，相對應的 OCR 檔案應該在原始檔案名稱後面附加 `.ocr.json`。 OCR JSON 輸出應具有下列格式。 如需完整範例，請參閱[範例 OCR 檔案](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json)。 

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    
    # <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    


    ---



### <a name="create-the-label-files"></a>建立標籤檔案

標籤檔案會包含使用者手動輸入的索引鍵/值關聯。 已加上標籤的資料定型需要這些關聯，但並非每個來源檔案都必須有對應的標籤檔案。 系統會將沒有標籤的原始檔視為一般定型文件。 建議您使用五個以上的加上標籤檔案，以便獲得可靠的定型。 您可以使用像是[範例標籤工具](./label-tool.md)之類的 UI 工具，產生這些檔案。

您在建立標籤檔案時，可以在文件上選擇性地指定區域，也就是值的確切位置。 這會讓定型的精確度提高。 區域會格式化成一組八個值，並分別對應到四個 X,Y 座標：左上角、右上角、右下角和左下角。 座標值介於 0 與 1 之間，並會調整為頁面的尺寸。

針對每個來源表單，相對應的標籤檔案應該在原始檔案名稱後面附加 `.labels.json`。 標籤檔案應具有下列格式。 如需完整範例，請參閱[範例標籤檔案](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json)。

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> 每個文字元素只能套用一個標籤，而且每個頁面只能將每個標籤套用一次。 您無法將標籤套用到多個頁面。


## <a name="train-a-model-using-labeled-data"></a>使用加上標籤的資料來定型模型

若要使用加上標籤的資料來定型模型，請執行下列 Python 程式碼，以呼叫 **[定型自訂模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API。 執行程式碼之前，請進行下列變更：

1. 將 `<Endpoint>` 取代為您表單辨識器資源的端點 URL。
1. 將 `<SAS URL>` 取代為 Azure Blob 儲存體容器的共用存取簽章 (SAS) URL。 若要擷取 SAS URL，請開啟 Microsoft Azure 儲存體總管、以滑鼠右鍵按一下您的容器，然後選取 [取得共用存取簽章]。 確定 [讀取] 和 [列出] 權限均已勾選，再按一下 [建立]。 然後，複製 [URL] 區段的值。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
1. 以輸入資料所在 Blob 容器中的資料夾名稱取代 `<Blob folder name>`。 或者，如果資料位於根目錄，則請讓其保持空白，並從 HTTP 要求的本文中移除 `"prefix"` 欄位。

# <a name="v20"></a>[v2.0](#tab/v2-0)
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```    
# <a name="v21-preview"></a>[v2.1 預覽](#tab/v2-1)    
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.1-preview.2/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```   

---



## <a name="get-training-results"></a>取得定型結果

在您開始定型作業之後，請使用傳回的識別碼來取得作業的狀態。 將下列程式碼新增到 Python 指令碼底部。 這會在新的 API 呼叫中，使用來自定型呼叫的識別碼值。 定型作業採非同步方式進行，因此這個指令碼會定期呼叫 API，直到定型狀態完成為止。 我們建議的間隔為一秒以上。

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

定型程序完成時，您會收到有如下 JSON 內容的 `201 (Success)` 回應。 為了簡單起見，我們已將回應內容縮短。

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

請複製 `"modelId"` 值，以便在下列步驟中使用。

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

程序完成時，您會收到有 JSON 內容的 `202 (Success)` 回應，且該內容採用下列格式。 為了簡單起見，我們已將回應內容縮短。 主要的索引鍵/值關聯位於 `"documentResults"` 節點中。 `"selectionMarks"` 節點 (在 v2.1 預覽中) 會顯示每個選取標記 (核取方塊、選項標記)，以及其狀態是「已選取」還是「未選取」。 版面配置 API 結果 (文件中所有文字的內容和位置) 位於 `"readResults"` 節點中。

# <a name="v20"></a>[v2.0](#tab/v2-0)
```json
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:16:28Z",
  "lastUpdatedDateTime": "2020-08-21T02:16:35Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "boundingBox": [
              0.5826,
              0.4411,
              2.3387,
              0.4411,
              2.3387,
              0.7969,
              0.5826,
              0.7969
            ],
            "text": "Contoso, Ltd.",
            "words": [
              {
                "boundingBox": [
                  0.5826,
                  0.4411,
                  1.744,
                  0.4411,
                  1.744,
                  0.7969,
                  0.5826,
                  0.7969
                ],
                "text": "Contoso,",
                "confidence": 1
              },
              {
                "boundingBox": [
                  1.8448,
                  0.4446,
                  2.3387,
                  0.4446,
                  2.3387,
                  0.7631,
                  1.8448,
                  0.7631
                ],
                "text": "Ltd.",
                "confidence": 1
              }
            ]
          },
          ...
            ]
          }
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "rowIndex": 0,
                "columnIndex": 0,
                "text": "Training Date",
                "boundingBox": [
                  0.5133,
                  4.2167,
                  1.7567,
                  4.2167,
                  1.7567,
                  4.4492,
                  0.5133,
                  4.4492
                ],
                "elements": [
                  "#/readResults/0/lines/14/words/0",
                  "#/readResults/0/lines/14/words/1"
                ]
              },
              ...
            ]
          },
        ]
      }
    ],
    "documentResults": [
      {
        "docType": "custom:form",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Receipt No": {
            "type": "string",
            "valueString": "9876",
            "text": "9876",
            "page": 1,
            "boundingBox": [
              7.615,
              1.245,
              7.915,
              1.245,
              7.915,
              1.35,
              7.615,
              1.35
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/3/words/3"
            ]
          },
          ...
        }
      }
    ],
    "errors": []
  }
}
```
# <a name="v-2"></a>[v 2](#tab/v2-1) 
```json   
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:29:42Z",
  "lastUpdatedDateTime": "2020-08-21T02:29:50Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "boundingBox": [
              0.5826,
              0.4411,
              2.3387,
              0.4411,
              2.3387,
              0.7969,
              0.5826,
              0.7969
            ],
            "text": "Contoso, Ltd.",
            "words": [
              {
                "boundingBox": [
                  0.5826,
                  0.4411,
                  1.744,
                  0.4411,
                  1.744,
                  0.7969,
                  0.5826,
                  0.7969
                ],
                "text": "Contoso,",
                "confidence": 1
              },
              {
                "boundingBox": [
                  1.8448,
                  0.4446,
                  2.3387,
                  0.4446,
                  2.3387,
                  0.7631,
                  1.8448,
                  0.7631
                ],
                "text": "Ltd.",
                "confidence": 1
              }
            ]
          },
          ...
        ], 
        "selectionMarks": [
          {
            "boundingBox": [
              3.9737,
              3.7475,
              4.1693,
              3.7475,
              4.1693,
              3.9428,
              3.9737,
              3.9428
            ],
            ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "rowIndex": 0,
                "columnIndex": 0,
                "text": "Training Date",
                "boundingBox": [
                  0.5133,
                  4.2167,
                  1.7567,
                  4.2167,
                  1.7567,
                  4.4492,
                  0.5133,
                  4.4492
                ],
                "elements": [
                  "#/readResults/0/lines/12/words/0",
                  "#/readResults/0/lines/12/words/1"
                ]
              },
              ...
            ]
          }
        ] 
      }
    ], 
    "documentResults": [
      {
        "docType": "custom:e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "modelId": "e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "ID #": {
            "type": "string",
            "valueString": "5554443",
            "text": "5554443",
            "page": 1,
            "boundingBox": [
              2.315,
              2.43,
              2.74,
              2.43,
              2.74,
              2.515,
              2.315,
              2.515
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/8/words/1"
            ]
          },
          ...
        },
        "docTypeConfidence": 1
      }
    ],
    "errors": []
  }
}
```

---


## <a name="improve-results"></a>改善結果

檢查 `"documentResults"` 節點下每個索引鍵/值結果的 `"confidence"` 值。 您也應該查看 `"readResults"` 節點中的信賴分數，其對應至版面配置作業。 版面配置結果的信賴度並不會影響索引鍵/值擷取結果的信賴度，因此您應該同時檢查這兩者。
* 如果版面配置作業的信賴分數很低，請嘗試改善輸入文件的品質 (請參閱[輸入需求](../overview.md#input-requirements))。
* 如果索引鍵/值擷取作業的信賴分數很低，請確定所分析的文件與訓練集中使用的文件類型相同。 如果訓練集中的文件有外觀變化，請考慮將其劃分成不同的資料夾，並針對每個變化訓練一個模型。

### <a name="avoid-cluttered-labels"></a>避免雜亂的標籤

有時候當您在同一行文字內套用不同標籤時，服務可能會將這些標籤合併成一個欄位。 例如在地址中，您可能會將城市、州別和郵遞區號標記為不同欄位，但在預測期間，這些欄位並無法個別辨識。

我們了解這種情況對客戶來說很重要，因此正努力要在日後改善這項功能。 目前我們會建議使用者將多個雜亂的欄位標記為一個欄位，然後在擷取結果的後置處理中將這些詞彙分隔開來。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何搭配使用表單辨識器 REST API 與 Python，來以手動加上標籤的資料定型模型。 接下來，請參閱 API 參考文件來深入探索表單辨識器 API。

> [!div class="nextstepaction"]
> [REST API 參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
