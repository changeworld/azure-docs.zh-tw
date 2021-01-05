---
title: 快速入門：適用於 .NET 的 QnA Maker 用戶端程式庫
description: 本快速入門示範如何開始使用適用於 .NET 的 QnA Maker 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。  QnA Maker 可讓您加強常見問題集文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 05349d8339b9f2b8472e1b5384f213c92e158e5d
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812784"
---
使用適用於 .NET 的 QnA Maker 用戶端程式庫來：

 * 建立知識庫
 * 更新知識庫
 * 發佈知識庫
 * 取得預測執行階段端點金鑰
 * 等候長時間執行的工作
 * 下載知識庫
 * 取得答案
 * 刪除知識庫

[參考文件](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# 範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或目前版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 擁有 Azure 訂用帳戶之後，請在 Azure 入口網站中建立 [QnA Maker 資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以取得您的撰寫金鑰和資源名稱。 在其部署後，選取 [前往資源]。
    * 您需要來自所建立資源的金鑰和資源名稱，以將應用程式連線至 QnA Maker API。 您稍後會在快速入門中將金鑰和資源名稱貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

使用 Visual Studio 建立 .NET Core 應用程式，並以滑鼠右鍵按一下 [方案總管] 中的解決方案，然後選取 [管理 NuGet 套件]，以安裝用戶端程式庫。 在開啟的套件管理員中，選取 [瀏覽]、核取 [包含發行前版本]，然後搜尋 `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`。 選取版本 `2.0.0-preview.1`，然後 **安裝**。

#### <a name="cli"></a>[CLI](#tab/cli)

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `qna-maker-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：*program.cs*。

```console
dotnet new console -n qna-maker-quickstart
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

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 QnA Maker 用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.0-preview.1
```


---

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs) 上找到該檔案，其中包含本快速入門中的程式碼範例。

從專案目錄中中開啟 program.cs 檔案，並新增下列 `using` 指示詞：

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

在應用程式的 `Main` 方法中新增變數和程式碼 (如下列各節所示)，以使用本快速入門中的一般工作。

> [!IMPORTANT]
> 移至 Azure 入口網站，並尋找在必要條件中建立用於 QnA Maker 資源的金鑰和端點。 您可以透過資源的 [金鑰和端點] 頁面，在 [資源管理] 下找到這些項目。
> 您必須要有完整的金鑰以建立您的知識庫。 您只需要來自端點的資源名稱。 格式為 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 例如，[Azure Key Vault](../../../key-vault/general/overview.md) 可提供安全的金鑰儲存。

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


## <a name="object-models"></a>物件模型

QnA Maker 使用兩種不同的物件模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 是用來建立、管理、發佈和下載知識庫的物件。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 是可讓您透過 GenerateAnswer API 查詢知識庫，並使用定型 API 傳送新的建議問題 (作為 [主動式學習](../concepts/active-learning-suggestions.md)的一部分) 的物件。

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 物件模型

製作 QnA Maker 用戶端是一種 [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) 物件，會使用含有金鑰的 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證。

建立用戶端之後，請使用[知識庫](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase)屬性來建立、管理和發佈知識庫。

藉由傳送 JSON 物件來管理知識庫。 針對立即性作業，方法通常會傳回可指出狀態的 JSON 物件。 針對長期作業，回應則是作業識別碼。 使用作業識別碼來呼叫 [client.Operations.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) 方法，以確定[要求的狀態](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 物件模型

預測 QnA Maker 用戶端是一種 [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) 物件，會使用 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證，其中包含您的預測執行階段金鑰，這是在發佈知識庫後由製作用戶端呼叫 `client.EndpointKeys.GetKeys` 傳回的金鑰。

使用 [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) 方法從查詢執行階段取得答案。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 .NET 的 QnA Maker 用戶端程式庫來執行下列動作：

* [驗證製作用戶端](#authenticate-the-client-for-authoring-the-knowledge-base)
* [建立知識庫](#create-a-knowledge-base)
* [更新知識庫](#update-a-knowledge-base)
* [下載知識庫](#download-a-knowledge-base)
* [發佈知識庫](#publish-a-knowledge-base)
* [刪除知識庫](#delete-a-knowledge-base)
* [取得查詢執行階段金鑰](#get-query-runtime-key)
* [取得作業的狀態](#get-status-of-an-operation)
* [驗證查詢執行階段用戶端](#authenticate-the-runtime-for-generating-an-answer)
* [從知識庫產生答案](#generate-an-answer-from-the-knowledge-base)



## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>驗證用戶端以撰寫知識庫

使用您的金鑰將用戶端物件具現化，並將其與您的資源搭配使用以建構端點，繼而使用您的端點和金鑰建立 [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet)。 建立 [ServiceClientCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials?view=azure-dotnet) 物件。

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>建立知識庫

知識庫會針對來自三個來源的 [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) 物件儲存問題和答案組：

* 針對 **編輯內容**，請使用 [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) 物件。
    * 若要使用中繼資料和後續提示，請使用編輯內容，因為這項資料會在個別的 QnA 配對層級上新增。
* 針對 **檔案**，請使用 [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) 物件。 FileDTO 包含檔案名稱，以及用來連接檔案的公用 URL。
* 針對 **URL**，請使用字串清單來代表公開可用的 URL。

建立步驟也包含知識庫的屬性：
* `defaultAnswerUsedForExtraction` - 找不到答案時會傳回的內容
* `enableHierarchicalExtraction` - 自動在已擷取的 QnA 配對之間建立提示關聯性
* `language` - 會在建立資源的第一個知識庫時，設定要在 Azure 搜尋服務索引中使用的語言。

請呼叫 [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) 方法，然後將傳回的作業識別碼傳遞至 [MonitorOperation](#get-status-of-an-operation) 方法以輪詢狀態。

下列程式碼的最後一行會傳回來自 MonitorOperation 回應的知識庫識別碼。

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=31)]

務必包含上述程式碼中參考的 [`MonitorOperation`](#get-status-of-an-operation) 函式，才能成功建立知識庫。

## <a name="update-a-knowledge-base"></a>更新知識庫

若要更新知識庫，請將知識庫識別碼以及包含 [add](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet)、[update](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) 和 [delete](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO 物件的 [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) 傳遞至 [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) 方法。 使用 [MonitorOperation](#get-status-of-an-operation) 方法來判斷更新是否成功。

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

務必包含上述程式碼中參考的 [`MonitorOperation`](#get-status-of-an-operation) 函式，才能成功更新知識庫。

## <a name="download-a-knowledge-base"></a>下載知識庫

使用 [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) 方法將資料庫下載為 [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet) 的清單。 此做法不等同於 QnA Maker 入口網站從 [設定] 頁面進行的匯出作業，原因是此方法的結果並非檔案。

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3)]

## <a name="publish-a-knowledge-base"></a>發佈知識庫

使用 [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) 方法來發佈知識庫。 這會採用目前已儲存且已定型的模型 (可透過知識庫識別碼加以參考)，並在端點加以發佈。 若要查詢您的知識庫，這將是必要步驟。

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]



## <a name="get-query-runtime-key"></a>取得查詢執行階段金鑰

知識庫發佈後，您必須要有查詢執行階段金鑰才能查詢執行階段。 這不是用來建立原始用戶端物件的相同金鑰。

使用 [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) 方法取得 [EndpointKeysDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet) 類別。

使用物件中傳回的其中一個金鑰屬性來查詢知識庫。

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

必須要有執行階段金鑰才能查詢您的知識庫。

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>驗證執行階段以產生解答

建立 [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) 來查詢知識庫，以產生解答，或從主動式學習進行訓練。

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

使用 QnAMakerRuntimeClient 進行下列動作：
* 從知識庫取得答案
* 將新的建議問題傳送至[主動式學習](../concepts/active-learning-suggestions.md)的知識庫。

## <a name="generate-an-answer-from-the-knowledge-base"></a>從知識庫產生答案

### <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

使用 [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) 方法從已發佈的知識庫產生答案。 此方法會接受知識庫識別碼和 [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet)。 存取 QueryDTO 的其他屬性 (例如[Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) 和 [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet))，以在聊天機器人中使用。

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]


### <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)

使用 [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet-preview).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync?view=azure-dotnet-preview) 方法從已發佈的知識庫產生答案。 此方法會接受知識庫識別碼和 [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet-preview)。 存取 QueryDTO 的其他屬性 (例如 [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top)、[Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) 和 [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest))，以在聊天機器人中使用。

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

這是查詢知識庫的簡單範例。 若要了解進階查詢案例，請檢閱[其他查詢範例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。


## <a name="delete-a-knowledge-base"></a>刪除知識庫

使用 [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) 方法搭配知識庫識別碼的參數來刪除知識庫。

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>取得作業的狀態

某些方法 (例如 create 和 update) 的時間很充裕，而不必等候程序完成，並傳回[作業](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet)。 從要輪詢 (使用重試邏輯) 的作業使用[作業識別碼](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId)來確定原始方法的狀態。

下列程式碼區塊中的 loop 和 Task.Delay 可用來模擬重試邏輯。 這些項目應該取代為您自己的重試邏輯。

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>執行應用程式

使用 `dotnet run` 命令，從您的應用程式目錄執行應用程式。

```dotnetcli
dotnet run
```

此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart) 上找到。
