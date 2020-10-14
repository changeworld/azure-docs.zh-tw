---
title: 快速入門：使用 Anomaly Detector REST API 與 Java 偵測時間序列資料中的異常狀況
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Anomaly Detector API 來偵測資料序列中的異常狀況 (以批次或串流資料的形式)。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.custom: devx-track-java
ms.author: mbullwin
ms.openlocfilehash: e23e9016b53a1d9f99809d792d710f865918a098
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019676"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>快速入門：使用 Anomaly Detector REST API 與 Java 偵測時間序列資料中的異常狀況

使用本快速入門以開始使用 Anomaly Detector API 的兩個偵測模式，來偵測時間序列資料中的異常狀況。 此 Java 應用程式會傳送包含 JSON 格式時間序列資料的 API 要求，並取得回應。

| API 要求                                        | 應用程式輸出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 以批次方式偵測異常狀況                        | JSON 回應包含時間序列資料中每個資料點的異常狀態 (和其他資料)，以及偵測到的任何異常狀況的位置。 |
| 偵測最新資料點的異常狀態 | JSON 回應包含時間序列資料中最新資料點的異常狀態 (和其他資料)。   |
| 偵測標示新資料趨勢的變更點 | JSON 回應包含時間序列資料中偵測到的變更點。 |

雖然此應用程式是以 Java 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 您可以在 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java) 上找到此快速入門的原始程式碼。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
- 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="建立異常偵測器資源"  target="_blank">建立異常偵測器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    - 您需要來自所建立資源的金鑰和端點，以將應用程式連線至異常偵測器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
- [Java&trade; 開發套件 (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 或更新版本。
- 從 Maven 存放庫匯入這些程式庫
    - [Java 中的 JSON](https://mvnrepository.com/artifact/org.json/json) 套件
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) 套件

- JSON 檔案包含時間序列資料點。 此快速入門的範例資料可以在 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) 上找到。

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>建立新的應用程式

1. 建立新的 Java 專案，並匯入下列程式庫。

    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. 針對您的訂用帳戶金鑰和端點建立變數。 以下是您可以用於異常偵測的 URI。 這些稍後將會附加至您的服務端點，以建立 API 要求 URL。

    |偵測方法  |URI  |
    |---------|---------|
    |批次偵測    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |最新資料點上的偵測     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    | 變更點偵測 | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立名為 `sendRequest()` 的新函式，取用上面建立的變數。 然後執行下列步驟。

2. 建立可將要求傳送至 API 的 `CloseableHttpClient` 物件。 藉由結合您的端點和 Anomaly Detector URL，將要求傳送至 `HttpPost` 要求物件。

3. 使用要求的 `setHeader()` 函式，將 `Content-Type` 標頭設定為 `application/json`，並將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

4. 將要求的 `setEntity()` 函式用於要傳送的資料。

5. 使用用戶端的 `execute()` 函式傳送要求，並將它儲存到 `CloseableHttpResponse` 物件。

6. 建立 `HttpEntity` 物件以儲存回應內容。 使用 `getEntity()` 取得內容。 如果回應不是空的，請將它傳回。

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>以批次方式偵測異常狀況

1. 建立名為 `detectAnomaliesBatch()` 的方法，以批次方式偵測整個資料的異常狀況。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方建立的 `sendRequest()` 方法。 取得結果，並將它列印到主控台。

2. 如果回應包含 `code` 欄位，則列印錯誤碼和錯誤訊息。

3. 否則，在資料集中尋找異常狀況的位置。 回應的 `isAnomaly` 欄位包含與指定的資料點是否為異常相關的布林值。 取得 JSON 陣列並逐一查看，然後列印任何 `true` 值的索引。 如果有找到，這些值會對應到異常資料點的索引。

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測最新資料點的異常狀態

建立名為 `detectAnomaliesLatest()` 的方法，以偵測資料集中最後一個資料點的異常狀態。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方建立的 `sendRequest()` 方法。 取得結果，並將它列印到主控台。

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]


## <a name="detect-change-points-in-the-data"></a>偵測資料中的變更點

1. 建立名為 `detectChangePoints()` 的方法，以批次方式偵測整個資料的異常狀況。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方建立的 `sendRequest()` 方法。 取得結果，並將它列印到主控台。

2. 如果回應包含 `code` 欄位，則列印錯誤碼和錯誤訊息。

3. 否則，在資料集中尋找變更點的位置。 回應的 `isChangePoint` 欄位包含布林值，指出指定的資料點是否為趨勢變更點。 取得 JSON 陣列並逐一查看，然後列印任何 `true` 值的索引。 如果有找到這些值，則會對應到趨勢變更點的索引。

    [!code-java[detect change points](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectChangePoint)]

## <a name="load-your-time-series-data-and-send-the-request"></a>載入時間序列資料，並傳送要求

1. 在您應用程式的主要方法中，讀入 JSON 檔案，其中包含將新增至要求的資料。

2. 呼叫上面建立的兩個異常偵測函式。

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>範例回應

成功的回應會以 JSON 格式傳回。 按一下以下連結，在 GitHub 上檢視 JSON 回應：
* [批次偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新資料點偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [變更資料點偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
