---
title: 快速入門：使用適用于 node.js 的 REST Api QnA Maker
description: 本快速入門示範如何開始使用適用於 Node.js 的 QnA Maker REST API。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。  QnA Maker 可讓您加強常見問題集文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: ecc3fb144fb4b4e27182567925199f841b1c4357
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851959"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>快速入門： QnA Maker 適用于 node.js 的 REST Api

開始使用適用於 Node.js 的 QnA Maker REST API。 請依照下列步驟試用基本工作的範例程式碼。  QnA Maker 可讓您加強常見問題集文件或 URL 及產品手冊等半結構化內容中的問題與解答服務。

使用適用於 Node.js 的 QnA Maker REST API 來：

* 建立知識庫
* 取代知識庫
* 發佈知識庫
* 刪除知識庫
* 下載知識庫
* 取得作業的狀態

[參考文件](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js 範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版的 [Node.js](https://nodejs.org)。
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰和端點 (其中包含資源名稱)，請在 Azure 入口網站中選取資源的 [快速入門]。

## <a name="setting-up"></a>設定

### <a name="create-a-qna-maker-azure-resource"></a>建立 QnA Maker Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立 QnA Maker 的資源。

從資源取得金鑰後，請為資源[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) (名為 `QNAMAKER_RESOURCE_KEY` 和 `QNAMAKER_AUTHORING_ENDPOINT`)。 使用在 Azure 入口網站中位於資源的 [快速入門] 頁面中的金鑰和端點值。

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

建立名為 `rest-apis.js` 的檔案，並新增下列 _requires_ 陳述式以提出 HTTP 要求。

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>新增 Azure 資源資訊

為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須先關閉執行該應用程式的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

設定下列環境值：

* `QNAMAKER_RESOURCE_KEY`-**金鑰**是32字元字串，而且可在 Azure 入口網站的 [**快速入門**] 頁面上的 [QnA Maker] 資源上取得。 這與預測端點金鑰不同。
* `QNAMAKER_AUTHORING_ENDPOINT` - 您的撰寫端點 (格式為 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`) 包含您的**資源名稱**。 這與用於查詢預測端點的 URL 不同。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>建立知識庫

知識庫會儲存從下列項目的 JSON 物件建立的問題和答案組：

* **編輯內容**。
* **檔案** - 不需要任何權限的本機檔案。
* **URL** - 公用的 URL。

使用 [REST API 建立知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>取代知識庫

使用 [REST API 取代知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>發佈知識庫

發佈知識庫。 此程序可讓使用者從 HTTP 查詢預測端點存取知識庫。

使用 [REST API 發佈知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)。


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>下載知識庫

使用 [REST API 下載知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>刪除知識庫

知識庫使用完畢後，請加以刪除。

使用 [REST API 刪除知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>取得作業的狀態

長時間執行的程序 (例如建立程序) 會傳回作業識別碼，此識別碼必須以個別的 REST API 呼叫來查看。 此函式會取得建立回應的本文。 重點在於 `operationState`，它可確認您是否需要繼續輪詢。

使用 [REST API 監視知識庫的作業](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)。


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


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
>[教學課程：建立及回答知識庫](../tutorials/create-publish-query-in-portal.md)

* [QnA Maker API 是什麼？](../Overview/overview.md)
* [編輯知識庫](../how-to/edit-knowledge-base.md)
* [取得使用情況分析](../how-to/get-analytics-knowledge-base.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js) 上找到。