---
title: 快速入門：使用 REST API 和 Java 檢查拼字 - Bing 拼字檢查
titleSuffix: Azure Cognitive Services
description: 開始使用 Bing 拼字檢查 REST API 和 Java 來檢查拼字和文法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: e74954e33be1b2d24219ca61762dd43941415306
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366973"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>快速入門：使用 Bing 拼字檢查 REST API 和 Java 進行拼字檢查

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

使用本快速入門，第一次呼叫 Bing 拼字檢查 REST API。 這個簡單的 Java 應用程式會將要求傳送至 API，並傳回建議的更正清單。 

雖然此應用程式是以 Java 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java) 上找到此應用程式的原始程式碼。

## <a name="prerequisites"></a>Prerequisites

* Java 開發套件 (JDK) 7 或更新版本。

* 匯入 [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) 或最新的 [Gson](https://github.com/google/gson) 版本。 對於命令列執行，將 `.jar` 新增至主要類別的 Java 資料夾中。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>建立應用程式並將其初始化

1. 在您慣用的 IDE 或編輯器中，使用您選擇的類別名稱建立新的 Java 專案，並匯入下列套件：

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. 為 API 端點的主機、路徑和訂用帳戶金鑰建立變數。 然後，為您的市場、您要進行拼字檢查的文字，以及拼字檢查模式的字串建立變數。 您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>建立和傳送 API 要求

1. 建立名為 `check()` 的函式，以建立及傳送 API 要求。 在此函式內，新增後續步驟中指定的程式碼。 建立要求參數的字串：

   1. 使用 `=` 運算子，將您的市場代碼指派給 `mkt` 參數。 

   1. 使用 `&` 運算子新增 `mode` 參數，然後指派拼字檢查模式。 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. 結合端點主機、路徑和參數字串以建立 URL。 建立新的 `HttpsURLConnection` 物件。

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. 開啟 URL 的連線。 將要求方法設定為 `POST` 並新增您的要求參數。 請務必將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. 建立新的 `DataOutputStream` 物件，並將要求傳送至 API。

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>格式化並讀取 API 回應

1. 將 `prettify()` 方法新增至您的類別，以將 JSON 格式化為更容易閱讀的輸出。

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. 建立 `BufferedReader`，並讀取 API 的回應。 將其列印至主控台。
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>呼叫 API

在應用程式的 main 函式中，呼叫您在先前建立的 `check()` 方法。
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>執行應用程式

建置並執行專案。 如果您使用命令列，請使用下列命令建置並執行該應用程式：

1. 建置應用程式：

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. 執行應用程式：

   ```bash
   java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
   ```

## <a name="example-json-response"></a>範例 JSON 回應

如以下範例所示，成功的回應會以 JSON 格式來傳回：

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../tutorials/spellcheck.md)

- [什麼是 Bing 拼字檢查 API？](../overview.md)
- [Bing 拼字檢查 API v7 參考](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)