---
title: 快速入門：使用 REST API 和 C# 來取得影像見解 - Bing 圖像式搜尋
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Bing 圖像式搜尋 API 和 C# 上傳影像，然後取得影像的相關見解。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-csharp
ms.openlocfilehash: 83e4e2b1c1eb10f7a4cd0f2fb4f87b280801eed0
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487111"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>快速入門：使用 Bing 圖像式搜尋 REST API 和 C# 來取得影像見解

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

本快速入門示範如何將影像上傳給 Bing 圖像式搜尋 API，以及如何檢視其所傳回的見解。

## <a name="prerequisites"></a>必要條件

* 任何版本的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)。
* [Json.NET 架構](https://www.newtonsoft.com/json) (以 NuGet 套件形式提供)。
* 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/) 來執行此應用程式。

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在 Visual Studio 中，建立一個名為 BingSearchApisQuickStart 的新主控台解決方案。 將下列命名空間新增至主要程式碼檔案：

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. 為訂用帳戶金鑰、端點和所要上傳影像的路徑新增變數。 對於 `uriBase` 值，您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. 建立名為 `GetImageFileName()` 的方法，以取得您影像的路徑。
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. 建立一個方法，以取得影像的二進位資料。

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>建置表單資料

1. 若要上傳本機影像，請先建置要傳送給 API 的表單資料。 表單資料包括 `Content-Disposition` 標頭、設為 "image" 的 `name` 參數，以及設為影像檔名的 `filename` 參數。 表單的內容包含影像的二進位資料。 您可以上傳的影像大小上限為 1 MB。

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

2. 新增界限字串來格式化 POST 表單資料。 界限字串會決定此資料的開始、結束和新行字元。

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

3. 使用下列變數將參數新增至表單資料：

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

4. 建立名為 `BuildFormDataStart()` 的函式，以使用界限字串和影像路徑建立表單資料的開始位置。
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

5. 建立名為 `BuildFormDataEnd()` 的函式，以使用界限字串建立表單資料的結束位置。
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>呼叫 Bing 圖像式搜尋 API

1. 建立一個函式來呼叫 Bing 圖像式搜尋端點，並傳回 JSON 回應。 此函式會取得表單資料的開始和結束位置、包含影像資料的位元組陣列，以及 `contentType` 值。

2. 使用 `WebRequest` 來儲存 URI、contentType 值和標頭。  

3. 使用 `request.GetRequestStream()` 來寫入表單和影像資料，然後取得回應。 您的函式應該類似下列程式碼：
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>建立 Main 方法

1. 在應用程式的 `Main()` 方法中，取得影像的檔案名稱和二進位資料。

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. 將界限格式化來設定 POST 主體。 然後呼叫 `BuildFormDataStart()` 和 `BuildFormDataEnd()`，以建立表單資料。

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. 將 `CONTENT_TYPE_HEADER_PARAMS` 和表單資料界限格式化，以建立 `ContentType` 值。

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. 藉由呼叫 `BingImageSearch()` 來取得 API 回應，然後列印回應。

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>使用 HTTPClient

如果您使用 `HttpClient`，則可以使用 `MultipartFormDataContent` 類別來建置表單資料。 使用下列程式碼區段來取代上述範例中的對應方法：

1. 以下列程式碼取代 `Main()` 方法：

   ```csharp
           static void Main()
           {
               try
               {
                   Console.OutputEncoding = System.Text.Encoding.UTF8;

                   if (accessKey.Length == 32)
                   {
                       if (IsImagePathSet(imagePath))
                       {
                           var filename = GetImageFileName(imagePath);
                           Console.WriteLine("Getting image insights for image: " + filename);
                           var imageBinary = GetImageBinary(imagePath);

                           var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                           var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                           Console.WriteLine("\nJSON Response:\n");
                           Console.WriteLine(JsonPrettyPrint(json));
                       }
                   }
                   else
                   {
                       Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                       Console.WriteLine("Please paste yours into the source code.");
                   }

                   Console.Write("\nPress Enter to exit ");
                   Console.ReadLine();
               }
               catch (Exception e)
               {
                   Console.WriteLine(e.Message);
               }
           }
   ```

2. 以下列程式碼取代 `BingImageSearch()` 方法：

   ```csharp
           /// <summary>
           /// Calls the Bing visual search endpoint and returns the JSON response.
           /// </summary>
           static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
           {
               var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
               requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

               var content = new MultipartFormDataContent(boundary);
               content.Add(new ByteArrayContent(image), "image", "myimage");
               requestMessage.Content = content;

               var httpClient = new HttpClient();

               Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
               HttpResponseMessage httpResponse = httpRequest.Result;
               HttpStatusCode statusCode = httpResponse.StatusCode;
               HttpContent responseContent = httpResponse.Content;

               string json = null;

               if (responseContent != null)
               {
                   Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                   json = stringContentsTask.Result;
               }

               return json;
           }
   ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立圖像式搜尋單頁 Web 應用程式](../tutorial-bing-visual-search-single-page-app.md)