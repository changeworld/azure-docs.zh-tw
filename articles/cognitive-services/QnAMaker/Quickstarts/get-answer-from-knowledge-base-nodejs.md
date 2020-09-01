---
title: 快速入門：從知識庫取得答案 - REST (Node.js) - QnA Maker
description: 這個以 Node.js REST 為基礎的快速入門會逐步引導您以程式設計方式從知識庫取得答案。
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: aa5a6395b537daa750a2e9b97bde4df320193c1e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268052"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>快速入門：使用 Node.js 從知識庫取得問題的解答

本快速入門會逐步引導您以程式設計方式從已發佈的 QnA Maker 知識庫取得答案。 知識庫包含[資料來源](../Concepts/knowledge-base.md)中的問題和答案，例如常見問題集。 [問題](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)會傳送至 QnA Maker 服務。 [回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含位居預測首位的答案。

[參考文件](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/get-answer/get-answer.js)

## <a name="prerequisites"></a>必要條件

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰，請在 QnA Maker 資源的 Azure 儀表板中選取 [資源管理]**** 下方的 [金鑰]****。
* [發佈]**** 頁面設定。 如果您沒有已發佈的知識庫，請建立空白的知識庫，在 [設定]**** 頁面上匯入知識庫，然後發佈。 您可以下載並使用[這個基本知識庫](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。

    發佈頁面設定包括 POST 路由值、主機值以及 EndpointKey 值。

    ![發佈設定](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-nodejs-file"></a>建立 Node.js 檔案

開啟 VSCode 並建立名為 `get-answer.js` 的新檔案。

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `get-answer.js` 檔案的頂端，將必要的相依性新增至專案：

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/query-kb.js" id="dependencies":::

## <a name="add-the-required-constants"></a>新增必要的常數

接下來，新增必要常數以存取 QnA Maker。 在您發佈知識庫之後，這些值都位於 [發佈]**** 頁面上。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/query-kb.js" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>新增 POST 要求以傳送問題和取得答案

下列程式碼會對 QnA Maker API 提出 HTTPS 要求，以將問題傳送到知識庫並接收回應：

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/query-kb.js" id="query":::

## <a name="run-the-program"></a>執行程式

從命令列執行程式。 程式會自動將要求傳送至 QnA Maker API，然後在主控台視窗中輸出。

執行檔案：

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

深入了解[要求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)
