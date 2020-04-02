---
title: 快速入門：適用於 Ruby 的文字分析用戶端程式庫 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您可以使用 Azure 認知服務中的 Ruby 文字分析用戶端程式庫來偵測語言。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0d4d32a413dd22c55f1b2f01dce3a3df81f5f729
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77919663"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>快速入門：使用適用於 Ruby 的文字分析用戶端程式庫

開始使用文字分析用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

使用文字分析用戶端程式庫執行：

* 情感分析
* 語言偵測
* 實體辨識
* 關鍵片語擷取

> [!NOTE]
> 本快速入門僅適用於文字分析 2.1 版。 目前無法使用適用於 Ruby 的 v3 用戶端程式庫。

[參考文件](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [套件 (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版的 [Ruby](https://www.ruby-lang.org/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="建立文字分析資源"  target="_blank">建立文字分析資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至文字分析 API。 您稍後會在快速入門中執行此動作。
    * 您可以使用免費定價層來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="create-a-new-ruby-application"></a>建立新的 Ruby 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 然後建立名為 `GemFile` 的檔案，以及用於您程式碼的 Ruby 檔案。

```console
mkdir myapp && cd myapp
```

在您的 `GemFile` 中，新增下列幾行，以將用戶端程式庫新增為相依性。

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

在 Ruby 檔案中，匯入下列套件。

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

為資源的 Azure 端點和金鑰建立變數。 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>物件模型 

文字分析用戶端會使用您的金鑰向 Azure 進行驗證。 此用戶端提供數種用來分析文字 (以單一字串或批次的形式) 的方法。 

文字會以 `documents` 清單的形式傳送至 API，而此清單中列載 `id`、`text` 和 `language` 屬性的組合 (視使用的方法而定) 所屬的 `dictionary` 物件。 `text` 屬性會儲存要在原始 `language` 中分析的文字，而 `id` 可以是任何值。 

回應物件是一份清單，包含每個文件的分析資訊。 

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的文字分析用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [情感分析](#sentiment-analysis)
* [語言偵測](#language-detection)
* [實體辨識](#entity-recognition)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>驗證用戶端

建立名為 `TextAnalyticsClient`的類別。 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

在此類別中，建立名為 `initialize` 的函式，以使用您的金鑰和端點驗證用戶端。 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

在類別之外，請使用用戶端的 `new()` 函式將其具現化。

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>情感分析

在用戶端物件中，建立名為 `AnalyzeSentiment()` 的函式，此函式會取用稍後將建立的輸入文件清單。 呼叫用戶端的 `sentiment()` 函式，並取得結果。 接著逐一查看結果，並列印每個文件的識別碼和人氣分數。 接近 0 的分數表示負面人氣，而接近 1 的分數則表示正面人氣。

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

在用戶端函式外部，建立名為 `SentimentAnalysisExample()` 的新函式，以取用稍早建立的 `TextAnalyticsClient` 物件。 建立 `MultiLanguageInput` 物件清單，包含您要分析的文件。 每個物件會包含 `id`、`Language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文件語言，而 `id` 可以是任何值。 然後呼叫用戶端的 `AnalyzeSentiment()` 函式。

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

呼叫 `SentimentAnalysisExample()` 函式。

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>輸出

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>語言偵測

在用戶端物件中，建立名為 `DetectLanguage()` 的函式，此函式會取用稍後將建立的輸入文件清單。 呼叫用戶端的 `detect_language()` 函式，並取得結果。 接著逐一查看結果，並輸出每個文件的識別碼和偵測到的語言。

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

在用戶端函式外部，建立名為 `DetectLanguageExample()` 的新函式，以取用稍早建立的 `TextAnalyticsClient` 物件。 建立 `LanguageInput` 物件清單，包含您要分析的文件。 每個物件會包含 `id` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，而 `id` 可以是任何值。 然後呼叫用戶端的 `DetectLanguage()` 函式。

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

呼叫 `DetectLanguageExample()` 函式。

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>輸出

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>實體辨識

在用戶端物件中，建立名為 `RecognizeEntities()` 的函式，此函式會取用稍後將建立的輸入文件清單。 呼叫用戶端的 `entities()` 函式，並取得結果。 接著逐一查看結果，並輸出每個文件的識別碼及辨識的實體。

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

在用戶端函式外部，建立名為 `RecognizeEntitiesExample()` 的新函式，以取用稍早建立的 `TextAnalyticsClient` 物件。 建立 `MultiLanguageInput` 物件清單，包含您要分析的文件。 每個物件會包含 `id`、`language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文字語言，而 `id` 可以是任何值。 然後呼叫用戶端的 `RecognizeEntities()` 函式。

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

呼叫 `RecognizeEntitiesExample()` 函式。

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>輸出

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>關鍵片語擷取

在用戶端物件中，建立名為 `ExtractKeyPhrases()` 的函式，此函式會取用稍後將建立的輸入文件清單。 呼叫用戶端的 `key_phrases()` 函式，並取得結果。 然後逐一查看結果，並輸出每個文件的識別碼以及擷取的主要片語。

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

在用戶端函式外部，建立名為 `KeyPhraseExtractionExample()` 的新函式，以取用稍早建立的 `TextAnalyticsClient` 物件。 建立 `MultiLanguageInput` 物件清單，包含您要分析的文件。 每個物件會包含 `id`、`language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文字語言，而 `id` 可以是任何值。 然後呼叫用戶端的 `ExtractKeyPhrases()` 函式。

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


呼叫 `KeyPhraseExtractionExample()` 函式。

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

### <a name="output"></a>輸出

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```
