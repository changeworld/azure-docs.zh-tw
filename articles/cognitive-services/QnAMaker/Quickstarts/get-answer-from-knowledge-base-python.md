---
title: 快速入門：從知識庫取得答案 - REST (Python) - QnA Maker
description: 這個以 Python REST 為基礎的快速入門會逐步引導您以程式設計方式從知識庫取得答案。
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 645ad24b2b275f9a14fda82746347a6f64c735dc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268001"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>快速入門：使用 Python 從知識庫取得問題的解答

本快速入門會逐步引導您以程式設計方式從已發佈的 QnA Maker 知識庫取得答案。 知識庫包含[資料來源](../Concepts/knowledge-base.md)中的問題和答案，例如常見問題集。 [問題](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)會傳送至 QnA Maker 服務。 [回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含位居預測首位的答案。

[參考文件](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py)

## <a name="prerequisites"></a>必要條件

* [Python 3.6 或更新版本](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰，請在 QnA Maker 資源的 Azure 儀表板中選取 [資源管理]**** 下方的 [金鑰]****。
* [發佈]**** 頁面設定。 如果您沒有已發佈的知識庫，請建立空白的知識庫，在 [設定]**** 頁面上匯入知識庫，然後發佈。 您可以下載並使用[這個基本知識庫](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。

    發佈頁面設定包括 POST 路由值、主機值以及 EndpointKey 值。

    ![發佈設定](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>建立 Python 檔案

開啟 VSCode 並建立名為 `get-answer-3x.py` 的新檔案。

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `get-answer-3x.py` 檔案的頂端，將必要的相依性新增至專案：

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="dependencies":::

<!--TBD - reword this following paragraph -->

主機和路由出現於 [發佈]**** 頁面上的方式有所不同。 這是因為 Python 程式庫不允許主機中的任何路由。 以主機的一部分形式出現於 [發佈]**** 頁面上的路由已移至路由。

## <a name="add-the-required-constants"></a>新增必要的常數

新增必要常數以存取 QnA Maker。 在您發佈知識庫之後，這些值都位於 [發佈]**** 頁面上。

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>新增 POST 要求以傳送問題和取得答案

下列程式碼會對 QnA Maker API 提出 HTTPS 要求，以將問題傳送到知識庫並接收回應：

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="main":::

`Authorization` 標頭的值包含字串 `EndpointKey`。

## <a name="run-the-program"></a>執行程式

從命令列執行程式。 程式會自動將要求傳送至 QnA Maker API，然後在主控台視窗中輸出。

執行檔案：

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

深入了解[要求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)
