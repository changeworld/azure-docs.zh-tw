---
title: '快速入門：發佈知識庫、REST、c #-QnA Maker'
description: 這個以 C# REST 為基礎的快速入門會發佈您的知識庫，並建立可在應用程式或聊天機器人中呼叫的端點。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: abaccfb1a1f36e1f7f47d3f5acefb08e2091cb39
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351175"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>快速入門：使用 C# 在 QnA Maker 中發佈知識庫

本快速入門以 REST 為基礎，會逐步引導您以程式設計方式發佈知識庫 (KB)。 發佈作業會將最新版的知識庫推送到專用的 Azure 認知搜尋索引，並建立可在應用程式或聊天機器人中呼叫的端點。

本快速入門會呼叫 QnA Maker API：
* [發佈](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 此 API 不需要在要求主體中有任何資訊。

## <a name="prerequisites"></a>必要條件

* 最新 [**Visual Studio Community 版本**](https://www.visualstudio.com/downloads/)。
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰和端點 (其中包含資源名稱)，請在 Azure 入口網站中選取資源的 [快速入門]。
* QnA Maker 知識庫 (KB) 識別碼可以在 `kbid` 查詢字串參數中的 URL 找到，如下所示。

    ![QnA Maker 知識庫識別碼](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果您還沒有知識庫，可以建立要用於本快速入門的範例知識庫：[建立新的知識庫](create-new-kb-csharp.md)。

> [!NOTE]
> 您可以從 [ **Azure 範例/認知服務-qnamaker-csharp** GitHub 儲存](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)機制取得完整的解決方案檔 (s) 。

## <a name="create-knowledge-base-project"></a>建立知識庫專案

1. 開啟 Visual Studio 2019 Community 版本。
1. 建立新的 **主控台應用程式 (.NET Core)** 專案，並且將專案命名為 `QnaMakerQuickstart`。 接受其餘設定的預設值。

## <a name="add-required-dependencies"></a>新增必要的相依性

在 Program.cs 的頂端，以下列幾行取代單一 using 陳述式，以將必要的相依性新增至專案：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="dependencies":::

## <a name="add-required-constants"></a>新增必要的常數

在 [程式] 類別中，新增必要常數以存取 QnA Maker。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="constants":::

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>新增 Main 方法以發佈知識庫

在必要常數之後，新增下列程式碼來對 QnA Maker API 提出 HTTPS 要求，以便發佈知識庫並接收回應：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="post":::

發佈成功時，API 呼叫會傳回 204 狀態，且回應主體中沒有任何內容。

## <a name="build-and-run-the-program"></a>建置並執行程式

建置並執行程式。 程式會自動將要求傳送至 QnA Maker API 以發佈知識庫，然後在主控台視窗中輸出回應。

發佈知識庫之後，您就可以從具有用戶端應用程式或聊天機器人的端點來加以查詢。

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

在發佈知識庫之後，您需要有[用來產生答案的端點 URL](./get-answer-from-knowledge-base-csharp.md)。

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)