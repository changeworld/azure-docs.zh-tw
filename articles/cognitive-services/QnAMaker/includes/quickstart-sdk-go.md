---
title: 快速入門：適用於 Go 的 QnA Maker 用戶端程式庫
description: 本快速入門說明如何開始使用適用於 Go 的 QnA Maker 用戶端程式庫。
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 621a0bbc6449c6deec9497d9f908edece8dba7f1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982768"
---
使用適用於 Go 的 QnA Maker 用戶端程式庫來：

* 建立知識庫
* 更新知識庫
* 發佈知識庫
* 取得預測執行階段端點金鑰
* 等候長時間執行的工作
* 下載知識庫
* 取得答案
* 刪除知識庫

[參考 (撰寫)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker) | [參考 (執行階段)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime) | [程式庫原始程式碼 (撰寫)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamaker) | [程式庫原始程式碼 (執行階段)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamakerruntime) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/QnAMaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* [Go](https://golang.org/)
* 擁有 Azure 訂用帳戶之後，請在 Azure 入口網站中建立 [QnA Maker 資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以取得您的撰寫金鑰和端點。 在其部署後，選取 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 QnA Maker API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="create-a-new-go-application"></a>建立新的 Go 應用程式

建立名為 `kb_sample.go` 的新檔案，並匯入下列程式庫。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="dependencies":::

為資源的 Azure 端點和金鑰建立變數。

> [!IMPORTANT]
> 移至 Azure 入口網站，並尋找在必要條件中建立用於 QnA Maker 資源的金鑰和端點。 您可以透過資源的 [金鑰和端點] 頁面，在 [資源管理] 下找到這些項目。
> 您必須要有完整的金鑰以建立您的知識庫。 您只需要來自端點的資源名稱。 格式為 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 例如，[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 可提供安全的金鑰儲存。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="environment":::

## <a name="object-models"></a>物件模型

QnA Maker 使用兩種不同的物件模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 是用來建立、管理、發佈和下載知識庫的物件。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 是可讓您透過 GenerateAnswer API 查詢知識庫，並使用定型 API 傳送新的建議問題 (作為[主動式學習](../concepts/active-learning-suggestions.md)的一部分) 的物件。

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 物件模型

使用 [KnowledgebaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient) 來建立、更新、發佈、下載及刪除您的知識庫。 以下各節將說明這些作業。

使用 [OperationsClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#OperationsClient) 檢查長時間執行作業的狀態，例如建立和更新知識庫。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 物件模型

發佈知識庫之後，請使用 [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient) 查詢您的知識庫。 [查詢知識庫](#query-the-knowledge-base)會說明這項作業。

## <a name="create-a-knowledge-base"></a>建立知識庫

知識庫會針對來自三個來源的 [CreateKbDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#CreateKbDTO) 物件儲存問題和答案組：

* 針對**編輯內容**，請使用 [QnADTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADTO) 物件。
    * 若要使用中繼資料和後續提示，請使用編輯內容，因為這項資料會在個別的 QnA 配對層級上新增。
* 針對**檔案**，請使用 [FileDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#FileDTO) 物件。 FileDTO 包含檔案名稱，以及用來連接檔案的公用 URL。
* 針對 **URL**，請使用字串清單來代表公開可用的 URL。

請呼叫 [create](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Create) 方法，然後將傳回之作業的 `operationId` 屬性傳遞至 [getDetails](#get-status-of-an-operation) 方法，以輪詢狀態。

下列程式碼的最後一行會傳回知識庫識別碼。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="create_kb":::

## <a name="update-a-knowledge-base"></a>更新知識庫

您可以藉由呼叫 [update](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Update) 並傳入知識庫識別碼和 [UpdateKbOperationDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTO) 物件，來更新知識庫。 該物件可包含：
- [add](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOAdd)
- [update](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOUpdate)
- [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTODelete)

將傳回之作業的 `operationId` 屬性傳遞至 [getDetails](#get-status-of-an-operation) 方法，以輪詢狀態。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="update_kb":::

## <a name="download-a-knowledge-base"></a>下載知識庫

使用 [download](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Download) 方法將資料庫下載為 [QnADocumentsDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADocumentsDTO) 的清單。 此做法不等同於 QnA Maker 入口網站從 [設定] 頁面進行的匯出作業，原因是此方法的結果並非 TSV 檔案。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="download_kb":::

## <a name="publish-a-knowledge-base"></a>發佈知識庫

使用 [publish](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Publish) 方法來發佈知識庫。 這會採用目前已儲存且已定型的模型 (可透過知識庫識別碼加以參考)，並在端點加以發佈。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="publish_kb":::

## <a name="query-the-knowledge-base"></a>查詢知識庫

知識庫發佈後，您必須要有執行階段端點金鑰才能查詢知識。 這與用來建立撰寫用戶端的訂用帳戶金鑰不同。

建立 [EndpointKeysClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient)，並使用 [getKeys](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient.GetKeys) 方法取得 [EndpointKeysDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysDTO) 物件。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="get_runtime_endpoint_key":::

建立 [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient) 並呼叫 [GenerateAnswer](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient.GenerateAnswer) 方法，以查詢知識庫。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="query_kb":::

這是查詢知識庫的簡單範例。 若要了解進階查詢案例，請檢閱[其他查詢範例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>刪除知識庫

使用 [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Delete) 方法搭配知識庫識別碼的參數來刪除知識庫。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="delete_kb":::

## <a name="get-status-of-an-operation"></a>取得作業的狀態

某些方法 (例如 create 和 update) 的時間很充裕，而不必等候程序完成，並傳回[作業](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#Operation)。 從要輪詢 (使用重試邏輯) 的作業使用作業識別碼來確認原始方法的狀態。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="wait_for_operation":::

## <a name="handle-errors"></a>處理錯誤

下列程式碼說明如何處理 SDK 的函式可能傳回的錯誤。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="error_handling":::

## <a name="run-the-application"></a>執行應用程式

以下是應用程式的主要方法。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="main":::

使用快速入門檔案上的 Go 命令執行應用程式。

```console
go run kb_sample.go
```

此範例的原始程式碼位於 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/qnamaker/sdk/kb_sample.go)
