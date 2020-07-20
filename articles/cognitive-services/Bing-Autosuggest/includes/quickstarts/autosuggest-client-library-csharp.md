---
title: Bing 自動建議 C# 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 5d325ac8da43f020f4ab39244d2e6a7dc6ed72b7
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156573"
---
開始使用適用於 .NET 的 Bing 自動建議用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

使用適用於 .NET 的 Bing 自動建議用戶端程式庫，以根據部分查詢字串來取得搜尋建議。

[參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | [範例程式碼](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，[您可以建立免費帳戶](https://azure.microsoft.com/free/)。
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>建立環境變數

>[!NOTE]
> 在 2019 年 7 月 1 日之後建立的資源端點使用下面顯示的自訂子網域格式。 如需詳細資訊和完整的區域端點清單，請參閱[認知服務的自訂子網域名稱](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。 

在您建立的資源中，使用金鑰和端點來建立兩個環境變數以進行驗證：
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`:用來驗證要求的資源金鑰。
* `AUTOSUGGEST_ENDPOINT`:用來傳送 API 要求的資源端點。 它看起來應該像這樣：`https://<your-custom-subdomain>.api.cognitive.microsoft.com`。 

請使用適合您作業系統的指示。
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
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

## <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在您慣用的編輯器或 IDE 中，建立新的 .NET Core 應用程式。 

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `bing-autosuggest-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：*program.cs*。 

```console
dotnet new console -n bing-autosuggest-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```console
dotnet build
```

建置輸出應該不會有警告或錯誤。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

從專案目錄，在慣用的編輯器或 IDE 中開啟 *program.cs* 檔案。 新增下列 `using` 指示詞：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

在 `Program` 類別中，為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

在應用程式的 `Main` 方法中，新增下列將會於稍後定義的方法呼叫。

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

## <a name="install-the-client-library"></a>安裝用戶端程式庫

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 Bing 自動建議用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 .NET 的 Bing 自動建議用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [傳送自動建議要求](#send-an-autosuggest-request)

### <a name="authenticate-the-client"></a>驗證用戶端

> [!NOTE]
> 本快速入門假設您已為 Bing 自動建議金鑰[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) (名為 `AUTOSUGGEST_SUBSCRIPTION_KEY`)，並為端點建立名為 `AUTOSUGGEST_ENDPOINT` 的環境變數。


在新的非同步方法中，使用端點和金鑰來具現化用戶端。 使用您的金鑰建立 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet) 物件，並使用該物件與您的端點建立 [AutosuggestClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet) 物件。

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

### <a name="send-an-autosuggest-request"></a>傳送自動建議要求

在相同的方法中，使用用戶端的 [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) 方法，將查詢傳送至 Bing。 然後，逐一查看[建議](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet)回應，並列印第一個建議。

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>執行應用程式

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除與其相關聯的任何其他資源：

* [在 Azure 入口網站中刪除資源群組](../../../cognitive-services-apis-create-account.md#clean-up-resources)。
* [在 Azure CLI 中刪除資源群組](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Bing 自動建議教學課程l](../../tutorials/autosuggest.md)

## <a name="see-also"></a>另請參閱

- [什麼是 Bing 自動建議？](../../get-suggested-search-terms.md)
- [Bing 自動建議 dotnet 參考](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet)
