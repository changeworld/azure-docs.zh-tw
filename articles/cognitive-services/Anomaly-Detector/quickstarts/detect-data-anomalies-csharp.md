---
title: 快速入門：使用 Anomaly Detector REST API 與 C# 偵測時間序列資料中的異常狀況
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Anomaly Detector API 來偵測資料序列中的異常狀況 (以批次或串流資料的形式)。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 06/30/2020
ms.author: aahi
ms.openlocfilehash: 86742568d8f0c7c951d872e7df23b8ce1cb0920f
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244222"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>快速入門：使用 Anomaly Detector REST API 與 C# 偵測時間序列資料中的異常狀況

使用本快速入門以開始使用 Anomaly Detector API 的兩個偵測模式，來偵測時間序列資料中的異常狀況。 此 C# 應用程式會傳送包含 JSON 格式時間序列資料的兩個 API 要求，並取得回應。

| API 要求                                        | 應用程式輸出                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 以批次方式偵測異常狀況                        | JSON 回應包含時間序列資料中每個資料點的異常狀態 (和其他資料)，以及偵測到的任何異常狀況的位置。 |
| 偵測最新資料點的異常狀態 | JSON 回應包含時間序列資料中最新資料點的異常狀態 (和其他資料)。                                        |

 雖然此應用程式是以 C# 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 您可以在 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs) 上找到此快速入門的原始程式碼。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
- 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="建立異常偵測器資源"  target="_blank">建立異常偵測器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    - 您需要來自所建立資源的金鑰和端點，以將應用程式連線至異常偵測器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
- [Visual Studio 2017 或更新版本](https://visualstudio.microsoft.com/downloads/)的任何版本
- [Json.NET](https://www.newtonsoft.com/json) 架構 (以 NuGet 套件形式提供)。 若要在 Visual Studio 中安裝 Newtonsoft.Json 作為 NuGet 套件：

    1. 在 [方案總管] 中，以滑鼠右鍵按一下您的專案。
    2. 選取 [管理 NuGet 套件]。
    3. 搜尋 Newtonsoft.Json 並安裝套件

- 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/) 來執行此應用程式。

- JSON 檔案包含時間序列資料點。 此快速入門的範例資料可以在 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) 上找到。

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>建立新的應用程式

1. 在 Visual Studio 中，建立新的主控台解決方案並新增下列套件。

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. 針對您的訂用帳戶金鑰和端點建立變數。 以下是您可以用於異常偵測的 URI。 這些稍後將會附加至您的服務端點，以建立 API 要求 URL。

    | 偵測方法                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | 批次偵測                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | 最新資料點上的偵測 | `/anomalydetector/v1.0/timeseries/last/detect`   |

    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立名為 `Request` 的新非同步函式，取用上面建立的變數。

2. 使用 `HttpClient` 物件設定用戶端的安全性通訊協定和標頭資訊。 請務必將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。 然後建立要求的 `StringContent` 物件。

3. 使用 `PostAsync()` 傳送要求，然後傳回回應。

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>以批次方式偵測異常狀況

1. 建立名為 `detectAnomaliesBatch()` 的新函式。 使用您的端點、訂用帳戶金鑰、批次異常偵測的 URL，以及時間序列資料呼叫 `Request()` 函式，進而建構要求並加以傳送。

2. 將 JSON 物件還原序列化，並將它寫入到主控台。

3. 如果回應包含 `code` 欄位，則列印錯誤碼和錯誤訊息。

4. 否則，在資料集中尋找異常狀況的位置。 回應的 `isAnomaly` 欄位包含布林值陣列，其中每個值都表示資料點是否異常。 使用回應物件的 `ToObject<bool[]>()` 函式，將其轉換成字串陣列。 逐一查看陣列，並列印任何 `true` 值的索引。 如果有找到，這些值會對應到異常資料點的索引。

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測最新資料點的異常狀態

1. 建立名為 `detectAnomaliesLatest()` 的新函式。 使用您的端點、訂用帳戶金鑰、最新資料點異常偵測的 URL，以及時間序列資料呼叫 `Request()` 函式，進而建構要求並加以傳送。

2. 將 JSON 物件還原序列化，並將它寫入到主控台。

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>載入時間序列資料，並傳送要求

1. 在您應用程式的主要方法中，使用 `File.ReadAllText()` 載入 JSON 時間序列資料。

2. 呼叫上面建立的異常偵測函式。 使用 `System.Console.ReadKey()`，讓主控台視窗在執行應用程式後保持開啟。

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>範例回應

成功的回應會以 JSON 格式傳回。 按一下以下連結，在 GitHub 上檢視 JSON 回應：
* [批次偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新資料點偵測回應範例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
