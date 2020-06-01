---
title: 快速入門：使用 Java 呼叫您的 Bing 自訂搜尋端點 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 您可以使用本快速入門，開始透過 Java 要求 Bing 自訂搜尋執行個體所產生的搜尋結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: 292b94e56d988a1126db83e2f8ce40bbb0af6f42
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873248"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>快速入門：使用 Java 呼叫您的 Bing 自訂搜尋端點

使用本快速入門來了解如何向 Bing 自訂搜尋執行個體要求搜尋結果。 雖然此應用程式是以 Java 撰寫的，但 Bing 自訂搜尋 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java) 上有此範例的原始程式碼。

## <a name="prerequisites"></a>Prerequisites

- 「Bing 自訂搜尋」執行個體。 如需詳細資訊，請參閱[快速入門：建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)。

- 最新的 [Java 開發套件](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。

- [Gson 程式庫](https://github.com/google/gson)。

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您慣用的 IDE 或編輯器中建立新的 Java 專案，並匯入下列程式庫：

    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. 建立名為 `CustomSrchJava` 的類別，然後建立訂用帳戶金鑰、自訂搜尋端點和搜尋執行個體的自訂組態識別碼的變數。 您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../cognitive-services/cognitive-services-custom-subdomains.md)端點。
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. 建立名為 `SearchResults` 的另一個類別，以包含 Bing 自訂搜尋執行個體的回應。

    ```java
    class SearchResults {
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. 建立名為 `prettify()` 函式，用以格式化 Bing 自訂搜尋 API 的 JSON 回應。

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>傳送及接收搜尋要求 

1. 建立名為 `SearchWeb()` 的函式，用以傳送要求和傳回 `SearchResults` 物件。 結合您的自訂組態識別碼、查詢和端點資訊，以建立要求 URL。 將訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. 建立資料流，並將 JSON 回應儲存在 `SearchResults` 物件中。

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. 列印 JSON 回應。

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. 執行程式。
    
## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置自訂搜尋 Web 應用程式](./tutorials/custom-search-web-page.md)
