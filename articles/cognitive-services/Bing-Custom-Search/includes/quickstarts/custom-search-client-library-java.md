---
title: Bing 自訂搜尋 Java 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: e175d77ec4684053070e817f8a6ec7e9455668b1
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78253090"
---
開始使用適用於 Java 的 Bing 自訂搜尋用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 Bing 自訂搜尋 API 可讓您針對感興趣的主題，建立量身訂做且無廣告的搜尋經驗。 此範例的原始程式碼位於 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

使用適用於 Java 的 Bing 自訂搜尋用戶端程式庫：

* 從您的 Bing 自訂搜尋執行個體，在 Web 上尋找搜尋結果。 

[參考文件](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [成品 (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [範例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)。
* 最新版的 [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [Gradle 建置工具](https://gradle.org/install/)，或其他相依性管理員。
* 「Bing 自訂搜尋」執行個體。 請參閱[快速入門：建立您的第一個 Bing 自訂搜尋執行個體](../../quick-start.md)，以取得詳細資訊。

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

從試用版訂用帳戶或資源取得金鑰後，請為名為 `AZURE_BING_CUSTOM_SEARCH_API_KEY` 的金鑰[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-gradle-project"></a>建立新的 Gradle 專案

> [!TIP]
> 如果您不是使用 Gradle，可以在 [Maven 中央存放庫](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)中找到其他相依性管理員的用戶端程式庫詳細資料。

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

從您的工作目錄執行 `gradle init` 命令。 此命令會建立 Gradle 的基本組建檔案，包括 build.gradle.kts  檔案，該檔案在執行階段用於設定您的應用程式。

```console
gradle init --type basic
```

出現選擇 **DSL** 的提示時，請選取 [Kotlin]  。

## <a name="install-the-client-library"></a>安裝用戶端程式庫 

找出 build.gradle.kts  ，並使用您慣用的 IDE 或文字編輯器加以開啟。 然後，在此組建組態中複製下列內容。 請務必在 `dependencies` 之下包含用戶端程式庫：

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

建立範例應用程式的資料夾。 從工作目錄執行下列命令：

```console
mkdir src/main/java
```

瀏覽至新的資料夾，並建立名為 BingCustomSearchSample.java  的檔案。 開啟該檔案，並新增下列 `import` 陳述式：


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

建立名為 `BingCustomSearchSample` 的類別

```java
public class BingCustomSearchSample {
}
```

在類別中，為資源的金鑰建立 `main` 方法和變數。 如果您在啟動應用程式後才建立環境變數，請先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。 您會在稍後定義方法。

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>物件模型

Bing 自訂搜尋用戶端是從 [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable)物件的 [authenticate()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_)方法建立的 [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) 物件。 您可以使用用戶端的 [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__)方法來傳送搜尋要求。

API 回應是 [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) 物件，其中包含搜尋查詢資訊和搜尋結果。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Java 的 Bing 自訂搜尋用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [從自訂搜尋執行個體中取得搜尋結果](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>驗證用戶端

您的 main 方法應包含 [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable)物件，該物件會採用您的金鑰並呼叫其 `authenticate()`。

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>從自訂搜尋執行個體中取得搜尋結果

使用用戶端的 [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__)函式，將搜尋查詢傳送至您的自訂執行個體。 將 `withCustomConfig` 設為您的自訂組態識別碼，或預設為 `1`。 從 API 取得回應之後，檢查是否找到任何搜尋結果。 若是如此，請呼叫回應的 `webPages().value().get()` 函式並列印結果的名稱和 URL，以取得第一筆搜尋結果。 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>執行應用程式

從您專案的主目錄，使用下列命令建置應用程式：

```console
gradle build
```

使用 `run` 目標執行應用程式：

```console
gradle run
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置自訂搜尋 Web 應用程式](../../tutorials/custom-search-web-page.md)
