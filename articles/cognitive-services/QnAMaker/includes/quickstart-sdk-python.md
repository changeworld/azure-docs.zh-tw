---
ms.openlocfilehash: 13dcdec4eff72f97e1af8ecb26e32c0ef5f881fd
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81637055"
---

使用適用於 Python 的 QnA Maker 用戶端程式庫來：

* 建立知識庫
* 管理知識庫
* 發佈知識庫

[參考文件](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [套件 (pypi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python 範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-qna-maker-azure-resource"></a>建立 QnA Maker Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立 QnA Maker 的資源。

從資源取得金鑰後，請為資源[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) (名為 `QNAMAKER_KEY` 和 `QNAMAKER_HOST`)。 使用位於 Azure 入口網站中的金鑰和端點值。

### <a name="install-the-python-library-for-qna-maker"></a>安裝 Python QnA Maker 程式庫

安裝 Python 之後，您可以透過以下項目安裝用戶端程式庫：

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

## <a name="object-model"></a>物件模型

使用金鑰建立 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 物件，並使用該物件與您的端點建立 [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.qn_amaker_client?view=azure-python) 物件。

建立用戶端之後，請使用[知識庫](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations?view=azure-python)建立、管理和發佈知識庫。

針對立即性作業，方法通常會傳回可指出狀態的 JSON 物件。 針對長期作業，回應則是作業識別碼。 使用作業識別碼來呼叫 [client.Operations.getDetails](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operationstatetype?view=azure-python) 方法，以確認[要求的狀態](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python)。


## <a name="code-examples"></a>程式碼範例

這些程式碼片段會說明如何使用適用於 Python 的 QnA Maker 用戶端程式庫執行下列動作：

* [建立知識庫](#create-a-knowledge-base)
* [更新知識庫](#update-a-knowledge-base)
* [發佈知識庫](#publish-a-knowledge-base)
* [下載知識庫](#download-a-knowledge-base)
* [刪除知識庫](#delete-a-knowledge-base)
* [取得作業的狀態](#get-status-of-an-operation)

## <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

在您慣用的編輯器或 IDE 中，建立新的 Python 應用程式。 然後匯入下列程式庫。

[!code-python[Dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=dependencies)]

為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

|環境變數|變動|範例|
|--|--|--|
|`QNAMAKER_KEY`|`subscription_key`|金鑰是 32 字元字串，且可在 Azure 入口網站中 [快速入門] 頁面上的 [QnA Maker 資源] 上取得。 這與預測端點金鑰不同。|
|`QNAMAKER_HOST`|`host`| 您的撰寫端點 (格式為 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`) 包含您的**資源名稱**。 這與用於查詢預測端點的 URL 不同。|

[!code-python[Azure resource variables](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=resourcekeys)]

## <a name="authenticate-the-client"></a>驗證用戶端

接著，使用金鑰建立 CognitiveServicesCredentials 物件，並使用該物件與您的端點建立 [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python) 物件。


[!code-python[Authorization to resource key](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=authorization)]

## <a name="create-a-knowledge-base"></a>建立知識庫

 使用用戶端物件取得[知識庫作業](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations?view=azure-python)物件。

知識庫會針對來自三個來源的 [CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.create_kb_dto?view=azure-python) 物件儲存問題和答案組：

* 針對**編輯內容**，請使用 [QnADTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qn_adto?view=azure-python) 物件。
* 針對**檔案**，請使用 [FileDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.file_dto?view=azure-python) 物件。
* 針對 **URL**，請使用字串清單。

請呼叫 [create](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) 方法，然後將傳回的作業識別碼傳至 [Operations.getDetails](#get-status-of-an-operation) 方法，以輪詢狀態。

[!code-python[Create a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=createkb&highlight=15)]

務必包含上述程式碼中參考的 [`_monitor_operation`](#get-status-of-an-operation) 函式，才能成功建立知識庫。

## <a name="update-a-knowledge-base"></a>更新知識庫

若要更新知識庫，請將知識庫識別碼以及包含 [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_add?view=azure-python)、[update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_update?view=azure-python) 和 [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_delete?view=azure-python) DTO 物件的 [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto?view=azure-python) 傳至 [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) 方法。 使用 [Operation.getDetail](#get-status-of-an-operation) 方法確認更新是否成功。

[!code-python[Update a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=updatekb&highlight=2)]

務必包含上述程式碼中參考的 [`_monitor_operation`](#get-status-of-an-operation) 函式，才能成功更新知識庫。

## <a name="publish-a-knowledge-base"></a>發佈知識庫

使用 [publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) 方法來發佈知識庫。 這會採用目前已儲存且已定型的模型 (可透過知識庫識別碼加以參考)，並在端點加以發佈。

[!code-python[Publish a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=publishkb&highlight=2)]

## <a name="download-a-knowledge-base"></a>下載知識庫

使用 [download](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) 方法將資料庫下載為 [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qn_adocuments_dto?view=azure-python) 的清單。 此做法不等同於  QnA Maker 入口網站從 [設定]  頁面進行的匯出作業，原因是此方法的結果並非 TSV 檔案。

[!code-python[Download a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=downloadkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>刪除知識庫

使用 [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) 方法搭配知識庫識別碼的參數來刪除知識庫。

[!code-python[Delete a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=deletekb&highlight=2)]

## <a name="get-status-of-an-operation"></a>取得作業的狀態

某些方法 (例如 create 和 update) 的時間很充裕，而不必等候程序完成，並傳回[作業](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operation.operation?view=azure-python)。 從要輪詢 (使用重試邏輯) 的作業使用作業識別碼來確認原始方法的狀態。

下列程式碼區塊中的 _setTimeout_ 呼叫可用來模擬非同步程式碼。 請將此項目取代為重試邏輯。

[!code-python[Monitor an operation](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=monitorOperation&highlight=7)]

## <a name="run-the-application"></a>執行應用程式

使用 `python knowledgebase_quickstart.py` 命令從您的應用程式目錄執行應用程式。

本文中的所有程式碼片段都是[可用](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)的，並可以單一檔案的形式執行。

```console
python knowledgebase_quickstart.py
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)
