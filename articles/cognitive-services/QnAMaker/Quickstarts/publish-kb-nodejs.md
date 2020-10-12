---
title: 快速入門：使用適用于 Node.js 的 REST Api QnA Maker
description: 本快速入門示範如何開始使用適用於 Node.js 的 QnA Maker REST API。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。  QnA Maker 可讓您加強常見問題集文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-js
ms.topic: how-to
ms.openlocfilehash: ef17f08677d715292a8ee49621156d130e2f5a6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777489"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>快速入門：適用于 Node.js QnA Maker REST Api

開始使用適用於 Node.js 的 QnA Maker REST API。 請依照下列步驟試用基本工作的範例程式碼。  QnA Maker 可讓您加強常見問題集文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。

使用適用於 Node.js 的 QnA Maker REST API 來：

* 建立知識庫
* 取代知識庫
* 發佈知識庫
* 刪除知識庫
* 下載知識庫
* 取得作業的狀態

[參考檔](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  | [Node.js 範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 最新版的 [Node.js](https://nodejs.org)。
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰和端點 (其中包含資源名稱)，請在 Azure 入口網站中選取資源的 [快速入門]****。

## <a name="setting-up"></a>設定

### <a name="create-a-qna-maker-azure-resource"></a>建立 QnA Maker Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立 QnA Maker 的資源。

從資源取得金鑰後，請為資源[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) (名為 `QNAMAKER_RESOURCE_KEY` 和 `QNAMAKER_AUTHORING_ENDPOINT`)。 使用在 Azure 入口網站中位於資源的 [快速入門]**** 頁面中的金鑰和端點值。

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。

```console
mkdir myapp && cd myapp
```

執行 `npm init -y` 命令以建立節點 `package.json` 檔案。

```console
npm init -y
```

新增 `reqeuestretry` 和 `request` NPM 套件：

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會說明如何使用適用於 Node.js 的 QnA Maker REST API 來執行下列動作：

* [建立知識庫](#create-a-knowledge-base)
* [取代知識庫](#replace-a-knowledge-base)
* [發佈知識庫](#publish-a-knowledge-base)
* [刪除知識庫](#delete-a-knowledge-base)
* [下載知識庫](#download-the-knowledge-base)
* [取得作業的狀態](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>新增相依性

建立名為的檔案 `rest-apis.js` ，並新增下列相依性。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="dependencies":::

## <a name="add-utility-functions"></a>新增公用程式函式

新增下列公用程式函數。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="utility":::

## <a name="add-azure-resource-information"></a>新增 Azure 資源資訊

為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

設定下列環境值：

* `QNAMAKER_RESOURCE_KEY` -索引 **鍵** 是32字元字串，且可在 [ **快速入門** ] 頁面上的 [QnA Maker 資源] Azure 入口網站中使用。 這與預測端點金鑰不同。
* `QNAMAKER_AUTHORING_ENDPOINT` - 您的撰寫端點 (格式為 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`) 包含您的**資源名稱**。 這與用於查詢預測端點的 URL 不同。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="authorization":::

## <a name="create-a-knowledge-base"></a>建立知識庫

知識庫會儲存從下列項目的 JSON 物件建立的問題和答案組：

* **編輯內容**。
* **檔案** - 不需要任何權限的本機檔案。
* **URL** - 公用的 URL。

使用 [REST API 建立知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="createKb":::

## <a name="replace-a-knowledge-base"></a>取代知識庫

使用 [REST API 取代知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="replaceKb":::

## <a name="publish-a-knowledge-base"></a>發佈知識庫

發佈知識庫。 此程序可讓使用者從 HTTP 查詢預測端點存取知識庫。

使用 [REST API 發佈知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="publishKb":::

## <a name="download-the-knowledge-base"></a>下載知識庫

使用 [REST API 下載知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="downloadKb":::

## <a name="delete-a-knowledge-base"></a>刪除知識庫

知識庫使用完畢後，請加以刪除。

使用 [REST API 刪除知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="deleteKb":::

## <a name="get-status-of-an-operation"></a>取得作業的狀態

長時間執行的程序 (例如建立程序) 會傳回作業識別碼，此識別碼必須以個別的 REST API 呼叫來查看。 此函式會取得建立回應的本文。 重點在於 `operationState`，它可確認您是否需要繼續輪詢。

使用 [REST API 監視知識庫的作業](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="operationDetails":::

## <a name="add-main-method"></a>新增 main 方法

新增下列 `main` 方法。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="main":::

## <a name="run-the-application"></a>執行應用程式

使用 `node rest-apis.js` 命令從您的應用程式目錄執行應用程式。

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[教學課程：建立及回答 KB](../tutorials/create-publish-query-in-portal.md)

* [QnA Maker API 是什麼？](../Overview/overview.md)
* [編輯知識庫](../how-to/edit-knowledge-base.md)
* [取得使用情況分析](../how-to/get-analytics-knowledge-base.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js) 上找到。
