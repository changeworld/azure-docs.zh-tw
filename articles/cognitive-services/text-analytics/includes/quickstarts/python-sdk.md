---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 988de8da839a677b47d679e7bd44059c7477a517
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986709"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

[v3 參考文件](https://aka.ms/azsdk-python-textanalytics-ref-docs) | [v3 程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3 套件 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

[v2 參考文件](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [v2 程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2 套件 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2 範例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="建立文字分析資源"  target="_blank"><span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]  。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至文字分析 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後，您可以透過以下項目安裝用戶端程式庫：

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

```console
pip install azure-ai-textanalytics==1.0.0b3
```

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py) 上找到該檔案，其中包含本快速入門中的程式碼範例。 

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py) 上找到該檔案，其中包含本快速入門中的程式碼範例。 

---

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

建立新的 Python 檔案，並為資源的 Azure 端點和訂用帳戶金鑰建立變數。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>物件模型

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

文字分析用戶端是一個 `TextAnalyticsClient` 物件，會使用您的金鑰向 Azure 進行驗證。 此用戶端提供數種用來分析文字 (以批次的形式) 的方法。 

批次處理開始進行時，文字會以 `documents` 清單的形式傳送至 API，而此清單中列載 `id`、`text` 和 `language` 屬性的組合 (視使用的方法而定) 所屬的 `dictionary` 物件。 `text` 屬性會儲存要在原始 `language` 中分析的文字，而 `id` 可以是任何值。 

回應物件是一份清單，包含每個文件的分析資訊。 

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

文字分析用戶端是一個 [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) 物件，會使用您的金鑰向 Azure 進行驗證。 此用戶端提供數種用來分析文字 (以單一字串或批次的形式) 的方法。 

文字會以 `documents` 清單的形式傳送至 API，而此清單中列載 `id`、`text` 和 `language` 屬性的組合 (視使用的方法而定) 所屬的 `dictionary` 物件。 `text` 屬性會儲存要在原始 `language` 中分析的文字，而 `id` 可以是任何值。 

---

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的文字分析用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [情感分析](#sentiment-analysis)
* [語言偵測](#language-detection)
* [具名實體辨識](#named-entity-recognition-ner) 
* [實體連結](#entity-linking)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>驗證用戶端

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

使用上方建立的 `key` 和 `endpoint` 來建立可具現化 `TextAnalyticsClient` 物件的函式。 然後，建立新的用戶端。 

```python
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiKeyCredential

def authenticate_client():
    ta_credential = TextAnalyticsApiKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

使用上方建立的 `key` 和 `endpoint` 來建立可具現化 `TextAnalyticsClient` 物件的函式。 然後，建立新的用戶端。 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>情感分析

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

建立名為 `sentiment_analysis_example()` 的新函式，此函式會將用戶端當做引數，然後呼叫 `analyze_sentiment()` 函式。 傳回的回應物件會包含整個輸入文件的情感標籤和分數，以及每個句子的情感分析。


```python
def sentiment_analysis_example(client):

    document = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(inputs=document)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Length: {}]".format(sentence.grapheme_length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))

            
sentiment_analysis_example(client)
```

### <a name="output"></a>輸出

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00

[Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

[Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

驗證用戶端物件，並呼叫 [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 函數。 逐一查看結果，並列印每個文件的識別碼和人氣分數。 接近 0 的分數表示負面人氣，而接近 1 的分數則表示正面人氣。

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>輸出

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>語言偵測

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

建立名為 `language_detection_example()` 的新函式，此函式會將用戶端當做引數，然後呼叫 `detect_language()` 函式。 如果成功，傳回的回應物件會在 `primary_language` 中包含所偵測到的語言，如果未能成功，則會包含 `error`。

> [!Tip]
> 在某些情況下，可能難以根據輸入來區分語言。 您可以使用 `country_hint` 參數來指定 2 個字母的國碼。 根據預設，API 會使用 "US" 作為預設 countryHint，若要移除此行為，您可以將此值設定為空字串 `country_hint : ""`，以重設此參數。 

```python
def language_detection_example(client):
    try:
        document = ["Ce document est rédigé en Français."]
        response = client.detect_language(inputs = document, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>輸出

```console
Language:  French
```

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

使用稍早建立的用戶端，呼叫 [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 函式並取得結果。 接著逐一查看結果，並列印每個文件的識別碼以及第一個傳回的語言。

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>輸出

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)

#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

> [!NOTE]
> 在 `3.0-preview` 版中：
> * NER 包含各種用於偵測個人資訊的方法。 
> * 實體連結是不同於 NER 的要求。

建立名為 `entity_recognition_example` 的新函式，此函式會將用戶端作為引數，然後呼叫 `recognize_entities()` 函式並逐一查看結果。 如果成功，傳回的回應物件會在 `entity` 中包含所偵測到實體的清單，若未能成功，則會包含 `error`。 針對每個偵測到的實體，列印其類別目錄和子類別 (如果有的話)。

```python
def entity_recognition_example(client):

    try:
        document = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(inputs= document)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>輸出

```console
Named Entities:

    Text:    Seattle        Category:        Location       SubCategory:     GPE
    Length:          7      Confidence Score:        0.92

    Text:    last week      Category:        DateTime       SubCategory:     DateRange
    Length:          9      Confidence Score:        0.8
```

## <a name="using-ner-to-detect-personal-information"></a>使用 NER 來偵測個人資訊

建立名為 `entity_pii_example()` 的新函式，此函式會將用戶端作為引數，然後呼叫 `recognize_pii_entities()` 函式並取得結果。 接著逐一查看結果，並印出實體。

```python
def entity_pii_example(client):

        document = ["Insurance policy for SSN on file 123-12-1234 is here by approved."]


        result = client.recognize_pii_entities(inputs= document)[0]
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tCategory: ", entity.category,"\tSubCategory: ", entity.subcategory)
            print("\t\tLength: ", entity.grapheme_length, "\tScore: {0:.2f}".format(entity.score), "\n")
        
entity_pii_example(client)
```

### <a name="output"></a>輸出

```console
Personally Identifiable Information Entities:
    Text:  123-12-1234      Category:  U.S. Social Security Number (SSN)    SubCategory:  None
        Length:  11     Score: 0.85
```


## <a name="entity-linking"></a>實體連結

建立名為 `entity_linking_example()` 的新函式，此函式會將用戶端作為引數，然後呼叫 `recognize_linked_entities()` 函式並逐一查看結果。 如果成功，傳回的回應物件會在 `entities` 中包含所偵測到實體的清單，若未能成功，則會包含 `error`。 因為連結的實體已識別為是唯一的，所以相同實體再次出現的話，就會群組到 `entity` 物件底下作為 `match` 物件的清單。

```python
def entity_linking_example(client):

    try:
        document = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(inputs= document)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.2f}".format(match.score), "\tLength: {}\n".format(match.grapheme_length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>輸出

```console
Linked Entities:

    Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.78     Length: 11

    Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.55     Length: 10

        Text: Gates
        Score: 0.55     Length: 5

    Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.53     Length: 10

    Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.47     Length: 9

        Text: Microsoft
        Score: 0.47     Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.25     Length: 7

    Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.28     Length: 5

```

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

> [!NOTE]
> 在 2.1 版中，實體連結會包含在 NER 回應中。

使用稍早建立的用戶端，呼叫 [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 函式並取得結果。 接著逐一查看結果，並輸出每個文件的識別碼以及其中包含的實體。

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>輸出

```console
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

---

## <a name="key-phrase-extraction"></a>關鍵片語擷取


#### <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

建立名為 `key_phrase_extraction_example()` 的新函式，此函式會將用戶端當做引數，然後呼叫 `extract_key_phrases()` 函式。 如果成功，結果將會在 `key_phrases` 中包含偵測到的關鍵片語清單，若不成功則為 `error`。 列印任何偵測到的關鍵片語。

```python
def key_phrase_extraction_example(client):

    try:
        document = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(inputs= document)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>輸出

```console
    Key Phrases:
         cat
         veterinarian
```

#### <a name="version-21"></a>[2.1 版](#tab/version-2)

使用稍早建立的用戶端，呼叫 [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 函式並取得結果。 接著逐一查看結果，並輸出每個文件的識別碼以及其中包含的關鍵片語。

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


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
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---