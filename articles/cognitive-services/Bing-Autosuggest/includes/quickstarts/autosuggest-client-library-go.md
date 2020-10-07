---
title: Bing 自動建議 Go 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: c0129ff25f1df492ab6eba9f49add18d5321a3e8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88246182"
---
開始使用適用於 Go 的 Bing 自動建議用戶端程式庫。 請遵循下列步驟來安裝程式庫，並試用我們的基本工作範例。

使用適用於 Go 的 Bing 自動建議用戶端程式庫，以根據部分查詢字串來取得搜尋建議。

[參考文件](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [範例程式碼](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，[您可以建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)。
* 最新版的 [Go](https://golang.org/dl/)。

藉由建立 Azure 資源，開始使用 Bing 自動建議用戶端程式庫。 在下方選擇適合您的資源類型：

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>建立環境變數

>[!NOTE]
> 在 2019 年 7 月 1 日之後建立的資源端點使用下面顯示的自訂子網域格式。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。

在您建立的資源中，使用金鑰和端點來建立兩個環境變數以進行驗證：
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`:用來驗證要求的資源金鑰。
* `AUTOSUGGEST_ENDPOINT`:用來傳送 API 要求的資源端點。 應該會顯示如下：`https://<your-custom-subdomain>.api.cognitive.microsoft.com`

請使用適合您作業系統的指示。
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

新增環境變數之後，請重新啟動主控台視窗。

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

新增環境變數之後，從主控台視窗執行 `source ~/.bashrc`，讓變更生效。

### <a name="macos"></a>[macOS](#tab/unix)

編輯 `.bash_profile`，然後新增環境變數：

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

新增環境變數之後，從主控台視窗執行 `source .bash_profile`，讓變更生效。
***

## <a name="create-a-new-go-project"></a>建立新的 Go 專案

在主控台視窗 (cmd、PowerShell、Bash) 中，為您的 Go 專案建立新的工作區，並瀏覽至該工作區。 您的工作區將包含三個資料夾：

* **src**：此目錄包含原始程式碼和套件。 任何使用 `go get` 命令安裝的套件都將位於此處。
* **pkg**：此目錄包含已編譯的 Go 套件物件。 這些檔案都具有 `.a` 副檔名。
* **bin**：此目錄包含您執行 `go install` 時所建立的二進位可執行檔。

> [!TIP]
> 深入了解 [Go 工作區](https://golang.org/doc/code.html#Workspaces)的結構。 本指南包含用來設定 `$GOPATH` 和 `$GOROOT` 的資訊。

讓我們建立名為 `my-app` 的工作區，以及 `src`、`pkg` 和 `bin` 所需的子目錄：

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>安裝適用於 Go 的用戶端程式庫

現在，讓我們安裝適用於 Go 的用戶端程式庫：

```bash
$ go get -u <library-location-or-url>
```

或者，如果您使用 dep，請在存放庫中執行：

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>建立您的 Go 應用程式

接下來，讓我們建立名為 `src/sample-app.go` 的檔案：

```bash
$ cd src
$ touch sample-app.go
```

開啟 `sample-app.go`，新增套件名稱，然後匯入下列程式庫：

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

建立名為 `main` 的函式。 然後，為 Bing 自動建議金鑰和端點建立環境變數：

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>程式碼範例

這些程式碼範例會示範如何使用適用於 Go 的 Bing 自動建議用戶端程式庫來完成基本工作：

* [驗證用戶端](#authenticate-the-client)
* [傳送 API 要求](#send-an-api-request)

### <a name="authenticate-the-client"></a>驗證用戶端

> [!NOTE]
> 本快速入門假設您已為 Bing 自動建議金鑰[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) (名為 `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`)，並為端點建立名為 `BING_AUTOSUGGEST_ENDPOINT` 的環境變數。

在 `main()` 函式中，使用端點和金鑰來具現化用戶端。

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>傳送 API 要求

在相同的方法中，使用用戶端的 [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) 方法，將查詢傳送至 Bing。 然後，逐一查看[建議](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet)回應，並列印第一個建議。

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>執行應用程式

使用 `go run [arguments]` 命令，從您的應用程式目錄執行 Go 應用程式。

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [在 Azure 入口網站中刪除資源群組](../../../cognitive-services-apis-create-account.md#clean-up-resources)。
* [在 Azure CLI 中刪除資源群組](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Bing 自動建議教學課程l](../../tutorials/autosuggest.md)

## <a name="see-also"></a>另請參閱

- [什麼是 Bing 自動建議？](../../get-suggested-search-terms.md)
- [Bing 自動建議 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
