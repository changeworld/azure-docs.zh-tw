---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 426158945e609b2bb46dd9fbbbbe378f25cd93f1
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206221"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>分析索引鍵/值組和資料表的表單

接下來，您會使用新定型的模型來分析文件，並從中擷取索引鍵/值組和資料表。 在新的 Python 指令碼中執行下列程式碼，以呼叫 **[分析表單](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeWithCustomForm)** API。 執行指令碼之前，請進行下列變更：

1. 將 `<file path>` 取代為表單的檔案路徑 (例如，C:\temp\file.pdf)。 這也可以是遠端檔案的 URL。 在本快速入門中，您可以使用[範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)中 **Test** 資料夾底下的檔案。
1. 將 `<model_id>` 取代為您在上一節中取得的模型識別碼。
1. 將 `<endpoint>` 取代為您使用表單辨識器訂用帳戶金鑰取得的端點。 您可以在表單辨識器的資源 [概觀]  索引標籤上找到此項目。
1. 將 `<file type>` 取代為檔案類型。 支援的類型：`application/pdf`、`image/jpeg`、`image/png`、`image/tiff`。
1. 將 `<subscription key>` 取代為訂用帳戶金鑰。

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. 將程式碼儲存在副檔名為 .py 的檔案中。 例如，*form-recognizer-analyze.py*。
1. 開啟 [命令提示字元] 視窗。
1. 出現提示時，使用 `python` 命令執行範例。 例如： `python form-recognizer-analyze.py` 。

當您呼叫**分析表單** API 時，您會收到 **Operation-Location** 標頭的 `201 (Success)` 回應。 此標頭的值是您將用來追蹤分析作業結果的識別碼。 上述指令碼會將此標頭的值列印到主控台。

## <a name="get-the-analyze-results"></a>取得分析結果

將下列程式碼新增到 Python 指令碼底部。 這會在新的 API 呼叫中使用先前呼叫中的識別碼值來擷取分析結果。 **分析表單**作業是非同步的，因此此指令碼會定時呼叫 API，直到有結果為止。 我們建議的間隔為一秒以上。

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
