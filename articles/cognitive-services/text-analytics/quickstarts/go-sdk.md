---
title: 快速入門：適用於 Go 的文字分析用戶端程式庫 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您可以使用 Azure 認知服務中的 Go 文字分析用戶端程式庫來偵測語言。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: 6849046211ae4216f181dd8ef0ca391c2876363c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87291776"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>快速入門：使用適用於 Go 的文字分析用戶端程式庫

- [參考文件](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics)
- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-go/blob/090dc0ee4d8d2d60e2a9525774d967a4111a2b0c/services/cognitiveservices/v2.1/textanalytics/client.go)
- [套件 (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)
- [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> 本快速入門僅適用於文字分析 2.1 版。 目前無法使用適用於 Go 的 v3 用戶端程式庫。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 最新版的 [Go](https://golang.org/dl/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="建立文字分析資源"  target="_blank">建立文字分析資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至文字分析 API。 您稍後會在快速入門中執行此動作。
    * 您可以使用免費定價層來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="create-a-new-go-project"></a>建立新的 Go 專案

在主控台視窗 (cmd、PowerShell、Bash) 中，為您的 Go 專案建立新的工作區，並瀏覽至該工作區。 您的工作區將包含三個資料夾： 

* **src** - 此目錄包含原始程式碼和套件。 任何使用 `go get` 命令安裝的套件都將位於此處。
* **pkg** - 此目錄包含已編譯的 Go 套件物件。 這些檔案都具有 `.a` 副檔名。
* **bin** - 此目錄包含您執行 `go install` 時所建立的二進位可執行檔。

> [!TIP]
> 深入了解 [Go 工作區](https://golang.org/doc/code.html#Workspaces)的結構。 本指南包含用來設定 `$GOPATH` 和 `$GOROOT` 的資訊。

建立名為 `my-app` 的工作區，以及 `src`、`pkg` 和 `bin` 所需的子目錄：

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>安裝適用於 Go 的文字分析用戶端程式庫

安裝適用於 Go 的用戶端程式庫： 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

或者，如果您使用 dep，請在存放庫中執行：

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>建立您的 Go 應用程式

接著，建立名為 `src/quickstart.go` 的檔案：

```bash
$ cd src
$ touch quickstart.go
```

在您慣用的 IDE 或文字編輯器中開啟 `quickstart.go`。 然後，新增套件名稱，並匯入下列程式庫：

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>物件模型 

文字分析用戶端是一個 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) 物件，會使用您的金鑰向 Azure 進行驗證。 此用戶端提供數種用來分析文字 (以單一字串或批次的形式) 的方法。 

文字會以 `documents` 清單的形式傳送至 API，而此清單中列載 `id`、`text` 和 `language` 屬性的組合 (視使用的方法而定) 所屬的 `dictionary` 物件。 `text` 屬性會儲存要在原始 `language` 中分析的文字，而 `id` 可以是任何值。 

回應物件是一份清單，包含每個文件的分析資訊。 

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Go 的文字分析用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [情感分析](#sentiment-analysis)
* [語言偵測](#language-detection)
* [實體辨識](#entity-recognition)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>驗證用戶端


在新的函式中，為資源的 Azure 端點和訂用帳戶金鑰建立變數。

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

建立新的 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) \(英文\) 物件。 將您的金鑰傳至 [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) 函式，繼而傳至用戶端的 `authorizer` 屬性。

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>情感分析

建立稱為 `SentimentAnalysis()` 的新函式，並使用稍早建立的 `GetTextAnalyticsClient()` 方法來建立用戶端。 建立 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 物件的清單，其中包含您要分析的文件。 每個物件會包含 `id`、`Language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文件語言，而 `id` 可以是任何值。 

呼叫用戶端的 [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) \(英文\) 函式，並取得結果。 接著逐一查看結果，並列印每個文件的識別碼和人氣分數。 接近 0 的分數表示負面人氣，而接近 1 的分數則表示正面人氣。

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

在您的專案中呼叫 `SentimentAnalysis()`。

### <a name="output"></a>輸出

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>語言偵測

建立稱為 `LanguageDetection()` 的新函式，並使用稍早建立的 `GetTextAnalyticsClient()` 方法來建立用戶端。 建立 [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) 物件的清單，其中包含您要分析的文件。 每個物件會包含 `id` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，而 `id` 可以是任何值。 

呼叫用戶端的 [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) \(英文\)，並取得結果。 接著逐一查看結果，並輸出每個文件的識別碼和偵測到的語言。

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

在您的專案中呼叫 `LanguageDetection()`。

### <a name="output"></a>輸出

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>實體辨識

建立稱為 `ExtractEntities()` 的新函式，並使用稍早建立的 `GetTextAnalyticsClient()` 方法來建立用戶端。 建立 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 物件的清單，其中包含您要分析的文件。 每個物件會包含 `id`、`language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文件語言，而 `id` 可以是任何值。 

呼叫用戶端的 [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) \(英文\)，並取得結果。 接著逐一查看結果，並輸出每個文件的識別碼以及擷取的實體分數。

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

在您的專案中呼叫 `ExtractEntities()`。

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

## <a name="key-phrase-extraction"></a>關鍵片語擷取

建立稱為 `ExtractKeyPhrases()` 的新函式，並使用稍早建立的 `GetTextAnalyticsClient()` 方法來建立用戶端。 建立 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 物件的清單，其中包含您要分析的文件。 每個物件會包含 `id`、`language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文件語言，而 `id` 可以是任何值。

呼叫用戶端的 [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) \(英文\)，並取得結果。 然後逐一查看結果，並輸出每個文件的識別碼以及擷取的主要片語。

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

在您的專案中呼叫 `ExtractKeyPhrases()`。

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
