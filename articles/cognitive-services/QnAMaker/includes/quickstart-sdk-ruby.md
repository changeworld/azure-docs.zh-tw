---
title: 快速入門：適用於 Ruby 的 QnA Maker 用戶端程式庫
description: 本快速入門說明如何開始使用適用於 Ruby 的 QnA Maker 用戶端程式庫。
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: ef0db373dc6faaa470470b8169fdb6ae61aa8dde
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982757"
---
使用適用於 Ruby 的 QnA Maker 用戶端程式庫來：

* 建立知識庫
* 更新知識庫
* 發佈知識庫
* 取得預測執行階段端點金鑰
* 等候長時間執行的工作
* 下載知識庫
* 取得答案
* 刪除知識庫

[程式庫原始程式碼 (撰寫)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated) | [程式庫原始程式碼 (執行階段)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated) | [套件 (撰寫)](https://rubygems.org/gems/azure_cognitiveservices_qnamaker) | [套件 (執行階段)](https://rubygems.org/gems/azure_cognitiveservices_qnamakerruntime) | [Ruby 範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/ruby/qnamaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* [Ruby 2.x](https://www.ruby-lang.org/)
* 擁有 Azure 訂用帳戶之後，請在 Azure 入口網站中建立 [QnA Maker 資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以取得您的撰寫金鑰和端點。 在其部署後，選取 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 QnA Maker API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="install-the-client-libraries"></a>安裝用戶端程式庫

安裝 Ruby 之後，您可以透過以下項目安裝用戶端程式庫：

```console
gem install azure_cognitiveservices_qnamaker
gem install azure_cognitiveservices_qnamakerruntime
```

### <a name="create-a-new-ruby-application"></a>建立新的 Ruby 應用程式

建立名為 `quickstart.rb` 的新檔案，並匯入下列程式庫。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Dependencies":::

為資源的 Azure 端點和金鑰建立變數。

> [!IMPORTANT]
> 移至 Azure 入口網站，並尋找在必要條件中建立用於 QnA Maker 資源的金鑰和端點。 您可以透過資源的 [金鑰和端點] 頁面，在 [資源管理] 下找到這些項目。
> 您必須要有完整的金鑰以建立您的知識庫。 您只需要來自端點的資源名稱。 格式為 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 例如，[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 可提供安全的金鑰儲存。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Resourcevariables":::

## <a name="object-models"></a>物件模型

QnA Maker 使用兩種不同的物件模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 是用來建立、管理、發佈和下載知識庫的物件。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 是可讓您透過 GenerateAnswer API 查詢知識庫，並使用定型 API 傳送新的建議問題 (作為[主動式學習](../concepts/active-learning-suggestions.md)的一部分) 的物件。

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 物件模型

撰寫 QnA Maker 用戶端是一種 [QnAMakerClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/qnamaker_client.rb) 物件，會使用含有金鑰的 MsRest::ServiceClientCredentials 向 Azure 進行驗證。

用戶端建立後，請使用用戶端[知識庫](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb)屬性的方法來建立、管理和發佈知識庫。

即時作業的方法通常會傳回結果 (如果有的話)。 長期作業的回應為[作業](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb)物件。 使用 `operation.operation_id` 值呼叫 [operations.get_details](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/operations.rb#L33) 方法，以確認[要求的狀態](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation_state_type.rb)。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 物件模型

執行階段 QnA Maker 用戶端是 [的 QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) 物件。

使用撰寫用戶端發佈知識庫之後，請使用執行階段用戶端的 [runtime.generate_answer](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/runtime.rb#L34) 方法，從知識庫取得答案。

當您呼叫 `generate_answer` 時，請傳遞 `custom_headers` 選擇性參數的雜湊。 此雜湊應包含索引鍵 `Authorization` 和 `EndpointKey YOUR_ENDPOINT_KEY` 值。 針對 YOUR_ENDPOINT_KEY 的值，使用撰寫用戶端呼叫 [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32)。

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>驗證用戶端以撰寫知識庫

使用您的撰寫端點和訂用帳戶金鑰將用戶端具現化。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationAuthor":::

## <a name="create-a-knowledge-base"></a>建立知識庫

知識庫會針對來自三個來源的 [CreateKbDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/create_kb_dto.rb) 物件儲存問題和答案組：

* 針對**編輯內容**，請使用 [QnADTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adto.rb) 物件。
    * 若要使用中繼資料和後續提示，請使用編輯內容，因為這項資料會在個別的 QnA 配對層級上新增。
* 針對**檔案**，請使用 [FileDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/file_dto.rb) 物件。 FileDTO 包含檔案名稱，以及用來連接檔案的公用 URL。
* 針對 **URL**，請使用字串清單來代表公開可用的 URL。

請呼叫 [create](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L554) 方法，然後將傳回之作業的 `operation_id` 屬性傳遞至 [operations.get_details](#get-status-of-an-operation) 方法，以輪詢狀態。

下列程式碼的最後一行會傳回知識庫識別碼。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="CreateKBMethod":::

## <a name="update-a-knowledge-base"></a>更新知識庫

您可以藉由呼叫 [knowledgebase.update](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L455) 並傳入知識庫識別碼和 [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dto.rb) 物件，來更新知識庫。 該物件可包含：
- [add](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoadd.rb)
- [update](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoupdate.rb)
- [delete](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtodelete.rb)

將傳回之作業的 `operation_id` 屬性傳遞至 [operations.get_details](#get-status-of-an-operation) 方法，以輪詢狀態。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="UpdateKBMethod":::

## <a name="download-a-knowledge-base"></a>下載知識庫

使用 [knowledgebase.download](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L651) 方法將資料庫下載為 [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adocuments_dto.rb) 的清單。 此做法不等同於 QnA Maker 入口網站從 [設定] 頁面進行的匯出作業，原因是此方法的結果並非 TSV 檔案。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DownloadKB":::

## <a name="publish-a-knowledge-base"></a>發佈知識庫

使用 [knowledgebase.publish](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L283) 方法來發佈知識庫。 這會採用目前已儲存且已定型的模型 (可透過知識庫識別碼加以參考)，並在端點加以發佈。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="PublishKB":::

## <a name="get-query-runtime-key"></a>取得查詢執行階段金鑰

知識庫發佈後，您必須要有執行階段端點金鑰才能查詢知識。 這與用來建立撰寫用戶端的訂用帳戶金鑰不同。

使用 [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32) 方法取得 [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/endpoint_keys_dto.rb) 物件。

使用物件中傳回的其中一個金鑰屬性來查詢知識庫。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GetQueryEndpointKey":::

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>驗證執行階段以產生解答

建立 [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) 來查詢知識庫，以產生解答，或從主動式學習進行訓練。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationQuery":::

使用 QnAMakerRuntimeClient 從知識取得解答，或將新的建議問題傳送至[主動式學習](../concepts/active-learning-suggestions.md)的知識庫。

## <a name="generate-an-answer-from-the-knowledge-base"></a>從知識庫產生答案

使用 RuntimeClient.runtime.generateAnswer 方法從已發佈的知識庫產生答案。 此方法會接受知識庫識別碼和 QueryDTO。 存取 QueryDTO 的其他屬性 (例如 Top 和 Context)，以在聊天機器人中使用。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GenerateAnswer":::

這是查詢知識庫的簡單範例。 若要了解進階查詢案例，請檢閱[其他查詢範例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>刪除知識庫

使用 [knowledgebase.delete](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L205) 方法搭配知識庫識別碼的參數來刪除知識庫。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DeleteKB":::

## <a name="get-status-of-an-operation"></a>取得作業的狀態

某些方法 (例如 create 和 update) 的時間很充裕，而不必等候程序完成，並傳回[作業](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb)。 從要輪詢 (使用重試邏輯) 的作業使用作業識別碼來確認原始方法的狀態。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="MonitorOperation":::

## <a name="run-the-application"></a>執行應用程式

以下是應用程式的主要方法。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Main":::

使用快速入門檔案上的 Ruby 命令執行應用程式。

```console
ruby quickstart.rb
```

此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/ruby/qnamaker/sdk/quickstart.rb) 上找到。
