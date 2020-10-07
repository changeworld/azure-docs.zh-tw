---
title: 快速入門：從知識庫取得答案 - REST (Go) - QnA Maker
description: 這個以 Go REST 為基礎的快速入門會逐步引導您以程式設計方式從知識庫取得答案。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: f826802de2aa979b5af6174fccda2f0203eba433
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777615"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-go"></a>快速入門：使用 Go 從知識庫取得問題的解答

本快速入門會逐步引導您以程式設計方式從已發佈的 QnA Maker 知識庫取得答案。 知識庫包含[資料來源](../Concepts/knowledge-base.md)中的問題和答案，例如常見問題集。 [問題](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)會傳送至 QnA Maker 服務。 [回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含位居預測首位的答案。

[參考文件](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/get-answer/get-answer.go)

## <a name="prerequisites"></a>必要條件

* [Go 1.10.1](https://golang.org/dl/)
* [Visual Studio Code](https://code.visualstudio.com/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰，請在 QnA Maker 資源的 Azure 儀表板中選取 [資源管理]**** 下方的 [金鑰]****。
* [發佈]**** 頁面設定。 如果您沒有已發佈的知識庫，請建立空白的知識庫，在 [設定]**** 頁面上匯入知識庫，然後發佈。 您可以下載並使用[這個基本知識庫](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。

    發佈頁面設定包括 POST 路由值、主機值以及 EndpointKey 值。

    ![發佈設定](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-go-file"></a>建立 Go 檔案

開啟 VSCode 並建立名為 `get-answer.go` 的新檔案，然後新增下列類別：

```Go
package main

func main() {

}
```

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `get-answer.go` 檔案頂端的 `main` 函式上方，將必要的相依性新增至專案：

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/query-kb.go" id="dependencies":::

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>新增 POST 要求以傳送問題和取得答案

下列程式碼會對 QnA Maker API 提出 HTTPS 要求，以將問題傳送到知識庫並接收回應：

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/query-kb.go" id="main":::

`Authorization` 標頭的值包含字串 `EndpointKey`。

深入了解[要求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

## <a name="build-and-run-the-program"></a>建置並執行程式

從命令列建置並執行程式。 程式會自動將要求傳送至 QnA Maker API，然後在主控台視窗中輸出。

1. 建立檔案：

    ```bash
    go build get-answer.go
    ```

1. 執行檔案：

    ```bash
    ./get-answer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)
