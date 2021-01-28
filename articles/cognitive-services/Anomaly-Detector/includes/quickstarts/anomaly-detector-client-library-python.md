---
title: 異常偵測器 Python 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: f6206ad2f88983396fa7d0be323daad327e4d235
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947915"
---
開始使用適用於 Python 的 Anomaly Detector 用戶端程式庫。 請依照下列步驟，開始使用服務提供的演算法安裝套件。 Anomaly Detector 服務可藉由自動對時間序列資料使用最適合的模型，而讓您找出其中的異常狀況，不論是什麼產業、情境或資料量都沒問題。

使用適用於 Python 的 Anomaly Detector 用戶端程式庫來：

* 以批次要求方式偵測整個時間序列資料集的異常狀況
* 偵測您的時間序列中最新資料點的異常狀態
* 偵測您資料集內的趨勢變更點。

[程式庫參考文件](https://go.microsoft.com/fwlink/?linkid=2090370) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [套件 (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [在 GitHub 上尋找範例程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector/samples)

## <a name="prerequisites"></a>Prerequisites

* [Python 3.x](https://www.python.org/)
* [Pandas 資料分析程式庫](https://pandas.pydata.org/)
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="建立異常偵測器資源"  target="_blank">建立異常偵測器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至異常偵測器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。


## <a name="setting-up"></a>設定

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

 建立新的 Python 檔案，並匯入下列程式庫。

```python
import os
from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectRequest, TimeSeriesPoint, TimeGranularity, \
    AnomalyDetectorError
from azure.core.credentials import AzureKeyCredential
import pandas as pd
```

為您的金鑰建立變數作為環境變數、時間序列資料檔案的路徑，以及您訂用帳戶的 Azure 位置。 例如： `westus2` 。

```python
SUBSCRIPTION_KEY = os.environ["ANOMALY_DETECTOR_KEY"]
ANOMALY_DETECTOR_ENDPOINT = os.environ["ANOMALY_DETECTOR_ENDPOINT"]
TIME_SERIES_DATA_PATH = os.path.join("./sample_data", "request-data.csv")
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後，您可以透過以下項目安裝用戶端程式庫：

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>物件模型

Anomaly Detector 用戶端是一種 [AnomalyDetectorClient](https://github.com/Azure/azure-sdk-for-python/blob/0b8622dc249969c2f01c5d7146bd0bb36bb106dd/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector/azure/cognitiveservices/anomalydetector/_anomaly_detector_client.py) 物件，其使用您的金鑰向 Azure 進行驗證。 用戶端可以使用 [detect_entire_series](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L26) 在整個資料集上進行異常偵測，或使用 [detect_last_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L87) 在最新資料點上進行異常偵測。 [detect_change_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/aio/operations_async/_anomaly_detector_client_operations_async.py#L142) 函式會偵測標記趨勢變更的點。

時間序列資料會以 [TimeSeriesPoints](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L370) 物件序列的形式來傳送。 `DetectRequest` 物件包含用來說明資料的屬性 (例如，`TimeGranularity`)，以及供異常偵測使用的參數。

Anomaly Detector 回應是 [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse)、[EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) 或 [ChangePointDetectResponse](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L107) 物件 (端視所使用的方法而定)。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 Python 的 Anomaly Detector 用戶端程式庫來執行下列動作：

* [驗證用戶端](#authenticate-the-client)
* [從檔案載入時間序列資料集](#load-time-series-data-from-a-file)
* [偵測整個資料集內的異常](#detect-anomalies-in-the-entire-data-set)
* [偵測最新資料點的異常狀態](#detect-the-anomaly-status-of-the-latest-data-point)
* [偵測資料集內的變更點](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>驗證用戶端

將您的 Azure 位置變數新增至端點，並使用您的金鑰驗證用戶端。

```python
client = AnomalyDetectorClient(AzureKeyCredential(SUBSCRIPTION_KEY), ANOMALY_DETECTOR_ENDPOINT)
```

## <a name="load-time-series-data-from-a-file"></a>從檔案載入時間序列資料集

從 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) 下載此快速入門的資料範例：
1. 在瀏覽器中以滑鼠右鍵按一下 [原始]。
2. 按一下 [另存連結]。
3. 以 .csv 檔案的形式將該檔案另儲到應用程式目錄中。

此時間序列資料會格式化為 .csv 檔案，且會傳送至 Anomaly Detector API。

使用 Pandas 程式庫的 `read_csv()` 方法載入您的資料檔案，並製作空白清單變數來儲存您的資料序列。 逐一查看檔案，並附加資料作為 `TimeSeriesPoint` 物件。 此物件會包含 .csv 資料檔的資料列中的時間戳記和數值。

```python
series = []
data_file = pd.read_csv(TIME_SERIES_DATA_PATH, header=None, encoding='utf-8', parse_dates=[0])
for index, row in data_file.iterrows():
    series.append(TimeSeriesPoint(timestamp=row[0], value=row[1]))
```

使用您的時間序列，以及其資料點的`TimeGranularity` (或週期性)，建立 `DetectRequest` 物件。 例如： `TimeGranularity.daily` 。

```python
request = DetectRequest(series=series, granularity=TimeGranularity.daily)
```

## <a name="detect-anomalies-in-the-entire-data-set"></a>偵測整個資料集內的異常

使用用戶端的 `detect_entire_series` 方法，呼叫 API 來偵測整個時間序列資料的異常情形。 儲存傳回的 [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) 物件。 逐一查看回應的 `is_anomaly` 清單，然後列印任何 `true` 值的索引。 如果有找到，這些值會對應到異常資料點的索引。

```python
print('Detecting anomalies in the entire time series.')

try:
    response = client.detect_entire_series(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_anomaly):
    print('An anomaly was detected at index:')
    for i, value in enumerate(response.is_anomaly):
        if value:
            print(i)
else:
    print('No anomalies were detected in the time series.')
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測最新資料點的異常狀態

呼叫 Anomaly Detector API，使用用戶端的 `detect_last_point` 方法來判斷最新資料點是否異常，並儲存傳回的 `LastDetectResponse` 物件。 回應的 `is_anomaly` 值是布林值，其指定該資料點的異常狀態。  

```python
print('Detecting the anomaly status of the latest data point.')

try:
    response = client.detect_last_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if response.is_anomaly:
    print('The latest point is detected as anomaly.')
else:
    print('The latest point is not detected as anomaly.')
```

## <a name="detect-change-points-in-the-data-set"></a>偵測資料集內的變更點

呼叫 API 以使用用戶端的 `detect_change_point` 方法，偵測時間序列中的變更點。 儲存傳回的 `ChangePointDetectResponse` 物件。 逐一查看回應的 `is_change_point` 清單，然後列印任何 `true` 值的索引。 如果有找到這些值，則會對應到趨勢變更點的索引。

```python
print('Detecting change points in the entire time series.')

try:
    response = client.detect_change_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_change_point):
    print('An change point was detected at index:')
    for i, value in enumerate(response.is_change_point):
        if value:
            print(i)
else:
    print('No change point were detected in the time series.')
```

## <a name="run-the-application"></a>執行應用程式

使用 `python` 命令及您的檔案名稱來執行應用程式。

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
