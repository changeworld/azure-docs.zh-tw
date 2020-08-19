---
title: 異常偵測器 JavaScript 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 836582003c4b4bd47d2b90b845ae414210d16edd
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246447"
---
開始使用適用於 JavaScript 的 Anomaly Detector 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 Anomaly Detector 服務可藉由自動對時間序列資料使用最適合的模型，而讓您找出其中的異常狀況，不論是什麼產業、情境或資料量都沒問題。

使用適用於 JavaScript 的 Anomaly Detector 用戶端程式庫來：

* 以批次要求方式偵測整個時間序列資料集的異常狀況
* 偵測您的時間序列中最新資料點的異常狀態

[參考文件](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [套件 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [在 GitHub 上尋找程式碼](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 最新版的 [Node.js](https://nodejs.org/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="建立異常偵測器資源"  target="_blank">建立異常偵測器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至異常偵測器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

執行命令 `npm init`，以使用 `package.json` 檔案建立節點應用程式。 

```console
npm init
```

建立名為 `index.js` 的檔案，並匯入下列程式庫：

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。 針對您將在稍後步驟中下載的範例資料檔案，建立另一個變數，並為資料點建立空白清單。 然後建立 `ApiKeyCredentials` 物件來包含金鑰。

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 `ms-rest-azure` 和 `azure-cognitiveservices-anomalydetector` NPM 套件。 本快速入門中也會使用 csv-parse 程式庫：

```console
npm install  @azure/cognitiveservices-anomalydetector @azure/ms-rest-js csv-parse
```

您應用程式的 `package.json` 檔案會隨著相依性而更新。

## <a name="object-model"></a>物件模型

Anomaly Detector 用戶端是一種 [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) 物件，其使用您的金鑰向 Azure 進行驗證。 此用戶端會提供兩種異常偵測方法：使用 [entireDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--) 在整個資料集上進行，以及使用 [LastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) 在最新的資料點上進行。 

時間序列資料會透過 [Request](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) 物件以 [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) 序列的形式來傳送。 `Request` 物件包含用來說明資料的屬性 (例如，[細微性](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity))，以及供異常偵測使用的參數。 

Anomaly Detector 回應是 [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) 或 [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) 物件 (端視所使用的方法而定)。 

## <a name="code-examples"></a>程式碼範例 

這些程式碼片段會示範如何使用適用於 Node.js 的 Anomaly Detector 用戶端程式庫來執行下列動作：

* [驗證用戶端](#authenticate-the-client)
* [從檔案載入時間序列資料集](#load-time-series-data-from-a-file)
* [偵測整個資料集內的異常](#detect-anomalies-in-the-entire-data-set) 
* [偵測最新資料點的異常狀態](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>驗證用戶端

使用您的端點和認證將 [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) 物件具現化。

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>從檔案載入時間序列資料集

從 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv) 下載此快速入門的資料範例：
1. 在瀏覽器中以滑鼠右鍵按一下 [原始]。
2. 按一下 [另存連結]。
3. 以 .csv 檔案的形式將該檔案另儲到應用程式目錄中。

此時間序列資料會格式化為 .csv 檔案，且會傳送至 Anomaly Detector API。

使用 csv-parse 程式庫的 `readFileSync()` 方法來讀取資料檔案，並使用 `parse()` 來剖析檔案。 針對每一行，推送包含時間戳記和數值的 [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) 物件。

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>偵測整個資料集內的異常 

使用用戶端的 [entireDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) 方法，單批次地呼叫 API 來偵測整個時間序列的異常情形。 儲存傳回的 [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) 物件。 逐一查看回應的 `isAnomaly` 清單，然後列印任何 `true` 值的索引。 如果有找到，這些值會對應到異常資料點的索引。

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測最新資料點的異常狀態

呼叫 Anomaly Detector API，使用用戶端的 [lastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) 方法來判斷最新資料點是否異常，並儲存傳回的 [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) 物件。 回應的 `isAnomaly` 值是布林值，其指定該資料點的異常狀態。  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `node` 命令執行應用程式。

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
