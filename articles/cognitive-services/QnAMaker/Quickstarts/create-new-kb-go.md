---
title: 快速入門：建立知識庫 - REST (Go) - QnA Maker
description: 這個以 Go REST 為基礎的快速入門會逐步引導您以程式設計方式建立範例 QnA Maker 知識庫，且該知識庫會出現在認知服務 API 帳戶的 Azure 儀表板中。
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 221220345f4f3b7aff2a32c956d921f677ca0627
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78852003"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>快速入門：使用 Go 在 QnA Maker 中建立知識庫

本快速入門會逐步引導您以程式設計方式建立 QnA Maker 知識庫範例。 QnA Maker 會從[資料來源](../Concepts/knowledge-base.md)中的半結構化內容 (如常見問題集) 自動擷取問題和答案。 知識庫的模型是在 JSON (在 API 要求的本體中傳送) 中定義的。

本快速入門會呼叫 QnA Maker API：
* [建立 KB](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [取得作業詳細資料](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[參考文件](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [GO 範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必要條件

* [Go 1.10.1](https://golang.org/dl/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰和端點 (其中包含資源名稱)，請在 Azure 入口網站中選取資源的 [快速入門]。

## <a name="create-a-knowledge-base-go-file"></a>建立知識庫 Go 檔案

建立名為 `create-new-knowledge-base.go` 的檔案。

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `create-new-knowledge-base.go` 的頂端，新增下列幾行以將必要的相依性新增至專案：

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>新增必要的常數
在上述必要的相依性後面，新增必要的常數以存取 QnA Maker。

設定下列值：

* `<your-qna-maker-subscription-key>` - **金鑰**是 32 字元字串，且可在 Azure 入口網站中 [快速入門] 頁面上的 [QnA Maker 資源] 上取得。 這與預測端點金鑰不同。
* `{your-resource-name}` - 您的**資源名稱**用來架構可供撰寫的撰寫端點 URL，其格式為 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。 這與用於查詢預測端點的 URL 不同。

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>新增 KB 模型定義
在常數後面新增下列 KB 模型定義。 模型會在定義之後轉換成字串。

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>新增支援結構和函式

接下來，新增下列支援的函式。

1. 新增 HTTP 要求的結構：

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. 新增下列方法，以處理 QnA Maker API 的 POST。 在本快速入門中，POST 可用來將 KB 定義傳送給 QnA Maker。

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. 新增下列方法，以處理 QnA Maker API 的 GET。 在本快速入門中，GET 可用來檢查建立作業的狀態。

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>新增函式以建立 KB

新增下列函式，來提出要建立知識庫的 HTTP POST 要求。 _建立_**作業識別碼**會在 POST 回應標頭欄位 [位置] 中傳回，然後在 GET 要求中作為路由的一部分。 `Ocp-Apim-Subscription-Key` 是 QnA Maker 服務金鑰，用於驗證。

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

此 API 呼叫會傳回包含作業識別碼的 JSON 回應。 使用作業識別碼來判斷是否已成功建立 KB。

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-get-status"></a>新增函式來取得狀態

新增下列函式來提出 HTTP GET 要求，以檢查作業狀態。 `Ocp-Apim-Subscription-Key` 是 QnA Maker 服務金鑰，用於驗證。

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

重複呼叫直到成功或失敗：

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```
## <a name="add-main-function"></a>新增 main 函式

下列函式是主要函式，並且會建立 KB 並重複檢查狀態。 因為 KB 建立可能需要一些時間，您必須重複呼叫來檢查狀態，直到狀態為成功或失敗。

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>編譯程式
輸入下列命令來編譯檔案。 命令提示字元不會傳回成功組建的任何資訊。

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>執行程式

在命令列中輸入下列命令，以執行程式。 程式會將要求傳送至 QnA Maker API 以建立 KB，然後會每隔 30 秒輪詢結果。 每個回應都會列印到主控台視窗。

```bash
go run create-new-knowledge-base
```

一旦建立您的知識庫，您可以在 QnA Maker 入口網站的[我的知識庫](https://www.qnamaker.ai/Home/MyServices) (英文) 頁面中檢視。

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)