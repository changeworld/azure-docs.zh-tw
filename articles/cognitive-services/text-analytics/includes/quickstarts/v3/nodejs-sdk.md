---
title: 快速入門：適用於 Node.js 的文字分析 v3 用戶端程式庫 | Microsoft Docs
description: 開始使用適用於 Node.js 的 v3 文字分析用戶端程式庫。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/13/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 69899f521e73cb5af1af145a0915dbe1a017f307
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281130"
---
<a name="HOLTop"></a>

[參考文件](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [套件 (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)

> [!NOTE]
> * 本快速入門使用的是文字分析用戶端程式庫 `3.0-preview` 版，這個版本包含已改善的[情感分析](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)和[具名實體辨識 (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) 公開預覽。
> * 為求簡化，本文中的程式碼使用未受保護的認證儲存體。 針對生產案例，建議您以批次方式傳送字串，以取得效能和延展性。 例如，呼叫 `SentimentBatchAsync()`，而不是 `Sentiment()`。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版的 [Node.js](https://nodejs.org/)。

## <a name="setting-up"></a>設定

### <a name="create-a-text-analytics-azure-resource"></a>建立文字分析 Azure 資源

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

執行命令 `npm init`，以使用 `package.json` 檔案建立節點應用程式。 

```console
npm init
```
### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 `@azure/cognitiveservices-textanalytics` NPM 套件：

```console
npm install --save @azure/cognitiveservices-textanalytics
```

您應用程式的 `package.json` 檔案會隨著相依性而更新。

建立名為 `index.js` 的檔案，並新增下列程式庫：

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

為資源的 Azure 端點和金鑰建立變數。

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>物件模型

文字分析用戶端是一個 `TextAnalyticsClient` 物件，會使用您的金鑰向 Azure 進行驗證。 此用戶端提供數種用來分析文字 (以單一字串或批次的形式) 的方法。

文字會以 `documents` 清單的形式傳送至 API，而此清單中列載 `id`、`text` 和 `language` 屬性的組合 (視使用的方法而定) 所屬的 `dictionary` 物件。 `text` 屬性會儲存要在原始 `language` 中分析的文字，而 `id` 可以是任何值。 

回應物件是一份清單，包含每個文件的分析資訊。 

## <a name="code-examples"></a>程式碼範例

* [用戶端驗證](#client-authentication)
* [情感分析](#sentiment-analysis) (公開預覽)
* [語言偵測](#language-detection)
* [具名實體辨識](#named-entity-recognition-public-preview) (公開預覽)
* [具名實體辨識 - 個人資訊](#named-entity-recognition---personal-information-public-preview) (公開預覽)
* [實體連結](#entity-linking)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="client-authentication"></a>用戶端驗證

使用金鑰和端點作為參數來建立新的 `TextAnalyticsClient` 物件。

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(key));
```

## <a name="sentiment-analysis-public-preview"></a>情感分析 (公開預覽)

> [!NOTE]
> 下列程式碼適用於情感分析 v3，其為公開預覽狀態。

建立所要分析文件所在字串的陣列。 呼叫用戶端的 `analyzeSentiment()` 方法，並取得傳回的 `SentimentBatchResult` 物件。 逐一查看結果清單，並印出每份文件的識別碼、文件層級情感與信賴分數。 針對每份文件，結果中會包含句子層級的情感，以及位移、長度和信賴分數。

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ]

    const sentimentResult = await client.analyzeSentiment(sentimentInput);
    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.documentScores.positive.toFixed(2)} \tNegative: ${document.documentScores.negative.toFixed(2)} \tNeutral: ${document.documentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentenceScores.positive.toFixed(2)} \tNegative: ${sentence.sentenceScores.negative.toFixed(2)} \tNeutral: ${sentence.sentenceScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

在主控台視窗中透過 `node index.js` 執行您的程式碼。

### <a name="output"></a>輸出

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.61  Negative: 0.01  Neutral: 0.39
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

## <a name="language-detection"></a>語言偵測

建立所要分析文件所在字串的陣列。 呼叫用戶端的 `detectLanguages()` 方法，並取得傳回的 `DetectLanguageResult`。 接著逐一查看結果，並印出每個文件的識別碼以及各自的主要語言和偵測到的語言。

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

在主控台視窗中透過 `node index.js` 執行您的程式碼。

### <a name="output"></a>輸出

```console
ID: 0
        Detected Language French
        Primary Language French
```

## <a name="named-entity-recognition"></a>具名實體辨識

> [!NOTE]
> 以下程式碼適用於具名實體辨識 v3，其為公開預覽狀態。

建立所要分析文件所在字串的陣列。 呼叫用戶端的 `recognizeEntities()` 方法，並取得 `RecognizeEntitiesResult` 物件。 逐一查看結果清單，並印出實體名稱、類型、子類型、位移、長度和分數。

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];

    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

在主控台視窗中透過 `node index.js` 執行您的程式碼。

### <a name="output"></a>輸出

```console
Document ID: 0
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>具名實體辨識 - 個人資訊 (公開預覽)

> [!NOTE]
> 下列程式碼可使用具名實體辨識 v3 (其為公開預覽狀態) 來偵測個人資訊。

建立所要分析文件所在字串的陣列。 呼叫用戶端的 `recognizePiiEntities()` 方法，並取得 `EntitiesBatchResult` 物件。 逐一查看結果清單，並印出實體名稱、類型、子類型、位移、長度和分數。


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ]
    const entityResults = await client.recognizePiiEntities(entityPiiInput);

    result.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

在主控台視窗中透過 `node index.js` 執行您的程式碼。

### <a name="output"></a>輸出

```console
Document ID: 0
        Name: 123-12-1234       Type: U.S. Social Security Number (SSN)         Sub Type: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>實體連結

建立所要分析文件所在字串的陣列。 呼叫用戶端的 `recognizeLinkedEntities()` 方法，並取得 `RecognizeLinkedEntitiesResult` 物件。 逐一查看結果清單，並印出實體名稱、識別碼、資料來源、URL 和相符項目。 `matches` 陣列中的每個物件都會包含該相符項目的位移、長度和分數。

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ]
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

在主控台視窗中透過 `node index.js` 執行您的程式碼。

### <a name="output"></a>輸出

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.650
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.243
                Text: Gates
                Offset: 161, Length: 5  Score: 0.243
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.174
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.196
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.196
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.137
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.052
```

## <a name="key-phrase-extraction"></a>關鍵片語擷取

建立所要分析文件所在字串的陣列。 呼叫用戶端的 `extractKeyPhrases()` 方法，並取得傳回的 `ExtractKeyPhrasesResult` 物件。 逐一查看結果，並列印每份文件的識別碼，以及任何偵測到的關鍵片語。

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

在主控台視窗中透過 `node index.js` 執行您的程式碼。

### <a name="output"></a>輸出

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `node` 命令執行應用程式。

```console
node index.js
```
