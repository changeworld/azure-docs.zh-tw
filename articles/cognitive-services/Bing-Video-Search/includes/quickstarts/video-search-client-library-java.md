---
title: Bing 影片搜尋 Java 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: 8124afef1aa12dbf3ec51e10597cb1567fc85551
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80289912"
---
使用本快速入門，透過適用於 Java 的 Bing 影片搜尋用戶端程式庫開始搜尋新聞。 雖然 Bing 影片搜尋具有與大部分程式設計語言相容的 REST API，但此用戶端程式庫可提供簡單的方法，將服務整合到您的應用程式。 在 [Github](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch) 上可找到此範例的完整原始程式碼，以及其他註釋和功能。

## <a name="prerequisites"></a>Prerequisites

* [Java 開發套件 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* [Gson 程式庫](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

使用 Maven、Gradle 或另一個相依性管理系統，安裝 Bing 影片搜尋用戶端程式庫相依性。 Maven POM 檔案需要下列宣告：

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化


在您最愛的 IDE 或編輯器中建立新的 Java 專案，並匯入下列程式庫。

```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
```

## <a name="create-a-search-client"></a>建立搜尋用戶端

1. 實作 `VideoSearchAPIImpl` 用戶端，這需要 API 端點及 `ServiceClientCredentials` 類別的執行個體。

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    若要實作 `ServiceClientCredentials`，請遵循下列步驟：

    1. 以作為參數的 `OkHttpClient.Builder` 物件覆寫 `applyCredentialsFilter()` 函式。 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. 在 `applyCredentialsFilter()` 內呼叫 `builder.addNetworkInterceptor()`。 建立新的 `Interceptor` 物件，並覆寫其 `intercept()` 方法，以取用 `Chain` 攔截器物件。

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. 在 `intercept` 函式內，為您的要求建立變數。 使用 `Request.Builder()` 來建置您的要求。 將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭，然後傳回要求物件上的 `chain.proceed()`。
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```

## <a name="send-a-search-request-and-receive-the-response"></a>傳送搜尋要求並接收回應 

1. 建立名為 `VideoSearch()` 的函式，並將您的訂用帳戶金鑰當作字串。 具現化稍早建立的搜尋用戶端。
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. 在 `VideoSearch()` 內，使用用戶端傳送影片搜尋，並以 `SwiftKey` 作為搜尋字詞。 如果影片搜尋 API 傳回結果，取得第一個結果，並列印其識別碼、名稱和 URL，以及傳回的影片總數。 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. 從您的 main 方法呼叫 search 方法。

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>另請參閱 

* [什麼是 Bing 影片搜尋 API？](../../overview.md)
* [認知服務 .NET SDK 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
