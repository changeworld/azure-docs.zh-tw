---
title: 快速入門：使用異常偵測器 REST API 與 Python 偵測批次異常行為
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，使用 Anomaly Detector API 來偵測資料序列中的異常狀況 (以批次或串流資料的方式)。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 7bfe10ea5e0e95bcabf02243bb8b7172a5aec08d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90906740"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>快速入門：使用異常偵測器 REST API 與 Python 偵測時間序列資料中的異常行為

使用本快速入門以開始使用 Anomaly Detector API 的兩個偵測模式，來偵測時間序列資料中的異常狀況。 此 Python 應用程式會傳送包含 JSON 格式時間序列資料的 API 要求，並取得回應。

| API 要求                                        | 應用程式輸出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 以批次方式偵測異常狀況                        | JSON 回應包含時間序列資料中每個資料點的異常狀態 (和其他資料)，以及偵測到的任何異常狀況的位置。 |
| 偵測最新資料點的異常狀態 | JSON 回應包含時間序列資料中最新資料點的異常狀態 (和其他資料)。|
| 偵測標示新資料趨勢的變更點 | JSON 回應包含時間序列資料中偵測到的變更點。 |

 雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 您可以在 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py) 上找到此快速入門的原始程式碼。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
- 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="建立異常偵測器資源"  target="_blank">建立異常偵測器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    - 您需要來自所建立資源的金鑰和端點，以將應用程式連線至異常偵測器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
- [Python 2.x 或 3.x](https://www.python.org/downloads/)
- Python 的[要求程式庫](https://pypi.org/project/requests/)

- JSON 檔案包含時間序列資料點。 此快速入門的範例資料可以在 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) 上找到。

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>建立新的應用程式

1. 建立新的 Python 檔案，並新增下列匯入項目。

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. 針對您的訂用帳戶金鑰和端點建立變數。 以下是您可以用於異常偵測的 URI。 這些稍後將會附加至您的服務端點，以建立 API 要求 URL。

    |偵測方法  |URI  |
    |---------|---------|
    |批次偵測    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |最新資料點上的偵測     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    | 變更點偵測 | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. 開啟檔案並使用 `json.load()` 來讀取 JSON 資料檔案。

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立名為 `send_request()` 的新函式，取用上面建立的變數。 然後執行下列步驟。

2. 建立要求標頭的字典。 將 `Content-Type` 設為 `application/json`，並將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

3. 使用 `requests.post()` 傳送要求。 針對完整要求 URL 合併您的端點與異常偵測 URL，並包含標頭和 json 要求資料。 然後傳回回應。

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>以批次方式偵測異常狀況

1. 建立名為 `detect_batch()` 的方法，以批次方式偵測整個資料的異常狀況。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方建立的 `send_request()` 方法。

2. 在結果上呼叫 `json.dumps()` 以將其格式化，並列印到主控台。

3. 如果回應包含 `code` 欄位，則列印錯誤碼和錯誤訊息。

4. 否則，在資料集中尋找異常狀況的位置。 回應的 `isAnomaly` 欄位包含與指定的資料點是否為異常相關的布林值。 逐一查看清單，並列印任何 `True` 值的索引。 如果有找到，這些值會對應到異常資料點的索引。

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測最新資料點的異常狀態

1. 建立名為 `detect_latest()` 的方法，以判斷時間序列中最新的資料點是否為異常。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方的 `send_request()` 方法。

2. 在結果上呼叫 `json.dumps()` 以將其格式化，並列印到主控台。

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="detect-change-points-in-the-data"></a>偵測資料中的變更點

1. 建立名為 `detect_change_point()` 的方法，以批次方式偵測整個資料的異常狀況。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方建立的 `send_request()` 方法。

2. 在結果上呼叫 `json.dumps()` 以將其格式化，並列印到主控台。

3. 如果回應包含 `code` 欄位，則列印錯誤碼和錯誤訊息。

4. 否則，在資料集中尋找異常狀況的位置。 回應的 `isChangePoint` 欄位包含布林值，指出指定的資料點是否異常。 逐一查看清單，並列印任何 `True` 值的索引。 如果有找到這些值，則會對應到趨勢變更點的索引。

    [!code-python[detect change points](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectChangePoint)]

## <a name="send-the-request"></a>傳送要求

呼叫上面所建立的異常偵測方法。

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>範例回應

成功的回應會以 JSON 格式傳回。 按一下以下連結，在 GitHub 上檢視 JSON 回應：
* [批次偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新資料點偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [變更資料點偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
