---
title: 快速入門：適用於 Java 的文字分析 v3 用戶端程式庫 | Microsoft Docs
description: 開始使用適用於 Java 的 v3 文字分析用戶端程式庫。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 2788b22d2b262cfef4b214d75813a203067462cb
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779625"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

[參考文件](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-stable) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.1/sdk/textanalytics/azure-ai-textanalytics) | [套件](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1) | [範例](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.1/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[3.0 版](#tab/version-3)

[參考文件](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-stable) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics) | [套件](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) | [範例](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-21"></a>[2.1 版](#tab/version-2)

本文僅描述 3.x 版的 API。

---

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* [Java 開發套件](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) 含第 8 版或更新版本
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="建立文字分析資源"  target="_blank">建立文字分析資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。  在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至文字分析 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="add-the-client-library"></a>新增用戶端程式庫

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

在您慣用的 IDE 或開發環境中建立 Maven 專案。 然後，在專案的 *pom.xml* 檔案中新增下列相依性。 您可以在線上找到[其他建置工具](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1)的實作語法。

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.1</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[3.0 版](#tab/version-3)

在您慣用的 IDE 或開發環境中建立 Maven 專案。 然後，在專案的 *pom.xml* 檔案中新增下列相依性。 您可以在線上找到[其他建置工具](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0)的實作語法。

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java) 上找到該檔案，其中包含本快速入門中的程式碼範例。 

# <a name="version-21"></a>[2.1 版](#tab/version-2)

本文僅描述 3.x 版的 API。

---

建立名為 `TextAnalyticsSamples.java` 的 Java 檔案。 開啟該檔案，並新增下列 `import` 陳述式：

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

在 Java 檔案中，新增新的類別，並新增 Azure 資源的金鑰和端點，如下所示。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

將下列 main 方法新增至類別。 稍後，您將會定義此處所呼叫的方法。

# <a name="version-31-preview"></a>[3.1 版 (預覽)](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[3.0 版](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-21"></a>[2.1 版](#tab/version-2)

本文僅描述 3.x 版的 API。

---


## <a name="object-model"></a>物件模型

文字分析用戶端是 `TextAnalyticsClient` 物件，會使用您的金鑰向 Azure 進行驗證，並提供可接受文字作為單一字串或批次的函式。 您可以透過同步或非同步方式將文字傳送至 API。 回應物件會包含每個傳送文件的分析資訊。 

## <a name="code-examples"></a>程式碼範例

* [驗證用戶端](#authenticate-the-client)
* [情感分析](#sentiment-analysis) 
* [語言偵測](#language-detection)
* [具名實體辨識](#named-entity-recognition-ner)
* [實體連結](#entity-linking)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>驗證用戶端

建立適當方法，以使用文字分析資源的金鑰和端點將 `TextAnalyticsClient` 物件具現化。 這個範例對於 3.0 版和 3.1 版的 API 皆相同。

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


在程式的 `main()` 方法中，呼叫驗證方法來具現化用戶端。

## <a name="sentiment-analysis"></a>情感分析

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

> [!NOTE]
> 在 `3.1` 版中：
> * 情感分析包含意見挖掘分析，這是選擇性旗標。 
> * 意見挖掘包含外觀和意見層級情緒。 

建立名為 `sentimentAnalysisExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `analyzeSentiment()` 函式。 如果成功，傳回的 `AnalyzeSentimentResult` 物件會包含 `documentSentiment` 和 `sentenceSentiments`，如果未能成功，則會包含 `errorMessage`。 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>輸出

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

### <a name="opinion-mining"></a>意見挖掘

若要執行情感分析與意見挖掘，請建立名為 `sentimentAnalysisWithOpinionMiningExample()` 的新函式，此函式會採用您稍早建立的用戶端，並呼叫其 `analyzeSentiment()` 函式搭配設定選項物件 `AnalyzeSentimentOptions`。 如果成功，傳回的 `AnalyzeSentimentResult` 物件會包含 `documentSentiment` 和 `sentenceSentiments`，如果未能成功，則會包含 `errorMessage`。 


```java
static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    // The Document that needs be analyzed.
    String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

    System.out.printf("Document = %s%n", document);

    AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
    final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
    SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
    System.out.printf(
            "\tRecognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
            documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());

    documentSentiment.getSentences().forEach(sentenceSentiment -> {
        SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
        System.out.printf("\t\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
        sentenceSentiment.getMinedOpinions().forEach(minedOpinions -> {
            AspectSentiment aspectSentiment = minedOpinions.getAspect();
            System.out.printf("\t\t\tAspect sentiment: %s, aspect text: %s%n", aspectSentiment.getSentiment(),
                    aspectSentiment.getText());
            for (OpinionSentiment opinionSentiment : minedOpinions.getOpinions()) {
                System.out.printf("\t\t\t\t'%s' opinion sentiment because of \"%s\". Is the opinion negated: %s.%n",
                        opinionSentiment.getSentiment(), opinionSentiment.getText(), opinionSentiment.isNegated());
            }
        });
    });
}
```

### <a name="output"></a>輸出

```console
Text = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
    Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
        Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
            Aspect sentiment: negative, aspect text: atmosphere
                'negative' opinion sentiment because of "bad". Is the opinion negated: false.
        Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
        Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
            Aspect sentiment: negative, aspect text: Staff
                'negative' opinion sentiment because of "friendly". Is the opinion negated: true.
                'negative' opinion sentiment because of "helpful". Is the opinion negated: true.

Process finished with exit code 0
```

# <a name="version-30"></a>[3.0 版](#tab/version-3)

建立名為 `sentimentAnalysisExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `analyzeSentiment()` 函式。 如果成功，傳回的 `AnalyzeSentimentResult` 物件會包含 `documentSentiment` 和 `sentenceSentiments`，如果未能成功，則會包含 `errorMessage`。 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>輸出

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

# <a name="version-21"></a>[2.1 版](#tab/version-2)

本文僅描述 3.x 版的 API。

---

## <a name="language-detection"></a>語言偵測

建立名為 `detectLanguageExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `detectLanguage()` 函式。 如果成功，傳回的 `DetectLanguageResult` 物件會包含所偵測到的主要語言和所偵測到其他語言的清單，如果未能成功，則會包含 `errorMessage`。 這個範例對於 3.0 版和 3.1 版的 API 皆相同。

> [!Tip]
> 在某些情況下，可能難以根據輸入來區分語言。 您可以使用 `countryHint` 參數來指定 2 個字母的國碼。 根據預設，API 會使用 "US" 作為預設 countryHint，若要移除此行為，您可以將此值設定為空字串 `countryHint = ""`，以重設此參數。 若要設定不同的預設值，請設定 `TextAnalyticsClientOptions.DefaultCountryHint` 屬性，並在用戶端的初始化期間傳遞此屬性。

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>輸出

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

> [!NOTE]
> 在 `3.1` 版中：
> * NER 包含各種用於偵測個人資訊的方法。 
> * 實體連結是不同於 NER 的要求。

建立名為 `recognizeEntitiesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `recognizeEntities()` 函式。 如果成功，傳回的 `CategorizedEntityCollection` 物件會包含 `CategorizedEntity` 的清單，如果未能成功，則會包含 `errorMessage`。

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>輸出

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```

### <a name="entity-linking"></a>實體連結

建立名為 `recognizeLinkedEntitiesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `recognizeLinkedEntities()` 函式。 如果成功，傳回的 `LinkedEntityCollection` 物件會包含 `LinkedEntity` 的清單，如果未能成功，則會包含 `errorMessage`。 因為連結的實體已識別為是唯一的，所以相同實體再次出現的話，就會群組到 `LinkedEntity` 物件底下作為 `LinkedEntityMatch` 物件的清單。


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>輸出

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```


### <a name="personally-identifiable-information-recognition"></a>個人識別資訊辨識

建立名為 `recognizePiiEntitiesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `recognizePiiEntities()` 函式。 如果成功，傳回的 `PiiEntityCollection` 物件會包含 `PiiEntity` 的清單，如果未能成功，則會包含 `errorMessage`。 也會包含編校的文字，這是由輸入文字組成，而且所有可識別的實體都會取代為 `*****`。

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>輸出

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[3.0 版](#tab/version-3)

> [!NOTE]
> 在 `3.0` 版中：
> * NER 包含各種用於偵測個人資訊的方法。 
> * 實體連結是不同於 NER 的要求。

建立名為 `recognizeEntitiesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `recognizeEntities()` 函式。 如果成功，傳回的 `CategorizedEntityCollection` 物件會包含 `CategorizedEntity` 的清單，如果未能成功，則會包含 `errorMessage`。

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>輸出

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

### <a name="entity-linking"></a>實體連結

建立名為 `recognizeLinkedEntitiesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `recognizeLinkedEntities()` 函式。 如果成功，傳回的 `LinkedEntityCollection` 物件會包含 `LinkedEntity` 的清單，如果未能成功，則會包含 `errorMessage`。 因為連結的實體已識別為是唯一的，所以相同實體再次出現的話，就會群組到 `LinkedEntity` 物件底下作為 `LinkedEntityMatch` 物件的清單。

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>輸出

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```

# <a name="version-21"></a>[2.1 版](#tab/version-2)

本文僅描述 3.x 版的 API。

---

## <a name="key-phrase-extraction"></a>關鍵片語擷取

建立名為 `extractKeyPhrasesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `extractKeyPhrases()` 函式。 如果成功，傳回的 `ExtractKeyPhraseResult` 物件會包含關鍵片語的清單，如果未能成功，則會包含 `errorMessage`。 這個範例對於 3.0 版和 3.1 版的 API 皆相同。

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>輸出

```console
Recognized phrases: 
cat
veterinarian
```