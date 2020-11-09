---
title: 快速入門：使用 REST API 和 Java 來取得影像見解 - Bing 圖像式搜尋
titleSuffix: Azure Cognitive Services
description: 了解如何將影像上傳到 Bing 圖像式搜尋 API 並取得其見解。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-java
ms.author: scottwhi
ms.openlocfilehash: f33b5f5274fcaf8f64f898e2b7aebe8a27470033
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086528"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>快速入門：使用 Bing 圖像式搜尋 REST API 和 Java 來取得影像見解

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照[這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

使用本快速入門，第一次呼叫 Bing 圖像式搜尋 API。 此 Java 應用程式會將影像上傳至 API，並顯示它傳回的資訊。 雖然此應用程式是以 Java 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>Prerequisites

* [Java 開發套件 (JDK) 7 或 8](https://aka.ms/azure-jdks)
* [Gson Java 程式庫](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在您慣用的 IDE 或編輯器中建立新的 Java 專案，並匯入下列程式庫：

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. 為您的 API 端點、訂用帳戶金鑰以及您影像的路徑，建立變數。 對於 `endpoint` 值，您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
3. 上傳本機影像時，表單資料必須包含 `Content-Disposition` 標頭。 將其 `name` 參數設為 "image"，以及將 `filename` 參數設為影像的檔案名稱。 表單的內容包含影像的二進位資料。 您可以上傳的影像大小上限為 1 MB。
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>建立 JSON 剖析器

建立一個方法，讓來自 API 的 JSON 回應更容易使用 `JsonParser` 閱讀。

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>建構搜尋要求和查詢

1. 在應用程式的 Main 方法中，使用 `HttpClientBuilder.create().build();` 建立 HTTP 用戶端。

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. 建立 `HttpEntity` 物件以將影像上傳至 API。

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. 建立端點的 `httpPost` 物件，並設定要使用您訂用帳戶金鑰的標頭。

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>接收及處理 JSON 回應

1. 使用 `HttpClient.execute()` 方法將要求傳送給 API，並且在 `InputStream` 物件中儲存回應。
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. 儲存 JSON 字串並列印回應。

    ```java
    String json = new Scanner(stream).useDelimiter("\\A").next();
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(json));
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置圖像式搜尋單頁 Web 應用程式](../tutorial-bing-visual-search-single-page-app.md)
