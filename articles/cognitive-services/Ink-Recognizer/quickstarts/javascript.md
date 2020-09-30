---
title: 快速入門：使用筆跡辨識器 REST API 與 Node.js 來辨識數位筆跡
titleSuffix: Azure Cognitive Services
description: 在本快速入門中使用筆跡辨識器 API 和 JavaScript 開始辨識數位筆跡筆觸。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 13e094b0f5d59e070a96ab4b45dcd37315c46c60
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327341"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>快速入門：使用筆跡辨識器 REST API 與 JavaScript 來辨識數位筆跡

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

使用本快速入門，開始針對數位筆跡筆觸使用筆跡辨識器 API。 此 JavaScript 應用程式會傳送包含 JSON 格式筆跡筆觸資料的 API 要求，並顯示回應。

雖然此應用程式是以 JavaScript 撰寫，且可在 Web 瀏覽器中執行，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

通常您會從數位筆跡應用程式呼叫 API。 本快速入門會從 JSON 檔案針對下列手寫範例傳送筆跡筆觸資料。

![手寫文字的影像](../media/handwriting-sample.jpg)

此快速入門的原始程式碼可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905) 上找到。

## <a name="prerequisites"></a>Prerequisites

- 網頁瀏覽器
- 此快速入門的範例筆跡筆觸資料可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json) 上找到。

### <a name="create-an-ink-recognizer-resource"></a>建立筆跡辨識器資源

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>建立新的應用程式

1. 在您最愛的整合式開發環境或編輯器中，建立新的 `.html` 檔案。 接著為其新增基本的 HTML，我們稍後會新增程式碼。
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. 在 `<body>` 標籤內新增下列 html：
    1. 顯示 JSON 要求和回應的兩個文字區域。
    2. 稍後將建立呼叫 `recognizeInk()` 函式的按鈕。
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>載入範例 JSON 資料

1. 在 `<script>` 標籤內，建立 sampleJson 的變數。 然後建立名為 `openFile()` 的 JavaScript 函式，其會開啟檔案總管，因此您可以選取您的 JSON 檔案。 按一下 `Recognize ink` 按鈕時，它會呼叫此函式並開始讀取檔案。
2. 使用 `FileReader` 物件的 `onload()` 函式，以非同步方式處理檔案。 
    1. 以空字串取代檔案中的任何 `\n` 或 `\r` 字元。 
    2. 使用 `JSON.parse()` 將文字轉換成有效的 JSON
    3. 更新應用程式中的 `request` 文字方塊。 使用 `JSON.stringify()` 格式化 JSON 字串。 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>將要求傳送給筆跡辨識器 API

1. 在 `<script>` 標籤內建立稱為 `recognizeInk()` 的函式。 此函式稍後將會呼叫 API，並以回應更新頁面。 在此函式內使用下列步驟新增程式碼。 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. 為您的端點 URL、訂用帳戶金鑰及範例 JSON 建立變數。 然後建立 `XMLHttpRequest` 物件以傳送 API 要求。 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. 建立 `XMLHttpRequest` 物件的傳回函式。 此函式會剖析成功要求的 API 回應，並顯示在應用程式中。 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. 建立要求物件的錯誤函式。 此函式會將錯誤記錄到主控台。 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. 為要求物件的 `onreadystatechange` 屬性建立函式。 當要求物件的整備狀態變更時，將會套用上述的傳回和錯誤函式。
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. 傳送 API 要求。 將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭，並將 `content-type` 設為 `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>執行應用程式並檢視回應

此應用程式可在您的 Web 瀏覽器中執行。 成功的回應會以 JSON 格式傳回。 您也可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json) 上找到 JSON 回應：

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [REST API 參考資料](https://go.microsoft.com/fwlink/?linkid=2089907)

若要了解筆跡辨識 API 在數位筆跡應用程式中的運作方式，請看位於 GitHub 上的下列應用程式範例：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 網頁瀏覽器應用程式](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089805)
