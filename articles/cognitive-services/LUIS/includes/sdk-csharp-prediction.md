---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/14/2020
ms.author: diberry
ms.openlocfilehash: fa0537b4009cf38b95cedff93850e5dac61baf34
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772413"
---
使用適用於 .NET 的 Language Understanding (LUIS) 預測用戶端程式庫可以：

* 依位置取得預測

[參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [預測執行階段套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C# 範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Prerequisites

* Language Understanding (LUIS) 入口網站帳戶 - [建立免費帳戶](https://www.luis.ai)
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

在尋找更多文件嗎？

 * [SDK 參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>設定

### <a name="create-an-environment-variable"></a>建立環境變數

使用您的金鑰和資源的名稱，建立兩個環境變數以進行驗證：

* `LUIS_PREDICTION_KEY` - 用於驗證您要求的資源金鑰。
* `LUIS_ENDPOINT_NAME` - 與您金鑰相關聯的資源名稱。

請使用適合您作業系統的指示。

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

新增環境變數之後，請重新啟動主控台視窗。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

新增環境變數之後，從主控台視窗執行 `source ~/.bashrc`，讓變更生效。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

編輯 `.bash_profile`，然後新增環境變數：

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

新增環境變數之後，從主控台視窗執行 `source .bash_profile`，讓變更生效。

---

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在您慣用的編輯器或 IDE 中，建立新的 .NET Core 應用程式。

1. 在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 dotnet `new` 命令建立名為 `language-understanding-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：`Program.cs`。

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. 將目錄變更為新建立的應用程式資料夾。

1. 您可以使用下列命令來建置應用程式：

    ```dotnetcli
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

### <a name="install-the-sdk"></a>安裝 SDK

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 Language Understanding (LUIS) 預測執行階段用戶端程式庫：

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。

## <a name="object-model"></a>物件模型

Language Understanding (LUIS) 預測執行階段用戶端是向 Azure 進行驗證的 [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet)物件，其中包含您的資源金鑰。

建立用戶端之後，請使用此用戶端來存取功能，包括：

* [依預備或產品位置](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)進行預測
* 依[版本](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)進行預測


## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 .NET 的 Language Understanding (LUIS) 預測執行階段用戶端程式庫來執行下列動作：

* [依位置進行預測](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>新增相依性

從專案目錄，在慣用的編輯器或 IDE 中開啟 *Program.cs* 檔案。 將現有的 `using` 程式碼取代為下列 `using` 指示詞：

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>驗證用戶端

1. 建立金鑰、名稱和應用程式識別碼的變數：

    此變數用來管理從名為 `LUIS_PREDICTION_KEY` 的環境變數中提取的撰寫金鑰。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。 方法會於稍後建立。

    建立用來保存資源名稱的變數 `LUIS_ENDPOINT_NAME`。

    建立應用程式識別碼的變數，作為名為 `LUIS_APP_ID` 的環境變數。 將環境變數設定為公用 IoT 應用程式：

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. 使用您的金鑰建立 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) 物件，並使用該物件與您的端點建立 [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) 物件。

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>從執行階段取得預測

新增下列方法，以建立對預測執行階段的要求。

使用者語句是 [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) 物件的一部分。

**GetSlotPredictionAsync** 方法需要數個參數，例如應用程式識別碼、位置名稱，以及用來完成要求的預測要求物件。 其他選項 (例如詳細資訊、顯示所有意圖和記錄) 是選擇性的。

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>預測的主要程式碼

使用下列主要方法將變數和方法繫結在一起，以取得預測。

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>執行應用程式

使用 `dotnet run` 命令，從您的應用程式目錄執行應用程式。

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>清除資源

在完成預測後，請刪除 program.cs 檔案及其子目錄，以清除本快速入門中的工作。
