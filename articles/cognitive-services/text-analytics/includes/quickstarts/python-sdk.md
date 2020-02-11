---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 30c65abcbf469dd4f6e8987aa1e2ee8a36fef17a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987873"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

[v3 參考文件](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [v3 程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3 套件 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

[v2 參考文件](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [v2 程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2 套件 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2 範例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後，您可以透過以下項目安裝用戶端程式庫：

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

---

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

建立新的 Python 檔案，並為資源的 Azure 端點和訂用帳戶金鑰建立變數。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>物件模型

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

文字分析用戶端是一個 `TextAnalyticsClient` 物件，會使用您的金鑰向 Azure 進行驗證。 此用戶端提供數種用來分析文字 (以批次的形式) 的方法。 本快速入門會使用一組函式來快速傳送單一文件。

批次處理開始進行時，文字會以 `documents` 清單的形式傳送至 API，而此清單中列載 `id`、`text` 和 `language` 屬性的組合 (視使用的方法而定) 所屬的 `dictionary` 物件。 `text` 屬性會儲存要在原始 `language` 中分析的文字，而 `id` 可以是任何值。 在處理單一文件時，只需要 `text` 輸入，如下列範例所示。  

回應物件是一份清單，包含每個文件的分析資訊。 

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

文字分析用戶端是一個 [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) 物件，會使用您的金鑰向 Azure 進行驗證。 此用戶端提供數種用來分析文字 (以單一字串或批次的形式) 的方法。 

文字會以 `documents` 清單的形式傳送至 API，而此清單中列載 `id`、`text` 和 `language` 屬性的組合 (視使用的方法而定) 所屬的 `dictionary` 物件。 `text` 屬性會儲存要在原始 `language` 中分析的文字，而 `id` 可以是任何值。 

---

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的文字分析用戶端程式庫來執行下列工作：

* [情感分析](#sentiment-analysis)
* [語言偵測](#language-detection)
* [具名實體辨識](#named-entity-recognition-ner) 
* [實體連結](#entity-linking)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>情感分析

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

建立名為 `sentiment_analysis_example()` 的新函式，此函式會採用端點和金鑰作為引數，然後呼叫 `single_analyze_sentiment()` 函式。 傳回的回應物件會包含整個輸入文件的情感標籤和分數，以及每個句子的情感分析。


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, key=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>輸出

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

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

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

建立名為 `language_detection_example()` 的新函式，此函式會採用端點和金鑰作為引數，然後呼叫 `single_detect_languages()` 函式。 如果成功，傳回的回應物件會在 `detected_languages` 中包含所偵測到的語言，如果未能成功，則會包含 `error`。

> [!Tip]
> 在某些情況下，可能難以根據輸入來區分語言。 您可以使用 `country_hint` 參數來指定 2 個字母的國碼。 根據預設，API 會使用 "US" 作為預設 countryHint，若要移除此行為，您可以將此值設定為空字串 `country_hint : ""`，以重設此參數。 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, key=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>輸出

```console
Language:  French
```

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

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

#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

> [!NOTE]
> 在 `3.0-preview` 版中：
> * NER 包含各種用於偵測個人資訊的方法。 
> * 實體連結是不同於 NER 的要求。

建立名為 `entity_recognition_example` 的新函式，此函式會採用端點和金鑰作為引數，然後呼叫 `single_recognize_entities()` 函式並逐一查看結果。 如果成功，傳回的回應物件會在 `entity` 中包含所偵測到實體的清單，若未能成功，則會包含 `error`。 針對每個偵測到的實體，列印其類型和子類型 (如果有的話)。

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>輸出

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="using-ner-to-detect-personal-information"></a>使用 NER 來偵測個人資訊

建立名為 `entity_pii_example()` 的新函式，此函式會採用端點和金鑰作為引數，然後呼叫 `single_recognize_pii_entities()` 函式並取得結果。 接著逐一查看結果，並印出實體。

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>輸出

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```


## <a name="entity-linking"></a>實體連結

建立名為 `entity_linking_example()` 的新函式，此函式會採用端點和金鑰作為引數，然後呼叫 `single_recognize_linked_entities()` 函式並逐一查看結果。 如果成功，傳回的回應物件會在 `entities` 中包含所偵測到實體的清單，若未能成功，則會包含 `error`。 因為連結的實體已識別為是唯一的，所以相同實體再次出現的話，就會群組到 `entity` 物件底下作為 `match` 物件的清單。

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, key=key, input_text= document)

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(endpoint, key)
```

### <a name="output"></a>輸出

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

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


#### <a name="version-30-previewtabversion-3"></a>[3.0-preview 版](#tab/version-3)

建立名為 `key_phrase_extraction_example()` 的新函式，此函式會採用端點和金鑰作為引數，然後呼叫 `single_extract_key_phrases()` 函式。 如果成功，結果將會在 `key_phrases` 中包含偵測到的關鍵片語清單，若不成功則為 `error`。 列印任何偵測到的關鍵片語。

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, key=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>輸出

```console
    Key Phrases:
         cat
         veterinarian
```

#### <a name="version-21tabversion-2"></a>[2.1 版](#tab/version-2)

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