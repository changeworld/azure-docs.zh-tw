---
title: 快速入門：適用於 Python 的 QnA Maker 用戶端程式庫
description: 本快速入門示範如何開始使用適用於 Python 的 QnA Maker 用戶端程式庫。
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 1b6990589663655c5b4518d55e42838775889671
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2020
ms.locfileid: "85114507"
---
使用適用於 Python 的 QnA Maker 用戶端程式庫來：

* 建立知識庫
* 更新知識庫
* 發佈知識庫
* 取得預測執行階段端點金鑰
* 等候長時間執行的工作
* 下載知識庫
* 取得答案
* 刪除知識庫

[參考文件](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [套件 (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python 範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* 擁有 Azure 訂用帳戶之後，請在 Azure 入口網站中建立 [QnA Maker 資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以取得您的撰寫金鑰和端點。 在其部署後，選取 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 QnA Maker API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後，您可以透過以下項目安裝用戶端程式庫：

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

建立名為 `quickstart-file.py` 的新 Python 檔案，並匯入下列程式庫。

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies&highlight=4,5)]

為資源的 Azure 端點和金鑰建立變數。

> [!IMPORTANT]
> 移至 Azure 入口網站，並尋找在必要條件中建立用於 QnA Maker 資源的金鑰和端點。 您可以透過資源的 [金鑰和端點] 頁面，在 [資源管理] 下找到這些項目。
> 您必須要有完整的金鑰以建立您的知識庫。 您只需要來自端點的資源名稱。 格式為 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 例如，[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 可提供安全的金鑰儲存。

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

## <a name="object-models"></a>物件模型

[QnA Maker](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) 使用兩種不同的物件模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 是用來建立、管理、發佈和下載知識庫的物件。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 是可讓您透過 GenerateAnswer API 查詢知識庫，並使用定型 API 傳送新的建議問題 (作為[主動式學習](../concepts/active-learning-suggestions.md)的一部分) 的物件。

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 物件模型

製作 QnA Maker 用戶端是一種 [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) 物件，會使用含有金鑰的 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證。

建立用戶端之後，請使用[知識庫](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python)屬性來建立、管理和發佈知識庫。

藉由傳送 JSON 物件來管理知識庫。 針對立即性作業，方法通常會傳回可指出狀態的 JSON 物件。 針對長期作業，回應則是作業識別碼。 使用作業識別碼來呼叫 [operations.get_details](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.operations(class)?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) 方法，以確認[要求的狀態](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operation(class)?view=azure-python)。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 物件模型

預測 QnA Maker 用戶端是一種 [QnAMakerRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.runtime.qnamakerruntimeclient?view=azure-python) 物件，會使用 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證，其中包含您的預測執行階段金鑰，這是在發佈知識庫後由製作用戶端呼叫 [client.EndpointKeysOperations.get_keys](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) 傳回的金鑰。

使用 [generate_answer](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.runtime.operations.runtimeoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) 方法從查詢執行階段取得答案。


## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>驗證用戶端以撰寫知識庫

使用端點和金鑰來具現化用戶端。 使用金鑰建立 CognitiveServicesCredentials 物件，並使用該物件與您的端點建立 [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.qnamakerclient?view=azure-python) 物件。

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>建立知識庫

 使用用戶端物件取得[知識庫作業](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations?view=azure-python)物件。

知識庫會針對來自三個來源的 [CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.create_kb_dto?view=azure-python) 物件儲存問題和答案組：

* 針對**編輯內容**，請使用 [QnADTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qn_adto?view=azure-python) 物件。
    * 若要使用中繼資料和後續提示，請使用編輯內容，因為這項資料會在個別的 QnA 配對層級上新增。
* 針對**檔案**，請使用 [FileDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.file_dto?view=azure-python) 物件。 FileDTO 包含檔案名稱，以及用來連接檔案的公用 URL。
* 針對 **URL**，請使用字串清單來代表公開可用的 URL。

請呼叫 [create](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) 方法，然後將傳回的作業識別碼傳至 [Operations.getDetails](#get-status-of-an-operation) 方法，以輪詢狀態。

下列程式碼的最後一行會傳回來自 MonitorOperation 回應的知識庫識別碼。

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod&highlight=36,38)]

務必包含上述程式碼中參考的 [`_monitor_operation`](#get-status-of-an-operation) 函式，才能成功建立知識庫。

## <a name="update-a-knowledge-base"></a>更新知識庫

若要更新知識庫，請將知識庫識別碼以及包含 [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_add?view=azure-python)、[update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_update?view=azure-python) 和 [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_delete?view=azure-python) DTO 物件的 [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto?view=azure-python) 傳至 [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) 方法。 使用 [Operation.getDetail](#get-status-of-an-operation) 方法確認更新是否成功。

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod&highlight=68,69)]

務必包含上述程式碼中參考的 [`_monitor_operation`](#get-status-of-an-operation) 函式，才能成功更新知識庫。

## <a name="download-a-knowledge-base"></a>下載知識庫

使用 [download](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) 方法將資料庫下載為 [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qnadocumentsdto?view=azure-python) 的清單。 此做法不等同於 QnA Maker 入口網站從 [設定] 頁面進行的匯出作業，原因是此方法的結果並非 TSV 檔案。

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>發佈知識庫

使用 [publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) 方法來發佈知識庫。 這會採用目前已儲存且已定型的模型 (可透過知識庫識別碼加以參考)，並在端點加以發佈。

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB&highlight=2)]

## <a name="get-query-runtime-key"></a>取得查詢執行階段金鑰

知識庫發佈後，您必須要有查詢執行階段金鑰才能查詢執行階段。 這不是用來建立原始用戶端物件的相同金鑰。

使用 [EndpointKeysOperations.get_keys](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) 方法取得 [EndpointKeysDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.endpointkeysdto?view=azure-python) 類別。

使用物件中傳回的其中一個金鑰屬性來查詢知識庫。

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey&highlight=2)]


## <a name="authenticate-the-runtime-for-generating-an-answer"></a>驗證執行階段以產生解答

建立 [QnAMakerRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.runtime.qnamakerruntimeclient?view=azure-python) 來查詢知識庫，以產生解答，或從主動式學習進行訓練。

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

使用 QnAMakerRuntimeClient 從知識取得解答，或將新的建議問題傳送至[主動式學習](../concepts/active-learning-suggestions.md)的知識庫。

## <a name="generate-an-answer-from-the-knowledge-base"></a>從知識庫產生答案

使用 RuntimeClient.runtime.generateAnswer 方法從已發佈的知識庫產生答案。 此方法會接受知識庫識別碼和 QueryDTO。 存取 QueryDTO 的其他屬性 (例如 Top 和 Context)，以在聊天機器人中使用。

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer&highlight=5)]

這是查詢知識庫的簡單範例。 若要了解進階查詢案例，請檢閱[其他查詢範例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>刪除知識庫

使用 [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) 方法搭配知識庫識別碼的參數來刪除知識庫。

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>取得作業的狀態

某些方法 (例如 create 和 update) 的時間很充裕，而不必等候程序完成，並傳回[作業](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operation.operation?view=azure-python)。 從要輪詢 (使用重試邏輯) 的作業使用作業識別碼來確認原始方法的狀態。

下列程式碼區塊中的 _setTimeout_ 呼叫可用來模擬非同步程式碼。 請將此項目取代為重試邏輯。

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation&highlight=7)]

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 Python 命令執行應用程式。

```console
python quickstart-file.py
```

此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py) 上找到。