---
title: 快速入門：發行知識庫，REST (Java) - QnA Maker
titleSuffix: Azure Cognitive Services
description: 這個以 Java REST 為基礎的快速入門會發佈您的知識庫，並建立可在應用程式或聊天機器人中呼叫的端點。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 48da92374a29b5319f0ae5eaa830f2e03511b512
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109481"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>快速入門：使用 Java 在 QnA Maker 中發佈知識庫

本快速入門以 REST 為基礎，會逐步引導您以程式設計方式發佈知識庫 (KB)。 發佈作業會將最新版的知識庫推送到專用的 Azure 認知搜尋索引，並建立可在應用程式或聊天機器人中呼叫的端點。

本快速入門會呼叫 QnA Maker API：
* [發佈](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 此 API 不需要在要求主體中有任何資訊。

## <a name="prerequisites"></a>Prerequisites

* [JDK SE](https://aka.ms/azure-jdks) (英文) (Java 開發套件，標準版)
* 這個範例會使用 HTTP 元件中的 [Apache HTTP 用戶端](https://hc.apache.org/httpcomponents-client-ga/)。 您必須在專案中新增下列 Apache HTTP 用戶端程式庫：
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰和端點 (其中包含資源名稱)，請在 Azure 入口網站中選取資源的 [快速入門]  。
* QnA Maker 知識庫 (KB) 識別碼可以在 `kbid` 查詢字串參數中的 URL 找到，如下所示。

    ![QnA Maker 知識庫識別碼](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果您還沒有知識庫，可以建立要用於本快速入門的範例知識庫：[建立新的知識庫](create-new-kb-csharp.md)。

> [!NOTE]
> 從 [**Azure-Samples/cognitive-services-qnamaker-java** GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base)可取得完整的解決方案檔。

## <a name="create-a-java-file"></a>建立 Java 檔案

開啟 VSCode 並建立名為 `PublishKB.java` 的新檔案。

## <a name="add-the-required-dependencies"></a>新增必要的相依性

在 `PublishKB.java` 頂端的類別上方，新增下列幾行以將必要的相依性新增至專案：

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>使用 main 方法來建立 PublishKB 類別

在相依性之後新增下列類別：

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>新增必要的常數

在 **main** 方法中，新增必要常數以存取 QnA Maker。 使用您自己的值加以取代。

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>新增 POST 要求來發佈知識庫

在必要常數之後，新增下列程式碼來對 QnA Maker API 提出 HTTPS 要求，以便發佈知識庫並接收回應：

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

發佈成功時，API 呼叫會傳回 204 狀態，且回應主體中沒有任何內容。 程式碼會針對 204 回應新增內容。

對於任何其他回應，該回應則會原封不動地傳回。

## <a name="build-and-run-the-program"></a>建置並執行程式

從命令列建置並執行程式。 程式會自動將要求傳送至 QnA Maker API，然後在主控台視窗中輸出。

1. 建立檔案：

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. 執行檔案：

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

在發佈知識庫之後，您需要有[用來產生答案的端點 URL](./get-answer-from-knowledge-base-java.md)。

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)
