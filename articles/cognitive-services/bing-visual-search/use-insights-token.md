---
title: 使用深入解析權杖 - Bing 圖像式搜尋
titleSuffix: Azure Cognitive Services
description: 說明如何搭配 Bing 圖像式搜尋 API 使用影像的深入解析權杖取得影像的相關深入解析。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/26/2019
ms.author: scottwhi
ms.openlocfilehash: 251197c456ece4fe2dbbe264219d52f3502b7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67341728"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>使用見解權杖獲取圖像的見解

Bing 圖像式搜尋 API 會傳回您所提供影像的相關資訊。 您可以使用影像的 URL、見解權杖，或上傳影像來提供影像。 如需這些選項的資訊，請參閱[什麼是 Bing 圖像式搜尋 API？](overview.md) 本文將示範如何使用見解權杖。 有關演示如何上傳圖像以獲得見解的示例，請參閱快速入門 （[C#](quickstarts/csharp.md) | [JAVA](quickstarts/java.md) | [Node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)）。

如果向必應視覺搜索發送圖像權杖或 URL，則下面將顯示您必須包含在 POST 正文中的表單資料。 表單資料必須包含`Content-Disposition`標頭，並且必須將其`name`參數設置為"知識請求"。 有關物件的詳細資訊，`imageInfo`請參閱請求：

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

本文中的範例將示範如何使用見解權杖。 從 /image/搜索`Image`API 回應中的物件獲取見解權杖。 有關獲取見解權杖的資訊，請參閱[什麼是必應圖像搜索 API？](../Bing-Image-Search/overview.md)

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```

有關使用見解權杖的示例，請參閱[C#](#use-with-c) | [JAVA](#use-with-java) | [Node.js](#use-with-nodejs) | [Python](#use-with-python)。

## <a name="use-with-c"></a>與 C 一起使用#

### <a name="c-prerequisites"></a>C# 先決條件

- 任何版本的[Visual Studio 2019，](https://www.visualstudio.com/downloads/)以在 Windows 上運行此代碼。
- Azure 訂用帳戶。 對於此快速入門，您可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)訂閱金鑰或付費訂閱金鑰。

## <a name="run-the-application"></a>執行應用程式

若要執行此應用程式，請遵循下列步驟：

1. 在視覺化工作室中創建主控台解決方案。
2. 將Program.cs的內容替換為此快速入門中顯示的代碼。
3. 以您的訂用帳戶金鑰取代 `accessKey` 值。
4. 以 /images/search 回應中的見解權杖取代 `insightsToken` 值。
5. 執行程式。

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
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


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

## <a name="use-with-java"></a>與 JAVA 一起使用

### <a name="java-prerequisites"></a>JAVA 先決條件

- 您必須使用[JDK 7 或 8](https://aka.ms/azure-jdks)來編譯和運行此代碼。 如果您有我的最愛，則可以使用 JAVA IDE，但文字編輯器就足夠了。
- 對於此快速入門，您可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)訂閱金鑰或付費訂閱金鑰。

## <a name="run-the-java-application"></a>運行 JAVA 應用程式

若要執行此應用程式，請遵循下列步驟：

1. 下載或安裝[GsonJAVA庫](https://github.com/google/gson)。 您也可以通過馬文獲得格森。
2. 在您最愛的 IDE 或編輯器中建立新的 Java 專案。
3. 在名為 `VisualSearch.java` 的檔案中新增提供的程式碼。
4. 以您的訂用帳戶金鑰取代 `subscriptionKey` 值。
5. 執行程式。

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }


}
```

## <a name="use-with-nodejs"></a>與 Node.js 一起使用

### <a name="nodejs-prerequisites"></a>Node.js 必要條件

- 您必須具有[Node.js 6](https://nodejs.org/en/download/)才能運行此代碼。
- 對於此快速入門，您可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)訂閱金鑰或付費訂閱金鑰。

## <a name="run-the-javascript-application"></a>運行 JavaScript 應用程式

若要執行此應用程式，請遵循下列步驟：

1. 建立專案的資料夾 (或使用您最愛的 IDE 或編輯器)。
2. 從命令提示字元或終端機，巡覽至您剛剛建立的資料夾。
3. 安裝 request 模組：
  
   ```
   npm install request  
   ```
1. 安裝 form-data 模組：  
   ```
   npm install form-data  
   ```
1. 建立名為 GetVisualInsights.js 的檔案，並在其中新增下列程式碼。
1. 以您的訂用帳戶金鑰取代 `subscriptionKey` 值。
1. 執行程式。  
   ```
   node GetVisualInsights.js
   ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```

## <a name="use-with-python"></a>與 Python 一起使用

### <a name="python-prerequisites"></a>Python 先決條件

- 您必須具有[Python 3](https://www.python.org/)才能運行此代碼。
- 在本快速入門中，您可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)的訂用帳戶金鑰或付費的訂用帳戶金鑰。

## <a name="run-the-python-application"></a>運行 Python 應用程式

若要執行此應用程式，請遵循下列步驟：

1. 在您最愛的 IDE 或編輯器中，建立新的 Python 專案。
2. 建立名為 visualsearch.py 的檔案，並新增本快速入門中所示的程式碼。
3. 以您的訂用帳戶金鑰取代 `SUBSCRIPTION_KEY` 值。
4. 執行程式。

```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests
import json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest': (None, formData)}


def main():

    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))


# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>後續步驟

[建立圖像式搜尋單頁 Web 應用程式](tutorial-bing-visual-search-single-page-app.md)  
[什麼是必應視覺化搜索 API？](overview.md)  
[試用認知服務](https://aka.ms/bingvisualsearchtryforfree)  
[取得免費試用的存取金鑰](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[圖片 - 視覺搜索](https://aka.ms/bingvisualsearchreferencedoc)
