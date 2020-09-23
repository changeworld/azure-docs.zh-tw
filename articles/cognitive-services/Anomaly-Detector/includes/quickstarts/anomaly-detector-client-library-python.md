---
title: 異常偵測器 Python 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 98f68af11cf21cb795e7741585e55c195c066995
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "91024802"
---
開始使用適用於 Python 的 Anomaly Detector 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 Anomaly Detector 服務可藉由自動對時間序列資料使用最適合的模型，而讓您找出其中的異常狀況，不論是什麼產業、情境或資料量都沒問題。

使用適用於 Python 的 Anomaly Detector 用戶端程式庫來：

* 以批次要求方式偵測整個時間序列資料集的異常狀況
* 偵測您的時間序列中最新資料點的異常狀態
* 偵測您資料集內的趨勢變更點。

[程式庫參考文件](https://go.microsoft.com/fwlink/?linkid=2090370) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [套件 (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [在 GitHub 上尋找範例程式碼](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

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

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

為您的金鑰建立變數作為環境變數、時間序列資料檔案的路徑，以及您訂用帳戶的 Azure 位置。 例如： `westus2` 。

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後，您可以透過以下項目安裝用戶端程式庫：

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>物件模型

Anomaly Detector 用戶端是一種 [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) 物件，其使用您的金鑰向 Azure 進行驗證。 用戶端可以使用 [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) 在整個資料集上進行異常偵測，或使用 [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) 在最新資料點上進行異常偵測。 [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090370) 函式會偵測標記趨勢變更的點。

時間序列資料會透過 [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) 物件以 [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) 序列的形式來傳送。 `Request` 物件包含用來說明資料的屬性 (例如，[細微性](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python))，以及供異常偵測使用的參數。

Anomaly Detector 回應是 [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python)、[EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) 或 [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) 物件 (端視所使用的方法而定)。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 Python 的 Anomaly Detector 用戶端程式庫來執行下列動作：

* [驗證用戶端](#authenticate-the-client)
* [從檔案載入時間序列資料集](#load-time-series-data-from-a-file)
* [偵測整個資料集內的異常](#detect-anomalies-in-the-entire-data-set)
* [偵測最新資料點的異常狀態](#detect-the-anomaly-status-of-the-latest-data-point)
* [偵測資料集內的變更點](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>驗證用戶端

將您的 Azure 位置變數新增至端點，並使用您的金鑰驗證用戶端。

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>從檔案載入時間序列資料集

從 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) 下載此快速入門的資料範例：
1. 在瀏覽器中以滑鼠右鍵按一下 [原始]。
2. 按一下 [另存連結]。
3. 以 .csv 檔案的形式將該檔案另儲到應用程式目錄中。

此時間序列資料會格式化為 .csv 檔案，且會傳送至 Anomaly Detector API。

使用 Pandas 程式庫的 `read_csv()` 方法載入您的資料檔案，並製作空白清單變數來儲存您的資料序列。 逐一查看檔案，並附加資料作為 [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) 物件。 此物件會包含 .csv 資料檔的資料列中的時間戳記和數值。

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

使用您的時間序列，以及其資料點的[細微性](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (或週期性)，建立 [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) 物件。 例如： `Granularity.daily` 。

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>偵測整個資料集內的異常

使用用戶端的 [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) 方法，呼叫 API 來偵測整個時間序列資料的異常情形。 儲存傳回的 [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) 物件。 逐一查看回應的 `is_anomaly` 清單，然後列印任何 `true` 值的索引。 如果有找到，這些值會對應到異常資料點的索引。

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測最新資料點的異常狀態

呼叫 Anomaly Detector API，使用用戶端的 [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) 方法來判斷最新資料點是否異常，並儲存傳回的 [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) 物件。 回應的 `is_anomaly` 值是布林值，其指定該資料點的異常狀態。  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="detect-change-points-in-the-data-set"></a>偵測資料集內的變更點

呼叫 API 以使用用戶端的 [detect_change_point()](https://go.microsoft.com/fwlink/?linkid=2090370) 方法，偵測時間序列中的變更點。 儲存傳回的 [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) 物件。 逐一查看回應的 `is_change_point` 清單，然後列印任何 `true` 值的索引。 如果有找到這些值，則會對應到趨勢變更點的索引。

[!code-python[detect change points](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=changePointDetection)]

## <a name="run-the-application"></a>執行應用程式

使用 `python` 命令及您的檔案名稱來執行應用程式。

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
