---
title: 快速入門：適用於 .NET 的表單辨識器用戶端程式庫
description: 透過本快速入門，開始使用適用於 .NET 的表單辨識器用戶端程式庫來定型、擷取、分析和取得結構化資料輸出。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: b9db9daf06b59e1a26a9b03a93aff63984841862
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77118358"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>快速入門：適用於 .NET 的表單辨識器用戶端程式庫

開始使用適用於 .NET 的表單辨識器用戶端程式庫。 表單辨識器是一項認知服務，其使用機器學習技術來識別和擷取表單文件中的索引鍵/值組和資料表資料。 然後，它會輸出在原始檔中包含關聯性的結構化資料。 請遵循下列步驟來安裝 SDK 套件，並試用基本工作的程式碼範例。

使用適用於 .NET 的表單辨識器用戶端程式庫執行下列動作：

* [定型自訂表單辨識器模型](#train-a-custom-model)
* [取得已擷取的索引鍵清單](#get-a-list-of-extracted-keys)
* [使用自訂模型分析表單](#analyze-forms-with-a-custom-model)
* [取得自訂模型的清單](#get-a-list-of-custom-models)
* [刪除自訂模型](#delete-a-custom-model)

[參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/FormRecognizer) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)。
* 包含一組訓練資料的 Azure 儲存體 Blob。 請參閱[為自訂模型建置訓練資料集](../build-training-data-set.md)，以獲得如何將訓練資料放在一起的祕訣和選項。 在本快速入門中，您可以使用[範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)中 **Train** 資料夾底下的檔案。
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="create-a-form-recognizer-azure-resource"></a>建立表單辨識器 Azure 資源

[!INCLUDE [create resource](../includes/create-resource.md)]

從試用版訂用帳戶或資源取得金鑰後，請為名稱分別是 `FORM_RECOGNIZER_KEY` 和 `FORM_RECOGNIZER_ENDPOINT` 的金鑰及端點[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `formrecognizer-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：_Program.cs_。 

```console
dotnet new console -n formrecognizer-quickstart
```

將目錄變更為新建立的應用程式資料夾。 接著，使用下列命令來建置應用程式：

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

從專案目錄，在慣用的編輯器或 IDE 中開啟 _Program.cs_ 檔案。 新增下列 `using` 陳述式：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

然後，在應用程式的 **Main** 方法中新增下列程式碼。 您稍後會定義此非同步工作。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>安裝用戶端程式庫

在應用程式目錄中，使用下列命令安裝適用於 .NET 的表單辨識器用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。

## <a name="object-model"></a>物件模型

下列類別會處理表單辨識器 SDK 的主要功能。

|名稱|描述|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|所有表單辨識器功能都需要此類別。 您可以使用訂用帳戶資訊來具現化此類別，並用其來產生其他類別的執行個體。|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| 您可以透過此類別使用您自己的訓練輸入資料來定型自訂表單辨識器模型。 |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| 此類別會傳遞自訂模型定型作業的結果 (包括模型識別碼)，以便您用來分析表單。 |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| 此類別會傳遞自訂模型分析作業的結果。 其包含 **ExtractedPage** 執行個體的清單。 |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| 此類別代表從單一表單文件擷取的所有資料。|

## <a name="code-examples"></a>程式碼範例

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

這些程式碼片段會示範如何使用適用於 .NET 的表單辨識器用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [定型自訂表單辨識器模型](#train-a-custom-model)
* [取得已擷取的索引鍵清單](#get-a-list-of-extracted-keys)
* [使用自訂模型分析表單](#analyze-forms-with-a-custom-model)
* [取得自訂模型的清單](#get-a-list-of-custom-models)
* [刪除自訂模型](#delete-a-custom-model)

## <a name="define-variables"></a>定義變數

定義任何方法之前，請先將下列變數定義新增至 **Program** 類別的頂端。 您必須自行填入一些變數。 

* 若要擷取您訓練資料的 SAS URL，請開啟 Microsoft Azure 儲存體總管、以滑鼠右鍵按一下您的容器，然後選取 [取得共用存取簽章]  。 確定 [讀取]  和 [列出]  權限均已勾選，再按一下 [建立]  。 然後，複製 [URL]  區段的值。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
* 如果您需要分析範例表單，您可以使用[範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)中 **Test** 資料夾底下的其中一個檔案。 本指南僅使用 PDF 表單。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>驗證用戶端

在 `Main` 方法下方，定義 `Main` 中所參考的工作。 在這裡，您將使用上方所定義的訂用帳戶變數來驗證用戶端物件。 您稍後將會定義其他方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>定型自訂模型

下列方法會使用您的表單辨識器用戶端物件，對儲存在 Azure Blob 容器的文件定型新辨識模型。 其中會使用 Helper 方法來顯示最新定型模型 (以 [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) 物件表示) 的相關資訊，並傳回模型識別碼。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

下列 Helper 方法會顯示表單辨識器模型的相關資訊。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>取得已擷取的索引鍵清單

定型完成後，自訂模型會保留已從定型文件中擷取的索引鍵清單。 其預期會有未來的表單文件來包含這些索引鍵，而且會擷取其在分析作業中對應的值。 使用下列方法來取得已擷取的索引鍵清單，並將其列印到主控台。 這是驗證定型程序是否有效的好方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>使用自訂模型分析表單

此方法會使用表單辨識器用戶端和模型識別碼來分析 PDF 表單文件，並擷取索引鍵/值資料。 其中會使用 Helper 方法來顯示結果 (以 [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) 物件表示)。

> [!NOTE]
> 下列方法會分析 PDF 表單。 如需分析 JPEG 和 PNG 表單的類似方法，請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer) 上的完整範例程式碼。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

下列 Helper 方法會顯示分析作業的相關資訊。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>取得自訂模型的清單

您可以傳回帳戶所屬的所有已定型模型清單，而且可以擷取模型建立時的相關資訊。 模型清單會由 [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) 物件表示。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>刪除自訂模型

如果您想要從帳戶中刪除自訂模型，請使用下列方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>執行應用程式

從應用程式目錄呼叫 `dotnet run` 命令來執行應用程式。

```console
dotnet run
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

此外，如果您已訓練想要從帳戶中刪除的自訂模型，請在[刪除自訂模型](#delete-a-custom-model)中執行方法。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用表單辨識器的 .NET 用戶端程式庫來定型自訂模型和分析表單。 接下來，您將了解建立更好的訓練資料集有何秘訣，然後產生更精確的模型。

> [!div class="nextstepaction"]
>[建置訓練資料集](../build-training-data-set.md)

* [什麼是表單辨識器？](../overview.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer) 上找到。
