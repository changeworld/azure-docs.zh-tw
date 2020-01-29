---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 518e6d544547b808b278121bf6364dcd1590bd6f
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281128"
---
<a name="HOLTop"></a>

[參考文件](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [套件 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

> [!NOTE]
> * 本快速入門使用的是文字分析用戶端程式庫 `3.0-preview` 版，這個版本包含已改善的[情感分析](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)和[具名實體辨識 (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) 公開預覽。
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 針對生產案例，建議您使用批次非同步方法來取得效能和擴充性。 例如，從 `azure.ai.textanalytics.aio` 命名空間匯入用戶端並呼叫 `analyze_sentiment()`，而不是從 `azure.ai.textanalytics` 命名空間匯入並呼叫 `analyze_sentiment()`。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-text-analytics-azure-resource"></a>建立文字分析 Azure 資源

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後，您可以透過以下項目安裝用戶端程式庫：

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

建立新的 Python 檔案，並為資源的 Azure 端點和訂用帳戶金鑰建立變數。

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>物件模型

文字分析用戶端是一個 `TextAnalyticsClient` 物件，會使用您的金鑰向 Azure 進行驗證。 此用戶端提供數種用來分析文字 (以批次的形式) 的方法。 本快速入門會使用一組函式來快速傳送單一文件。

批次處理開始進行時，文字會以 `documents` 清單的形式傳送至 API，而此清單中列載 `id`、`text` 和 `language` 屬性的組合 (視使用的方法而定) 所屬的 `dictionary` 物件。 `text` 屬性會儲存要在原始 `language` 中分析的文字，而 `id` 可以是任何值。 在處理單一文件時，只需要 `text` 輸入，如下列範例所示。  

回應物件是一份清單，包含每個文件的分析資訊。 

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的文字分析用戶端程式庫來執行下列工作：

* [情感分析](#sentiment-analysis) (公開預覽)
* [語言偵測](#language-detection)
* [具名實體辨識](#named-entity-recognition-public-preview) (公開預覽)
* [具名實體辨識 - 個人資訊](#named-entity-recognition---personal-information-public-preview) (公開預覽)
* [實體連結](#entity-linking)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>情感分析

> [!NOTE]
> 下列程式碼適用於情感分析 v3，其為公開預覽狀態。

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

## <a name="language-detection"></a>語言偵測

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

## <a name="named-entity-recognition-public-preview"></a>具名實體辨識 (公開預覽)

> [!NOTE]
> 以下程式碼適用於具名實體辨識 v3，其為公開預覽狀態。

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

## <a name="named-entity-recognition---personal-information-public-preview"></a>具名實體辨識 - 個人資訊 (公開預覽)

> [!NOTE]
> 下列程式碼可使用具名實體辨識 v3 (其為公開預覽狀態) 來偵測個人資訊。

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

## <a name="key-phrase-extraction"></a>關鍵片語擷取

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