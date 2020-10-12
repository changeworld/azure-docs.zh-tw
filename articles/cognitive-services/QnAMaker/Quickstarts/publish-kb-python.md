---
title: 快速入門：發佈知識庫、REST、Python-QnA Maker
description: 這個以 Python REST 為基礎的快速入門會發佈您的知識庫，並建立可在您應用程式或聊天機器人中呼叫的端點。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 5c28eac20b0bf2fab01312223ca2d5daef1d316d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777478"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>快速入門：使用 Python 在 QnA Maker 中發佈知識庫

本快速入門以 REST 為基礎，會逐步引導您以程式設計方式發佈知識庫 (KB)。 發佈作業會將最新版的知識庫推送到專用的 Azure 認知搜尋索引，並建立可在應用程式或聊天機器人中呼叫的端點。

本快速入門會呼叫 QnA Maker REST API：
* [發佈](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 此 API 不需要在要求主體中有任何資訊。

## <a name="prerequisites"></a>必要條件

* [Python 3。7](https://www.python.org/downloads/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰和端點 (其中包含資源名稱)，請在 Azure 入口網站中選取資源的 [快速入門]****。
* QnA Maker 知識庫 (KB) 識別碼可以在 `kbid` 查詢字串參數中的 URL 找到，如下所示。

    ![QnA Maker 知識庫識別碼](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果您還沒有知識庫，可以建立要用於本快速入門的範例知識庫：[建立新的知識庫](../how-to/create-knowledge-base.md)。

> [!NOTE]
> 從 [**Azure-Samples/cognitive-services-qnamaker-python** GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base)可取得完整的解決方案檔。

## <a name="create-a-knowledge-base-python-file"></a>建立知識庫 Python 檔案

建立名為 `publish-kb-3x.py` 的檔案。

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `publish-kb-3x.py` 的頂端，新增下列幾行以將必要的相依性新增至專案：

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="dependencies":::

## <a name="add-required-constants"></a>新增必要的常數

在上述必要的相依性後面，新增必要的常數以存取 QnA Maker。 使用您自己的值加以取代。

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="constants":::

## <a name="add-post-request-to-publish-knowledge-base"></a>新增 POST 要求來發佈知識庫

在必要常數之後，新增下列程式碼來對 QnA Maker API 提出 HTTPS 要求，以便發佈知識庫並接收回應：

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="main":::

發佈成功時，API 呼叫會傳回 204 狀態，且回應主體中沒有任何內容。 程式碼會針對 204 回應新增內容。

對於任何其他回應，該回應則會原封不動地傳回。

## <a name="build-and-run-the-program"></a>建置並執行程式

在命令列中輸入下列命令，以執行程式。 它會傳送要求給 QnA Maker API 來發佈知識庫，然後印出代表成功的 204 或錯誤。

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

在發佈知識庫之後，您需要有[用來產生答案的端點 URL](./get-answer-from-knowledge-base-python.md)。

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)

[QnA Maker 概觀](../Overview/overview.md)
