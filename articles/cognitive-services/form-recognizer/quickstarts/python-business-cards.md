---
title: 快速入門：使用 Python 來擷取名片資料 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將使用表單辨識器 REST API 搭配 Python 來擷取英文名片影像中的資料。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 67a21dd86059f6cf1f017ce3eada285d2faab1e6
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012419"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>快速入門：搭配使用表單辨識器 REST API 和 Python 來擷取名片資料

在本快速入門中，您將搭配使用 Azure 表單辨識器 REST API 與 Python 來擷取及識別名片上的相關資訊。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

若要完成此快速入門，您必須：
- 已安裝 [Python](https://www.python.org/downloads/) (如果您想要在本機執行此範例)。
- 名片的影像。 您可以使用本快速入門的[範例影像](../media/business-card-english.jpg)。

> [!NOTE]
> 本快速入門會使用本機檔案。 若要改為使用透過 URL 存取的遠端名片影像，請參閱[參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)。

## <a name="create-a-form-recognizer-resource"></a>建立表單辨識器資源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>分析名片

若要開始分析名片，請使用下面的 Python 指令碼，呼叫 **[分析名片](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** API。 執行指令碼之前，請進行下列變更：

1. 將 `<endpoint>` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `<path to your business card>` 取代為您名片影像或 PDF 的本機路徑。
1. 將 `<subscription key>` 取代為您在先前的步驟中複製的訂用帳戶金鑰。
1. 以 'image/jpeg'、'image/png'、'application/pdf' 或 'image/tiff' 取代 `<file type>`。

    ```python
    ########### Python Form Recognizer Async Business Cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    content_type = "<file type>"
    
    headers = {
        # Request headers
        'Content-Type': content_type,
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True  # True to output all recognized text
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. 將程式碼儲存在副檔名為 .py 的檔案中。 例如，*form-recognizer-businesscards.py*。
1. 開啟 [命令提示字元] 視窗。
1. 出現提示時，使用 `python` 命令執行範例。 例如： `python form-recognizer-businesscards.py` 。

您會收到包含 **Operation-Location** 標頭的 `202 (Success)` 回應，而指令碼會將其輸出至主控台。 此標頭包含結果識別碼，您可用來查詢長時間執行的作業狀態並取得結果。 在下列範例值中，`operations/` 之後的字串就是結果識別碼。

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>取得名片結果

呼叫 **分析名片** API 之後，您可以呼叫 **[取得分析名片結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** API 來取得作業狀態並擷取資料。 將下列程式碼新增到 Python 指令碼底部。 這會在新的 API 呼叫中使用結果識別碼值。 此指令碼會定期呼叫 API，直到有結果為止。 我們建議的間隔為一秒以上。

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. 儲存指令碼。
1. 再次使用 `python` 命令執行範例。 例如： `python form-recognizer-businesscards.py` 。

### <a name="examine-the-response"></a>檢查回應
![Contoso 公司的名片](../media/business-card-english.jpg)

這個範例說明表單辨識器所傳回的 JSON 輸出。 為了方便閱讀，此值已截斷。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
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
                                "confidence": 0.993
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
                    }
                }
            }
        ]
    }
}
```

指令碼會將回應輸出到主控台，直到 **分析名片** 作業完成。 `"readResults"` 節點包含所有已辨識的文字。 文字會依頁面彙整，然後依文字行，再依個別字組彙整。 `"documentResults"` 節點包含此模型所探索到的名片專屬值。 您可以在這裡找到有用的資訊，例如公司名稱、名字、姓氏、電話號碼等等。


## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用表單辨識器 REST API 搭配 Python 來擷取名片上的內容。 接下來，請參閱參考文件來深入探索表單辨識器 API。

> [!div class="nextstepaction"]
> [REST API 參考文件](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
