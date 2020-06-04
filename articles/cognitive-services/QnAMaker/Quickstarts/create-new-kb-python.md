---
title: 快速入門：建立知識庫 - REST (Python) - QnA Maker
description: 這個以 Python REST 為基礎的快速入門會逐步引導您以程式設計方式建立範例 QnA Maker 知識庫，且該知識庫會出現在認知服務 API 帳戶的 Azure 儀表板中。
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: 31763a10ea365e9879557dcd6dcec65c9943f9db
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343450"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>快速入門：使用 Python 在 QnA Maker 中建立知識庫

本快速入門會逐步引導您以程式設計方式建立及發佈範例 QnA Maker 知識庫。 QnA Maker 會從[資料來源](../Concepts/knowledge-base.md)中的半結構化內容 (如常見問題集) 自動擷取問題和答案。 知識庫的模型是在 JSON (在 API 要求的本體中傳送) 中定義的。

本快速入門會呼叫 QnA Maker API：
* [建立知識庫](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [取得作業詳細資料](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[參考檔](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  | [Python 範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必要條件

* [Python 3.7](https://www.python.org/downloads/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰和端點 (其中包含資源名稱)，請在 Azure 入口網站中選取資源的 [快速入門]****。

## <a name="create-a-knowledge-base-python-file"></a>建立知識庫 Python 檔案

建立名為 `create-new-knowledge-base-3x.py` 的檔案。

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `create-new-knowledge-base-3x.py` 的頂端，新增下列幾行以將必要的相依性新增至專案：

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>新增必要的常數
在上述必要的相依性後面，新增必要的常數以存取 QnA Maker。 將 `<your-qna-maker-subscription-key>` 和 `<your-resource-name>` 的值取代為您自己的 QnA Maker 金鑰和資源名稱。

在 Program 類別的頂端，新增必要常數以存取 QnA Maker。

設定下列值：

* `<your-qna-maker-subscription-key>` - **金鑰**是 32 字元字串，且可在 Azure 入口網站中 [快速入門] 頁面上的 [QnA Maker 資源] 上取得。 這與預測端點金鑰不同。
* `<your-resource-name>` - 您的**資源名稱**用來架構可供撰寫的撰寫端點 URL，其格式為 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。 這與用於查詢預測端點的 URL 不同。

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=5-13 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>新增 KB 模型定義

在常數後面新增下列 KB 模型定義。 模型會在定義之後轉換成字串。

[!code-python[Add the KB model definition](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=15-41 "Add the KB model definition")]

## <a name="add-supporting-function"></a>新增支援的函式

新增下列函式，以可讀取的格式列印出 JSON：

[!code-python[Add supporting function](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=43-45 "Add supporting function")]

## <a name="add-function-to-create-kb"></a>新增函式以建立 KB

新增下列函式，以提出建立知識庫的 HTTP POST 要求。
此 API 呼叫會傳回 JSON 回應，其中包含標頭欄位 [位置]**** 的作業識別碼。 使用作業識別碼來判斷是否已成功建立 KB。 `Ocp-Apim-Subscription-Key` 是 QnA Maker 服務金鑰，用於驗證。

[!code-python[Add function to create KB](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=48-59 "Add function to create KB")]

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

## <a name="add-function-to-check-creation-status"></a>新增函式以檢查建立狀態

下列函式會檢查在 URL 路由結束時在作業識別碼中傳送的建立狀態。 對 `check_status` 的呼叫會位於主要 _while_ 迴圈內。

[!code-python[Add function to check creation status](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=61-67 "Add function to check creation status")]

此 API 呼叫會傳回包含作業狀態的 JSON 回應：

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

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

## <a name="add-main-code-block"></a>新增主要程式碼區塊
下列迴圈會在建立作業完成之前定期輪詢該作業的狀態。

[!code-python[Add main code block](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=70-96 "Add main code block")]

## <a name="build-and-run-the-program"></a>建置並執行程式

在命令列中輸入下列命令，以執行程式。 程式會將要求傳送至 QnA Maker API 以建立 KB，然後會每隔 30 秒輪詢結果。 每個回應都會列印到主控台視窗。

```bash
python create-new-knowledge-base-3x.py
```

一旦建立您的知識庫，您可以在 QnA Maker 入口網站的[我的知識庫](https://www.qnamaker.ai/Home/MyServices) (英文) 頁面中檢視。 選取您的知識庫名稱 (例如 QnA Maker 常見問題集) 以進行檢視。

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)