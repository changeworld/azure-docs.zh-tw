---
title: 快速入門：適用於 C# 的文字分析用戶端程式庫 | Microsoft Docs
description: 開始使用適用於 C# 的文字分析用戶端程式庫
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 12/11/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: aee064a15d465c412d16dd313279168c37af0e98
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947657"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

[v3.1 參考文件](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-previews) | [v3.1 程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [v3.1 套件 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.3) | [v3.1 範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[3.0 版](#tab/version-3)

[v3 參考文件](/dotnet/api/azure.ai.textanalytics) | [v3 程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics) | [3 套件 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 範例](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-21"></a>[2.1 版](#tab/version-2)

[v2 參考文件](/dotnet/api/overview/azure/cognitiveservices/client) | [v2 程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="建立文字分析資源"  target="_blank">建立文字分析資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。  在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至文字分析 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 若要使用分析功能，您將需要具有標準定價層的文字分析資源。

## <a name="setting-up"></a>設定

### <a name="create-a-new-net-core-application"></a>建立新的 .NET Core 應用程式

使用 Visual Studio IDE，建立新的 .NET Core 主控台應用程式。 這會建立 "Hello World" 專案，內含單一 C# 原始程式檔：program.cs。

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

以滑鼠右鍵按一下 [方案總管] 中的解決方案，然後選取 [管理 NuGet 套件]，以安裝用戶端程式庫。 在開啟的封裝管理員中，選取 [瀏覽] 並搜尋 `Azure.AI.TextAnalytics`。 核取 [包含前置版本] 方塊、選取版本 `5.1.0-beta.3`，然後選取 [安裝]。 您也可以使用[套件管理員主控台](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)。

# <a name="version-30"></a>[3.0 版](#tab/version-3)

以滑鼠右鍵按一下 [方案總管] 中的解決方案，然後選取 [管理 NuGet 套件]，以安裝用戶端程式庫。 在開啟的封裝管理員中，選取 [瀏覽] 並搜尋 `Azure.AI.TextAnalytics`。 選取版本 `5.0.0`，然後 **安裝**。 您也可以使用[套件管理員主控台](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)。


> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs) 上找到該檔案，其中包含本快速入門中的程式碼範例。 

# <a name="version-21"></a>[2.1 版](#tab/version-2)

以滑鼠右鍵按一下 [方案總管] 中的解決方案，然後選取 [管理 NuGet 套件]，以安裝用戶端程式庫。 在開啟的封裝管理員中，選取 [瀏覽] 並搜尋 `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`。 在其上按一下，然後按一下 [安裝]。 您也可以使用[套件管理員主控台](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)。

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs) 上找到該檔案，其中包含本快速入門中的程式碼範例。 

---

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

開啟 *program.cs* 檔案並新增下列 `using` 指示詞：

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

在應用程式的 `Program` 類別中，為資源的金鑰和端點建立變數。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

取代應用程式的 `Main` 方法。 稍後，您將會定義此處所呼叫的方法。

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[3.0 版](#tab/version-3)

開啟 *program.cs* 檔案並新增下列 `using` 指示詞：

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

在應用程式的 `Program` 類別中，為資源的金鑰和端點建立變數。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

取代應用程式的 `Main` 方法。 稍後，您將會定義此處所呼叫的方法。

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-21"></a>[2.1 版](#tab/version-2)

開啟 *program.cs* 檔案並新增下列 `using` 指示詞：

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

在應用程式的 `Program` 類別中，為資源的金鑰和端點建立變數。 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

取代應用程式的 `Main` 方法。 稍後，您將會定義此處所呼叫的方法。

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>物件模型

文字分析用戶端是 `TextAnalyticsClient` 物件，會使用您的金鑰向 Azure 進行驗證，並提供可接受文字作為單一字串或批次的函式。 您可以透過同步或非同步方式將文字傳送至 API。 回應物件會包含每個傳送文件的分析資訊。 

如果您使用服務的 `3.x` 版，您可以使用選擇性 `TextAnalyticsClientOptions` 執行個體來初始化具有各種預設設定 (例如，預設語言或國家/區域提示) 的用戶端。 您也可以使用 Azure Active Directory 權杖進行驗證。 

## <a name="code-examples"></a>程式碼範例

* [情感分析](#sentiment-analysis)
* [意見挖掘](#opinion-mining)
* [語言偵測](#language-detection)
* [具名實體辨識](#named-entity-recognition-ner)
* [實體連結](#entity-linking)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>驗證用戶端

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

確定您先前的 main 方法會使用您的端點和認證建立新的用戶端物件。

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[3.0 版](#tab/version-3)

確定您先前的 main 方法會使用您的端點和認證建立新的用戶端物件。

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-21"></a>[2.1 版](#tab/version-2)

建立新的 `ApiKeyServiceClientCredentials` 類別以儲存認證，並對用戶端的要求新增認證。 `ProcessHttpRequestAsync()` 的覆寫會建立在其中，以將您的金鑰新增 `Ocp-Apim-Subscription-Key` 標頭。

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

使用您的端點建立用於具現化 [TextAnalyticsClient](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient) 的方法，以及建立包含您金鑰的 `ApiKeyServiceClientCredentials` 物件。

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>情感分析

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

建立名為 `SentimentAnalysisExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `AnalyzeSentiment()` 函式。 傳回的 `Response<DocumentSentiment>` 物件會包含整個輸入文件的情感標籤和分數，且如果成功，還會包含每個句子的情感分析。 如果發生錯誤，則會擲回 `RequestFailedException`。

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>輸出

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>意見挖掘

建立名為 `SentimentAnalysisWithOpinionMiningExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `AnalyzeSentimentBatch()` 函式搭配 `AdditionalSentimentAnalyses.OpinionMining` 選項。 傳回的 `AnalyzeSentimentResultCollection` 物件將包含 `AnalyzeSentimentResult` 的集合，其中代表 `Response<DocumentSentiment>`。 `SentimentAnalysis()` 與 `SentimentAnalysisWithOpinionMiningExample()` 之間的差異，在於後者會包含每個句子中的 `MinedOpinion`，這會顯示分析的層面和相關意見。 如果發生錯誤，則會擲回 `RequestFailedException`。

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (MinedOpinion minedOpinion in sentence.MinedOpinions)
            {
                Console.WriteLine($"\tAspect: {minedOpinion.Aspect.Text}, Value: {minedOpinion.Aspect.Sentiment}");
                Console.WriteLine($"\tAspect positive score: {minedOpinion.Aspect.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tAspect negative score: {minedOpinion.Aspect.ConfidenceScores.Negative:0.00}");
                foreach (OpinionSentiment opinion in minedOpinion.Opinions)
                {
                    Console.WriteLine($"\t\tRelated Opinion: {opinion.Text}, Value: {opinion.Sentiment}");
                    Console.WriteLine($"\t\tRelated Opinion positive score: {opinion.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Opinion negative score: {opinion.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>輸出

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Aspect: food, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: service, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: concierge, Value: Positive
        Aspect positive score: 1.00
        Aspect negative score: 0.00
                Related Opinion: nice, Value: Positive
                Related Opinion positive score: 1.00
                Related Opinion negative score: 0.00


Press any key to exit.
```

# <a name="version-30"></a>[3.0 版](#tab/version-3)

建立名為 `SentimentAnalysisExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `AnalyzeSentiment()` 函式。 傳回的 `Response<DocumentSentiment>` 物件會包含整個輸入文件的情感標籤和分數，且如果成功，還會包含每個句子的情感分析。 如果發生錯誤，則會擲回 `RequestFailedException`。

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>輸出

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

# <a name="version-21"></a>[2.1 版](#tab/version-2)

建立名為 `SentimentAnalysisExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 [Sentiment()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment) 函式。 如果成功，傳回的 [SentimentResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult)物件將會包含情感 `Score`，若不成功則為 `errorMessage`。 

接近 0 的分數表示負面人氣，而接近 1 的分數則表示正面人氣。

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>語言偵測

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)


建立名為 `LanguageDetectionExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `DetectLanguage()` 函式。 傳回的 `Response<DetectedLanguage>` 物件會包含偵測到的語言，以及其名稱和 ISO-6391 代碼。 如果發生錯誤，則會擲回 `RequestFailedException`。

> [!Tip]
> 在某些情況下，可能難以根據輸入來區分語言。 您可以使用 `countryHint` 參數來指定 2 個字母的國碼/區域碼。 根據預設，API 會使用 "US" 作為預設 countryHint，若要移除此行為，您可以將此值設定為空字串 `countryHint = ""`，以重設此參數。 若要設定不同的預設值，請設定 `TextAnalyticsClientOptions.DefaultCountryHint` 屬性，並在用戶端的初始化期間傳遞此屬性。

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>輸出

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[3.0 版](#tab/version-3)


建立名為 `LanguageDetectionExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `DetectLanguage()` 函式。 傳回的 `Response<DetectedLanguage>` 物件會包含偵測到的語言，以及其名稱和 ISO-6391 代碼。 如果發生錯誤，則會擲回 `RequestFailedException`。

> [!Tip]
> 在某些情況下，可能難以根據輸入來區分語言。 您可以使用 `countryHint` 參數來指定 2 個字母的國碼/區域碼。 根據預設，API 會使用 "US" 作為預設 countryHint，若要移除此行為，您可以將此值設定為空字串 `countryHint = ""`，以重設此參數。 若要設定不同的預設值，請設定 `TextAnalyticsClientOptions.DefaultCountryHint` 屬性，並在用戶端的初始化期間傳遞此屬性。

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>輸出

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-21"></a>[2.1 版](#tab/version-2)

建立名為 `languageDetectionExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 [DetectLanguage()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 函式。 如果成功，傳回的 [LanguageResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult) 物件將會在 `DetectedLanguages` 中包含偵測到的語言清單，若不成功則為 `errorMessage`。 列印第一個傳回的語言。

> [!Tip]
> 在某些情況下，可能難以根據輸入來區分語言。 您可以使用 `countryHint` 參數來指定 2 個字母的國碼/區域碼。 根據預設，API 會使用 "US" 作為預設 countryHint，若要移除此行為，您可以將此值設定為空字串 `countryHint = ""`，以重設此參數。

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>輸出

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)


建立名為 `EntityRecognitionExample()` 的新函式，此函式會採用您稍早建立的用戶端、呼叫其 `RecognizeEntities()` 函式，並逐一查看結果。 傳回的 `Response<CategorizedEntityCollection>` 物件會包含偵測到的實體 `CategorizedEntity` 集合。 如果發生錯誤，則會擲回 `RequestFailedException`。

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>輸出

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>實體連結

建立名為 `EntityLinkingExample()` 的新函式，此函式會採用您稍早建立的用戶端、呼叫其 `RecognizeLinkedEntities()` 函式，並逐一查看結果。 傳回的 `Response<LinkedEntityCollection>` 物件會包含偵測到的實體 `LinkedEntity` 集合。 如果發生錯誤，則會擲回 `RequestFailedException`。 因為連結的實體已識別為是唯一的，所以相同實體再次出現的話，就會群組到 `LinkedEntity` 物件底下作為 `LinkedEntityMatch` 物件的清單。

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>輸出

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>個人識別資訊辨識

建立名為 `RecognizePIIExample()` 的新函式，此函式會採用您稍早建立的用戶端、呼叫其 `RecognizePiiEntities()` 函式，並逐一查看結果。 傳回的 `PiiEntityCollection` 代表偵測到的 PII 實體清單。 如果發生錯誤，則會擲回 `RequestFailedException`。

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>輸出

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[3.0 版](#tab/version-3)


> [!NOTE]
> `3.0` 版本中的新功能：
> * 實體連結現已與實體辨識分開。


建立名為 `EntityRecognitionExample()` 的新函式，此函式會採用您稍早建立的用戶端、呼叫其 `RecognizeEntities()` 函式，並逐一查看結果。 傳回的 `Response<IReadOnlyCollection<CategorizedEntity>>` 物件會包含偵測到的實體清單。 如果發生錯誤，則會擲回 `RequestFailedException`。

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>輸出

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>實體連結

建立名為 `EntityLinkingExample()` 的新函式，此函式會採用您稍早建立的用戶端、呼叫其 `RecognizeLinkedEntities()` 函式，並逐一查看結果。 傳回的 `Response<IReadOnlyCollection<LinkedEntity>>` 表示偵測到的實體清單。 如果發生錯誤，則會擲回 `RequestFailedException`。 因為連結的實體已識別為是唯一的，所以相同實體再次出現的話，就會群組到 `LinkedEntity` 物件底下作為 `LinkedEntityMatch` 物件的清單。

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>輸出

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

# <a name="version-21"></a>[2.1 版](#tab/version-2)

> [!NOTE]
> 在 2.1 版中，實體連結會包含在 NER 回應中。

建立名為 `RecognizeEntitiesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 [Entities()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 函式。 逐一查看結果。 如果成功，傳回的 [EntitiesResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult) 物件將會在 `Entities` 中包含偵測到的語言清單，若不成功則為 `errorMessage`。 對於每個偵測到的實體，列印其類型、子類型、維基百科名稱 (如果存在)，以及在原始文字中的位置。

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


### <a name="key-phrase-extraction"></a>關鍵片語擷取

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

建立名為 `KeyPhraseExtractionExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `ExtractKeyPhrases()` 函式。 傳回的 `<Response<KeyPhraseCollection>` 物件會包含偵測到的關鍵片語清單。 如果發生錯誤，則會擲回 `RequestFailedException`。

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>輸出

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[3.0 版](#tab/version-3)

建立名為 `KeyPhraseExtractionExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `ExtractKeyPhrases()` 函式。 傳回的 `<Response<IReadOnlyCollection<string>>` 物件會包含偵測到的關鍵片語清單。 如果發生錯誤，則會擲回 `RequestFailedException`。

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>輸出

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-21"></a>[2.1 版](#tab/version-2)

建立名為 `KeyPhraseExtractionExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 [KeyPhrases()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 函式。 如果成功，結果將會在 `KeyPhrases` 中包含偵測到的關鍵片語清單，若不成功則為 `errorMessage`。 列印任何偵測到的關鍵片語。

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>輸出

```console
Key phrases:
    cat
    veterinarian
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>以非同步方式使用 API 與分析作業搭配

# <a name="version-31-preview"></a>[3.1 版預覽](#tab/version-3-1)

> [!CAUTION]
> 若要使用分析作業，請確定您的 Azure 資源正在使用標準定價層。

建立名為 `AnalyzeOperationExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `StartAnalyzeOperationBatch()` 函式。 傳回的 `AnalyzeOperation` 物件將包含 `AnalyzeOperationResult` 的 `Operation` 介面物件。 因為這是長時間執行的作業，所以請在 `operation.WaitForCompletionAsync()` 上 `await`，以更新值。 一旦 `WaitForCompletionAsync()` 完成，就應該在 `operation.Value` 中更新集合。 如果發生錯誤，則會擲回 `RequestFailedException`。


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };

    AnalyzeOperationOptions operationOptions = new AnalyzeOperationOptions()
    {
        EntitiesTaskParameters = new EntitiesTaskParameters(),
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeOperation operation = client.StartAnalyzeOperationBatch(batchDocuments, operationOptions, "en");

    await operation.WaitForCompletionAsync();

    AnalyzeOperationResult resultCollection = operation.Value;

    RecognizeEntitiesResultCollection entitiesResult = resultCollection.Tasks.EntityRecognitionTasks[0].Results;

    Console.WriteLine("Analyze Operation Request Details");
    Console.WriteLine($"    Status: {resultCollection.Status}");
    Console.WriteLine($"    DisplayName: {resultCollection.DisplayName}");
    Console.WriteLine("");

    Console.WriteLine("Recognized Entities");

    foreach (RecognizeEntitiesResult result in entitiesResult)
    {
        Console.WriteLine($"    Recognized the following {result.Entities.Count} entities:");

        foreach (CategorizedEntity entity in result.Entities)
        {
            Console.WriteLine($"    Entity: {entity.Text}");
            Console.WriteLine($"    Category: {entity.Category}");
            Console.WriteLine($"    Offset: {entity.Offset}");
            Console.WriteLine($"    ConfidenceScore: {entity.ConfidenceScore}");
            Console.WriteLine($"    SubCategory: {entity.SubCategory}");
        }
        Console.WriteLine("");
    }
}
```

將此範例新增至您的應用程式之後，請使用 `await` 在 `main()` 方法中呼叫。

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>輸出

```console
Analyze Operation Request Details
    Status: succeeded
    DisplayName: Analyze Operation Quick Start Example

Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

您也可以使用分析作業來偵測 PII 和關鍵片語擷取。 請參閱 GitHub 上的[分析範例](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeOperation.md)。

# <a name="version-30"></a>[3.0 版](#tab/version-3)

3\.0 版無法使用此功能。

# <a name="version-21"></a>[2.1 版](#tab/version-2)

2\.1 版無法使用此功能。

---
