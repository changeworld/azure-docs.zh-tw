---
title: 快速入門：發佈知識庫、REST、Go QnA Maker
description: 這個以 Go REST 為基礎的快速入門會發佈您的知識庫，並建立可在應用程式或聊天機器人中呼叫的端點。
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: b94b09fcb3bfff2eeacabaaa49eb5e4c751ec79d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267746"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>快速入門：使用 Go 在 QnA Maker 中發佈知識庫

本快速入門以 REST 為基礎，會逐步引導您以程式設計方式發佈知識庫 (KB)。 發佈作業會將最新版的知識庫推送到專用的 Azure 認知搜尋索引，並建立可在應用程式或聊天機器人中呼叫的端點。

本快速入門會呼叫 QnA Maker API：
* [發佈](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 此 API 不需要在要求主體中有任何資訊。

## <a name="prerequisites"></a>先決條件

* [Go 1.10.1](https://golang.org/dl/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰和端點 (其中包含資源名稱)，請在 Azure 入口網站中選取資源的 [快速入門]****。

* QnA Maker 知識庫 (KB) 識別碼可以在 `kbid` 查詢字串參數中的 URL 找到，如下所示。

    ![QnA Maker 知識庫識別碼](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果您還沒有知識庫，可以建立要用於本快速入門的範例知識庫：[建立新的知識庫](create-new-kb-csharp.md)。

> [!NOTE]
> 您可以從[ **Azure 範例/認知服務-qnamaker-go** GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base)取得完整的解決方案檔 (s) 。

## <a name="create-a-go-file"></a>建立 Go 檔案

開啟 VSCode 並建立名為 `publish-kb.go` 的新檔案。

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `publish-kb.go` 的頂端，新增下列幾行以將必要的相依性新增至專案：

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="dependencies":::

## <a name="create-the-main-function"></a>建立 main 函式

在必要的相依性之後新增下列類別：

```Go
package main

func main() {

}
```

## <a name="add-post-request-to-publish-kb"></a>新增 POST 要求來發佈知識庫

新增下列程式碼，此程式碼會對 QnA Maker API 提出 HTTPS 要求，以發佈知識庫並接收回應：

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="main":::

發佈成功時，API 呼叫會傳回 204 狀態，且回應主體中沒有任何內容。 程式碼會針對 204 回應新增內容。

對於任何其他回應，該回應則會原封不動地傳回。

## <a name="build-and-run-the-program"></a>建置並執行程式

輸入下列命令來編譯檔案。 命令提示字元不會傳回成功組建的任何資訊。

```bash
go build publish-kb.go
```

在命令列中輸入下列命令，以執行程式。 它會傳送要求給 QnA Maker API 來發佈知識庫，然後印出代表成功的 204 或錯誤。

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

在發佈知識庫之後，您需要有[用來產生答案的端點 URL](./get-answer-from-knowledge-base-go.md)。

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)
