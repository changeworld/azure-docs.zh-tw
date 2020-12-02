---
title: 快速入門：適用於 Node.js 的 QnA Maker 用戶端程式庫
description: 本快速入門示範如何開始使用適用於 Node.js 的 QnA Maker 用戶端程式庫。
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9eb79223e91d31c76631d17263f6f33632824266
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350988"
---
使用適用於 Node.js 的 QnA Maker 用戶端程式庫來：

* 建立知識庫
* 更新知識庫
* 發佈知識庫
* 取得預測執行階段端點金鑰
* 等候長時間執行的工作
* 下載知識庫
* 取得答案
* 刪除知識庫

[參考文件](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [套件 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js 範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 最新版的 [Node.js](https://nodejs.org)。
* 擁有 Azure 訂用帳戶之後，請在 Azure 入口網站中建立 [QnA Maker 資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以取得您的撰寫金鑰和資源。 在其部署後，選取 [前往資源]。
    * 您需要來自所建立資源的金鑰和資源名稱，以將應用程式連線至 QnA Maker API。 您稍後會在快速入門中將金鑰和資源名稱貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

執行命令 `npm init -y`，以使用 `package.json` 檔案建立節點應用程式。

```console
npm init -y
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝下列 NPM 套件：

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

您應用程式的 `package.json` 檔案會隨著相依性而更新。

建立名為 index.js 的檔案，並匯入下列程式庫：

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

為資源的 Azure 金鑰和資源名稱建立變數。 製作和預測 URL 都會使用資源名稱作為子網域。

> [!IMPORTANT]
> 移至 Azure 入口網站，並尋找在必要條件中建立用於 QnA Maker 資源的金鑰和端點。 您可以透過資源的 [金鑰和端點] 頁面，在 [資源管理] 下找到這些項目。
> 您必須要有完整的金鑰以建立您的知識庫。 您只需要來自端點的資源名稱。 格式為 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 例如，[Azure Key Vault](../../../key-vault/general/overview.md) 可提供安全的金鑰儲存。

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

## <a name="object-models"></a>物件模型

QnA Maker 使用兩種不同的物件模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 是用來建立、管理、發佈和下載知識庫的物件。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 是可讓您透過 GenerateAnswer API 查詢知識庫，並使用定型 API 傳送新的建議問題 (作為 [主動式學習](../concepts/active-learning-suggestions.md)的一部分) 的物件。


### <a name="qnamakerclient-object-model"></a>QnAMakerClient 物件模型

製作 QnA Maker 用戶端是一種 [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) 物件，會使用含有金鑰的認證向 Azure 進行驗證。

建立用戶端之後，請使用[知識庫](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase)建立、管理和發佈知識庫。

藉由傳送 JSON 物件來管理知識庫。 針對立即性作業，方法通常會傳回可指出狀態的 JSON 物件。 針對長期作業，回應則是作業識別碼。 使用作業識別碼來呼叫 [client.operations.getDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--msrest-requestoptionsbase-) 方法，以確認[要求的狀態](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest)。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 物件模型

預測 QnA Maker 用戶端是一種 QnAMakerRuntimeClient 物件，會使用 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證，其中包含您的預測執行階段金鑰，這是在發佈知識庫後由製作用戶端呼叫 [client.EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest#getkeys-msrest-requestoptionsbase-) 傳回的金鑰。


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

使用端點和金鑰來具現化用戶端。 使用金鑰建立 ServiceClientCredentials 物件，並使用該物件與您的端點建立 [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) 物件。

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>建立知識庫

知識庫會針對來自三個來源的 [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) 物件儲存問題和答案組：

* 針對 **編輯內容**，請使用 [QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) 物件。
    * 若要使用中繼資料和後續提示，請使用編輯內容，因為這項資料會在個別的 QnA 配對層級上新增。
* 針對 **檔案**，請使用 [FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) 物件。 FileDTO 包含檔案名稱，以及用來連接檔案的公用 URL。
* 針對 **URL**，請使用字串清單來代表公開可用的 URL。

建立步驟也包含知識庫的屬性：
* `defaultAnswerUsedForExtraction` - 找不到答案時會傳回的內容
* `enableHierarchicalExtraction` - 自動在已擷取的 QnA 配對之間建立提示關聯性
* `language` - 會在建立資源的第一個知識庫時，設定要在 Azure 搜尋服務索引中使用的語言。

使用知識庫資訊呼叫 [create](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) 方法。 知識庫資訊基本上是 JSON 物件。

create 方法傳回後，將傳回的作業識別碼傳至 [wait_for_operation](#get-status-of-an-operation) 方法，以輪詢狀態。 當作業完成時，wait_for_operation 方法會傳回。 剖析傳回作業的 `resourceLocation` 標頭值，以取得新的知識庫識別碼。

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod&highlight=39,46)]

務必包含上述程式碼中參考的 [`wait_for_operation`](#get-status-of-an-operation) 函式，才能成功建立知識庫。

## <a name="update-a-knowledge-base"></a>更新知識庫

若要更新知識庫，請將知識庫識別碼以及包含 [add](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add)、[update](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) 和 [delete](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO 物件的 [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) 傳至 [update](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) 方法。 DTO 基本上也是 JSON 物件。 使用 [wait_for_operation](#get-status-of-an-operation) 方法來判斷更新是否成功。

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod&highlight=74,81)]

務必包含上述程式碼中參考的 [`wait_for_operation`](#get-status-of-an-operation) 函式，才能成功更新知識庫。

## <a name="download-a-knowledge-base"></a>下載知識庫

使用 [download](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#download-string--models-environmenttype--msrest-requestoptionsbase-) 方法將資料庫下載為 [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto?view=azure-node-latest) 的清單。 此做法不等同於 QnA Maker 入口網站從 [設定] 頁面進行的匯出作業，原因是此方法的結果並非 TSV 檔案。

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB&highlight=2)]


## <a name="publish-a-knowledge-base"></a>發佈知識庫

使用 [publish](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) 方法來發佈知識庫。 這會採用目前已儲存且已定型的模型 (可透過知識庫識別碼加以參考)，並在端點加以發佈。 檢查 HTTP 回應碼以驗證發佈是否成功。

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB&highlight=3)]


## <a name="get-query-runtime-key"></a>取得查詢執行階段金鑰

知識庫發佈後，您必須要有查詢執行階段金鑰才能查詢執行階段。 這不是用來建立原始用戶端物件的相同金鑰。

使用 [EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest) 方法取得 [EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto?view=azure-node-latest) 類別。

使用物件中傳回的其中一個金鑰屬性來查詢知識庫。

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey&highlight=4)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>驗證執行階段以產生解答

建立 QnAMakerRuntimeClient 來查詢知識庫，以產生解答，或從主動式學習進行訓練。

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

使用 QnAMakerRuntimeClient 從知識取得解答，或將新的建議問題傳送至[主動式學習](../concepts/active-learning-suggestions.md)的知識庫。

## <a name="generate-an-answer-from-the-knowledge-base"></a>從知識庫產生答案

使用 RuntimeClient.runtime.generateAnswer 方法從已發佈的知識庫產生答案。 此方法會接受知識庫識別碼和 QueryDTO。 存取 QueryDTO 的其他屬性 (例如 Top 和 Context)，以在聊天機器人中使用。

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer&highlight=3)]

這是查詢知識庫的簡單範例。 若要了解進階查詢案例，請檢閱[其他查詢範例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>刪除知識庫

使用 [delete](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) 方法搭配知識庫識別碼的參數來刪除知識庫。

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>取得作業的狀態

某些方法 (例如 create 和 update) 的時間很充裕，而不必等候程序完成，並傳回[作業](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest)。 從要輪詢 (使用重試邏輯) 的作業使用[作業識別碼](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid)來確定原始方法的狀態。

下列程式碼區塊中的 _delayTimer_ 呼叫可用來模擬重試邏輯。 請將此項目取代為您自己的重試邏輯。

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation&highlight=8)]

## <a name="run-the-application"></a>執行應用程式

使用 `node index.js` 命令從您的應用程式目錄執行應用程式。

```console
node index.js
```

此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) 上找到。