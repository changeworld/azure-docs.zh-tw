---
title: 異常偵測器 .NET 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: 7969e2011a242152e27d7c1aa67b36d99d92adfe
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371948"
---
開始使用適用於 .NET 的 Anomaly Detector 用戶端程式庫。 請依照下列步驟，開始使用服務提供的演算法安裝套件。 Anomaly Detector 服務可藉由自動對時間序列資料使用最適合的模型，而讓您找出其中的異常狀況，不論是什麼產業、情境或資料量都沒問題。

使用適用於 .NET 的 Anomaly Detector 用戶端程式庫來：

* 以批次要求方式偵測整個時間序列資料集的異常狀況
* 偵測您的時間序列中最新資料點的異常狀態
* 偵測您資料集內的趨勢變更點。

[程式庫參考文件](https://aka.ms/anomaly-detector-dotnet-ref) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [套件 (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.2) | [在 GitHub 上尋找程式碼](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="建立異常偵測器資源"  target="_blank">建立異常偵測器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至異常偵測器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>建立新的 .NET Core 應用程式

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `anomaly-detector-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" 專案，內含單一 C# 原始程式檔： *Program.cs* 。

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```dotnetcli
dotnet build
```

建置輸出應該不會有警告或錯誤。

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 Anomaly Detector 用戶端程式庫：

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector
```

從專案目錄中中開啟 program.cs  檔案，並使用 `directives` 新增下列內容：

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

在應用程式的 `main()` 方法中，為資源的 Azure 位置建立變數，並將金鑰建立為環境變數。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>物件模型

Anomaly Detector 用戶端是一種 [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) 物件，會使用含有金鑰的 [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials) 向 Azure 進行驗證。 用戶端可以使用 [EntireDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync) 在整個資料集上執行異常偵測，或使用 [LastDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync) 在最新資料點上執行異常偵測。 [ChangePointDetectAsync](https://aka.ms/anomaly-detector-dotnet-ref) 方法會偵測標記趨勢變更的點。

時間序列資料會透過 [Request](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) 物件以 [Point](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) 序列的形式來傳送。 `Request` 物件包含用來說明資料的屬性 (例如，[細微性](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity))，以及供異常偵測使用的參數。

Anomaly Detector 回應是 [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse)、[LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) 或 [changePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) 物件 (端視所使用的方法而定)。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 .NET 的 Anomaly Detector 用戶端程式庫來執行下列動作：

* [驗證用戶端](#authenticate-the-client)
* [從檔案載入時間序列資料集](#load-time-series-data-from-a-file)
* [偵測整個資料集內的異常](#detect-anomalies-in-the-entire-data-set)
* [偵測最新資料點的異常狀態](#detect-the-anomaly-status-of-the-latest-data-point)
* [偵測資料集內的變更點](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>驗證用戶端

在新方法中，使用端點和金鑰來具現化用戶端。 使用金鑰建立 [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) 物件，並使用該物件與您的端點來建立 [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) 物件。

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>從檔案載入時間序列資料集

從 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) 下載此快速入門的資料範例：
1. 在瀏覽器中以滑鼠右鍵按一下 [原始]。
2. 按一下 [另存連結]。
3. 以 .csv 檔案的形式將該檔案另儲到應用程式目錄中。

此時間序列資料會格式化為 .csv 檔案，且會傳送至 Anomaly Detector API。

建立新方法來讀取時間序列資料，並將其新增至 [Request](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) 物件。 使用檔案路徑呼叫 `File.ReadAllLines()`，並建立 [Point](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) 物件清單，然後移除任何新行字元。 擷取這些值，並將時間戳記與其數字值分開，然後將其新增至新的 `Point` 物件。

使用資料點序列製作 `Request` 物件，並為資料點的[細微性](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (或週期性) 製作 `Granularity.Daily`。

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>偵測整個資料集內的異常

使用 `Request` 物件建立方法來呼叫用戶端的 [EntireDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) 方法，並等候 [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) 物件形式的回應。 如果時間序列包含任何異常，請逐一查看回應的 [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) 值和，並列印任何屬於 `true` 的值。 如果有找到，這些值會對應到異常資料點的索引。

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>偵測最新資料點的異常狀態

使用 `Request` 物件建立方法來呼叫用戶端的 [LastDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) 方法，並等候 [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) 物件形式的回應。 檢查回應的 [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) 屬性來判斷所傳送的最新資料點是否異常。

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="detect-change-points-in-the-data-set"></a>偵測資料集內的變更點

使用 `Request` 物件建立方法來呼叫用戶端的 [DetectChangePointAsync](https://aka.ms/anomaly-detector-dotnet-ref) 方法，並等候 [ChangePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) 物件形式的回應。 檢查回應的 IsChangePoint 值，並列印屬於 `true` 的任何資料。 如果有找到這些值，則這些值會對應到趨勢變更點。

[!code-csharp[DetectChangePoint() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=changePointExample)]

## <a name="run-the-application"></a>執行應用程式

使用 `dotnet run` 命令，從您的應用程式目錄執行應用程式。

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]