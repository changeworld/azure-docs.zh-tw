---
title: Bing 實體搜尋 Java 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 8c987aa14e922573d01aa35fab609edf01e109b4
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136642"
---
使用本快速入門，透過適用於 Java 的 Bing 實體搜尋用戶端程式庫開始搜尋實體。 雖然 Bing 實體搜尋具有與大部分程式設計語言相容的 REST API，但此用戶端程式庫可提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch) 上找到。

## <a name="prerequisites"></a>Prerequisites

* [Java 開發套件 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* 適用於 Java 的 Bing 實體搜尋用戶端程式庫

使用 Maven、Gradle 或另一個相依性管理系統，安裝 Bing 實體搜尋用戶端程式庫相依性。 Maven POM 檔案需要宣告：

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 Java 專案，並匯入下列程式庫。

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. 建立訂用帳戶金鑰的變數

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>建立搜尋用戶端

1. 實作 `dominantEntityLookup` 用戶端，這需要 API 端點及 `ServiceClientCredentials` 類別的執行個體。 您可以使用下方的全域端點，也可以使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
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
## <a name="send-a-request-and-receive-a-response"></a>傳送要求並接收回應

1. 使用您的訂用帳戶金鑰建立新的搜尋用戶端執行個體。 使用 `client.entities().search()` 傳送搜尋查詢 `satya nadella` 的搜尋要求，並取得回應。 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. 如果傳回任何實體，請將其轉換為清單。 逐一查看這些項目，並列印主控實體。

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entries = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entries) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置單頁 Web 應用程式](../../tutorial-bing-entities-search-single-page-app.md)

* [什麼是 Bing 實體搜尋 API？](../../overview.md)
