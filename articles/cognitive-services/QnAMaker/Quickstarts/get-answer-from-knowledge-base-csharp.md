---
title: 快速入門：從知識庫取得答案 - REST (C#) - QnA Maker
description: 這個以 C# REST 為基礎的快速入門會逐步引導您以程式設計方式從知識庫取得答案。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: 0e41bc819f63a54f37087f71a50b11088f4578ae
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346043"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>快速入門：使用 C 從知識庫取得問題的解答#

本快速入門會逐步引導您以程式設計方式從已發佈的 QnA Maker 知識庫取得答案。 知識庫包含[資料來源](../index.yml)中的問題和答案，例如常見問題集。 [問題](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)會傳送至 QnA Maker 服務。 [回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含位居預測首位的答案。

[參考文件](/rest/api/cognitiveservices/qnamaker4.0/Runtime) | [範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>必要條件

* 最新 [**Visual Studio Community 版本**](https://www.visualstudio.com/downloads/)。
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰，請在 QnA Maker 資源的 Azure 儀表板中選取 [資源管理] 下方的 [金鑰]。
* [發佈] 頁面設定。 如果您沒有已發佈的知識庫，請建立空白的知識庫，在 [設定] 頁面上匯入知識庫，然後發佈。 您可以下載並使用[這個基本知識庫](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。

    發佈頁面設定包括 POST 路由值、主機值以及 EndpointKey 值。

    ![發佈設定](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>建立知識庫專案

1. 開啟 Visual Studio 2019 Community 版本。
1. 建立新的主控台應用程式 (.NET Core) 專案，並將專案命名為 QnaMakerQuickstart。 接受其餘設定的預設值。

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 Program.cs 檔案的頂端，以下列幾行取代單一 using 陳述式，以將必要的相依性新增至專案：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>新增必要的常數

在 `Main` 內部的 `Program` 類別頂端，新增必要常數以存取 QnA Maker。 在您發佈知識庫之後，這些值都位於 [發佈] 頁面上。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>新增 POST 要求以傳送問題和取得答案

下列程式碼會對 QnA Maker API 提出 HTTPS 要求，以將問題傳送到知識庫並接收回應：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="post":::

`Authorization` 標頭的值包含字串 `EndpointKey`。

深入了解[要求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

## <a name="build-and-run-the-program"></a>建置並執行程式

從 Visual Studio 建置並執行程式。 程式會自動將要求傳送至 QnA Maker API，然後在主控台視窗中輸出。

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)