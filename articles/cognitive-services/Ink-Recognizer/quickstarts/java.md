---
title: 快速入門：使用筆跡辨識器 REST API 與 Java 來辨識數位筆跡
titleSuffix: Azure Cognitive Services
description: 在本快速入門中使用筆跡辨識器 API 和 Java 開始辨識數位筆跡筆觸。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-java
ms.openlocfilehash: b73f5013fdbef34344ece672bacff90db9bac60b
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051571"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>快速入門：使用筆跡辨識器 REST API 與 Java 來辨識數位筆跡

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

使用本快速入門，開始針對數位筆跡筆觸使用筆跡辨識器 API。 此 Java 應用程式會傳送包含 JSON 格式筆跡筆觸資料的 API 要求，並取得回應。

雖然此應用程式是以 Java 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

通常您會從數位筆跡應用程式呼叫 API。 本快速入門會從 JSON 檔案針對下列手寫範例傳送筆跡筆觸資料。

![手寫文字的影像](../media/handwriting-sample.jpg)

此快速入門的原始程式碼可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904) 上找到。

## <a name="prerequisites"></a>Prerequisites

- [Java&trade; 開發套件 (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 或更新版本。

- 從 Maven 存放庫匯入這些程式庫
    - [Java 中的 JSON](https://mvnrepository.com/artifact/org.json/json) 套件
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) 套件

- 此快速入門的範例筆跡筆觸資料可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json) 上找到。

### <a name="create-an-ink-recognizer-resource"></a>建立筆跡辨識器資源

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>建立新的應用程式

1. 在您最愛的 IDE 或編輯器中建立新的 Java 專案，並匯入下列程式庫。
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. 為您的訂用帳戶金鑰、端點與 JSON 檔案建立變數。 端點稍後將會附加到筆跡辨識器 URI。

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>建立傳送要求的函式

1. 建立名為 `sendRequest()` 的新函式，取用上面建立的變數。 然後執行下列步驟。

2. 建立可將要求傳送至 API 的 `CloseableHttpClient` 物件。 藉由結合您的端點和手寫辨識器 URL，將要求傳送至 `HttpPut` 要求物件。

3. 使用要求的 `setHeader()` 函式，將 `Content-Type` 標頭設定為 `application/json`，並將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

4. 將要求的 `setEntity()` 函式用於要傳送的資料。   

5. 使用用戶端的 `execute()` 函式傳送要求，並將它儲存到 `CloseableHttpResponse` 物件。 

6. 建立 `HttpEntity` 物件以儲存回應內容。 使用 `getEntity()` 取得內容。 如果回應不是空的，請將它傳回。
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>傳送筆跡辨識要求

建立名為 `recognizeInk()` 的方法以辨識您的筆跡筆觸資料。 使用端點、URL、訂用帳戶金鑰和 json 資料呼叫上方建立的 `sendRequest()` 方法。 取得結果，並將它列印到主控台。

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>載入數位筆跡資料並傳送要求

1. 在您應用程式的主要方法中，讀入 JSON 檔案，其中包含將新增至要求的資料。

2. 呼叫上面建立的筆跡辨識函式。
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>執行應用程式並檢視回應

執行應用程式。 成功的回應會以 JSON 格式傳回。 您也可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json) 上找到 JSON 回應。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://go.microsoft.com/fwlink/?linkid=2089907)


若要了解筆跡辨識 API 在數位筆跡應用程式中的運作方式，請看位於 GitHub 上的下列應用程式範例：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 網頁瀏覽器應用程式](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089805)
