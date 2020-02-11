---
title: 快速入門：適用於 C# 的文字分析用戶端程式庫 | Microsoft Docs
description: 開始使用適用於 C# 的文字分析用戶端程式庫
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: a907fb5347e6559e066f678c53bb16c7727a74b5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987875"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

[v3 參考文件](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [3 套件 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

[v2 參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>設定

### <a name="create-a-new-net-core-application"></a>建立新的 .NET Core 應用程式

使用 Visual Studio IDE，建立新的 .NET Core 主控台應用程式。 這會建立 "Hello World" 專案，內含單一 C# 原始程式檔：program.cs  。

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

以滑鼠右鍵按一下 [方案總管]  中的解決方案，然後選取 [管理 NuGet 套件]  ，以安裝用戶端程式庫。 在開啟的套件管理員中，選取 [瀏覽]  、核取 [包含發行前版本]  ，然後搜尋 `Azure.AI.TextAnalytics`。 在其上按一下，然後按一下 [安裝]  。 您也可以使用[套件管理員主控台](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)。

開啟 *program.cs* 檔案並新增下列 `using` 指示詞：

```csharp
using System;
using Azure.AI.TextAnalytics;
```

在應用程式的 `Program` 類別中，為資源的金鑰和端點建立變數。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

取代應用程式的 `Main` 方法。 稍後，您將會定義此處所呼叫的方法。

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

以滑鼠右鍵按一下 [方案總管]  中的解決方案，然後選取 [管理 NuGet 套件]  ，以安裝用戶端程式庫。 在開啟的封裝管理員中，選取 [瀏覽]  並搜尋 `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`。 在其上按一下，然後按一下 [安裝]  。 您也可以使用[套件管理員主控台](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)。

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

如果您使用 `3.0-preview` 版，您可以使用選擇性 `TextAnalyticsClientOptions` 執行個體來初始化具有各種預設設定 (例如，預設語言或國家/地區提示) 的用戶端。 您也可以使用 Azure Active Directory 權杖進行驗證。 

## <a name="code-examples"></a>程式碼範例

* [情感分析](#sentiment-analysis)
* [語言偵測](#language-detection)
* [具名實體辨識](#named-entity-recognition-ner)
* [實體連結](#entity-linking)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>驗證用戶端

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

確定您先前的 main 方法會使用您的端點和金鑰建立新的用戶端物件。 

```csharp
var client = new TextAnalyticsClient(endpoint, key);
```

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

建立新的 `ApiKeyServiceClientCredentials` 類別以儲存認證，並對用戶端的要求新增認證。 `ProcessHttpRequestAsync()` 的覆寫會建立在其中，以將您的金鑰新增 `Ocp-Apim-Subscription-Key` 標頭。

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

使用您的端點建立用於具現化 [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) 的方法，以及建立包含您金鑰的 `ApiKeyServiceClientCredentials` 物件。

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>情感分析

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

建立名為 `SentimentAnalysisExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `AnalyzeSentiment()` 函式。 傳回的 `Response<AnalyzeSentimentResult>` 物件會包含整個輸入文件的情感標籤和分數，且如果成功，還會包含每個句子的情感分析，如果未能成功，則會包含 `Value.ErrorMessage`。

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>輸出

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

建立名為 `SentimentAnalysisExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) 函式。 如果成功，傳回的 [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet)物件將會包含情感 `Score`，若不成功則為 `errorMessage`。 

接近 0 的分數表示負面人氣，而接近 1 的分數則表示正面人氣。

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>語言偵測

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)


建立名為 `LanguageDetectionExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `DetectLanguage()` 函式。 如果成功，傳回的 `Response<DetectLanguageResult>` 物件會在 `Value.PrimaryLanguage` 中包含所偵測到的語言，如果未能成功，則會包含 `Value.ErrorMessage`。

> [!Tip]
> 在某些情況下，可能難以根據輸入來區分語言。 您可以使用 `countryHint` 參數來指定 2 個字母的國碼。 根據預設，API 會使用 "US" 作為預設 countryHint，若要移除此行為，您可以將此值設定為空字串 `countryHint = ""`，以重設此參數。 若要設定不同的預設值，請設定 `TextAnalyticsClientOptions.DefaultCountryHint` 屬性，並在用戶端的初始化期間傳遞此屬性。

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>輸出

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

建立名為 `languageDetectionExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 函式。 如果成功，傳回的 [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) 物件將會在 `DetectedLanguages` 中包含偵測到的語言清單，若不成功則為 `errorMessage`。  列印第一個傳回的語言。

> [!Tip]
> 在某些情況下，可能難以根據輸入來區分語言。 您可以使用 `countryHint` 參數來指定 2 個字母的國碼。 根據預設，API 會使用 "US" 作為預設 countryHint，若要移除此行為，您可以將此值設定為空字串 `countryHint = ""`，以重設此參數。

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>輸出

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)


> [!NOTE]
> 在 `3.0-preview` 版中：
> * NER 包含各種用於偵測個人資訊的方法。 
> * 實體連結是不同於 NER 的要求。

下列程式碼可使用 NER v3 (其為公開預覽狀態) 來偵測個人資訊。


建立名為 `EntityRecognitionExample()` 的新函式，此函式會採用您稍早建立的用戶端、呼叫其 `RecognizeEntities()` 函式，並逐一查看結果。 如果成功，傳回的 `Response<RecognizeEntitiesResult>` 物件會在 `Value.NamedEntities` 中包含所偵測到實體的清單，若未能成功，則會包含 `Value.ErrorMessage`。 針對每個偵測到的實體，列印其類型和子類型 (如果有的話)。

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>輸出

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="using-ner-to-detect-personal-information"></a>使用 NER 來偵測個人資訊

建立名為 `EntityPIIExample()` 的新函式，此函式會採用您稍早建立的用戶端、呼叫其 `RecognizePiiEntities()` 函式，並逐一查看結果。 類似於上一個函式，如果成功，傳回的 `Response<RecognizeEntitiesResult>` 物件會在 `Value.NamedEntities` 中包含所偵測到實體的清單，若未能成功，則會包含 `Value.ErrorMessage`。

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>輸出

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>實體連結

建立名為 `EntityLinkingExample()` 的新函式，此函式會採用您稍早建立的用戶端、呼叫其 `RecognizeLinkedEntities()` 函式，並逐一查看結果。 如果成功，傳回的 `Response<RecognizeLinkedEntitiesResult>` 物件會在 `Value.LinkedEntities` 中包含所偵測到實體的清單，若未能成功，則會包含 `Value.ErrorMessage`。 因為連結的實體已識別為是唯一的，所以相同實體再次出現的話，就會群組到 `LinkedEntity` 物件底下作為 `LinkedEntityMatch` 物件的清單。

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
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
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
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

> [!NOTE]
> 在 2.1 版中，實體連結會包含在 NER 回應中。

建立名為 `RecognizeEntitiesExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 函式。 逐一查看結果。 如果成功，傳回的 [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) 物件將會在 `Entities` 中包含偵測到的語言清單，若不成功則為 `errorMessage`。 對於每個偵測到的實體，列印其類型、子類型、維基百科名稱 (如果存在)，以及在原始文字中的位置。

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>關鍵片語擷取

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

建立名為 `KeyPhraseExtractionExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 `ExtractKeyPhrases()` 函式。 如果成功，結果將會在 `Value.KeyPhrases` 中包含偵測到的關鍵片語清單，若未能成功，則會包含 `Value.ErrorMessage`。 列印任何偵測到的關鍵片語。

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
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

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

建立名為 `KeyPhraseExtractionExample()` 的新函式，該函式會使用您稍早建立的用戶端，並呼叫其 [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 函式。 如果成功，結果將會在 `KeyPhrases` 中包含偵測到的關鍵片語清單，若不成功則為 `errorMessage`。 列印任何偵測到的關鍵片語。

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>輸出

```console
Key phrases:
    cat
    veterinarian
```

---