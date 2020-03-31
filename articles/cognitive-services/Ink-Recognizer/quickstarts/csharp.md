---
title: 快速入門：使用筆跡辨識器 REST API 與 C# 來辨識數位筆跡
titleSuffix: Azure Cognitive Services
description: 本快速入門說明如何使用筆跡辨識器 API 開始辨識數位筆跡筆觸。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: c24d055f1904453d2f512a278f00e23c6fea1d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371385"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>快速入門：使用筆跡辨識器 REST API 與 C# 來辨識數位筆跡

使用本快速入門，開始將數位筆跡筆觸傳送到筆跡辨識器 API。 此 C# 應用程式會傳送包含 JSON 格式筆跡筆觸資料的 API 要求，並取得回應。

雖然此應用程式是以 C# 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

通常您會從數位筆跡應用程式呼叫 API。 本快速入門會從 JSON 檔案針對下列手寫範例傳送筆跡筆觸資料。

![手寫文字的影像](../media/handwriting-sample.jpg)

此快速入門的原始程式碼可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502) 上找到。

## <a name="prerequisites"></a>Prerequisites

- 任何一版的 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)。
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - 若要在 Visual Studio 中安裝 Newtonsoft.Json 作為 NuGet 套件：
        1. 以滑鼠右鍵按一下 [Solution Manager] 
        2. 按一下 [管理 NuGet 套件...] 
        3. 然後搜尋 `Newtonsoft.Json` 並安裝該套件。
- 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/) 來執行此應用程式。

- 此快速入門的範例筆跡筆觸資料可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json) 上找到。

### <a name="create-an-ink-recognizer-resource"></a>建立筆跡辨識器資源

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>建立新的應用程式

1. 在 Visual Studio 中，建立新的主控台解決方案並新增下列套件。 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. 為您的訂用帳戶金鑰與端點和範例 JSON 檔案建立變數。 端點稍後將與 `inkRecognitionUrl` 結合以存取 API。 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立名為 `Request` 的新非同步函式，取用上面建立的變數。

2. 使用 `HttpClient` 物件設定用戶端的安全性通訊協定和標頭資訊。 請務必將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。 然後建立要求的 `StringContent` 物件。
 
3. 以 `PutAsync()` 傳送要求。 如果要求成功，則傳回回應。  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>傳送筆跡辨識要求

1. 建立名為 `recognizeInk()` 的新函式。 使用您的端點、訂用帳戶金鑰、API 的 URL，以及數位筆跡筆觸資料呼叫 `Request()` 函式，進而建構要求並加以傳送。

2. 將 JSON 物件還原序列化，並將它寫入到主控台。 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>載入您的數位筆跡資料

建立名為 `LoadJson()` 的函式以載入筆跡資料 JSON 檔案。 使用 `StreamReader` 和 `JsonTextReader` 來建立 `JObject` 並將它傳回。

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>傳送 API 要求

1. 在您應用程式的主要方法中，使用上面建立的函式來載入 JSON 資料。 

2. 呼叫上面所建立的 `recognizeInk()`函式。 使用 `System.Console.ReadKey()`，讓主控台視窗在執行應用程式後保持開啟。
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>執行應用程式並檢視回應

執行應用程式。 成功的回應會以 JSON 格式傳回。 您也可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json) 上找到 JSON 回應。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://go.microsoft.com/fwlink/?linkid=2089907)


若要了解筆跡辨識 API 在數位筆跡應用程式中的運作方式，請看位於 GitHub 上的下列應用程式範例：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 網頁瀏覽器應用程式](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089805)
