---
title: 快速入門：從知識庫取得答案 - REST (Java) - QnA Maker
description: 這個以 Java REST 為基礎的快速入門會逐步引導您以程式設計方式從知識庫取得答案。
ms.topic: quickstart
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCHANGE-20200128
ms.openlocfilehash: 96b8b870dc82bfb08aca2a1d1acb2958ff7eb2f3
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109889"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>快速入門：使用 Java 從知識庫取得答案

本快速入門會逐步引導您以程式設計方式從已發佈的 QnA Maker 知識庫取得答案。 知識庫包含[資料來源](../Concepts/knowledge-base.md)中的問題和答案，例如常見問題集。 [問題](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)會傳送至 QnA Maker 服務。 [回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含位居預測首位的答案。

[參考文件](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [範例](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Prerequisites

* [JDK SE](https://aka.ms/azure-jdks) (英文) (Java 開發套件，標準版)
* 這個範例會使用 HTTP 元件中的 [Apache HTTP 用戶端](https://hc.apache.org/httpcomponents-client-ga/)。 您必須在專案中新增下列 Apache HTTP 用戶端程式庫：
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* 您必須有 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)。 若要擷取您的金鑰，請在 QnA Maker 資源的 Azure 儀表板中選取 [資源管理]  下方的 [金鑰]  。
* [發佈]  頁面設定。 如果您沒有已發佈的知識庫，請建立空白的知識庫，在 [設定]  頁面上匯入知識庫，然後發佈。 您可以下載並使用[這個基本知識庫](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。

    發佈頁面設定包括 POST 路由值、主機值以及 EndpointKey 值。

    ![發佈設定](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>建立 Java 檔案

開啟 VSCode 並建立名為 `GetAnswer.java` 的新檔案，然後新增下列類別：

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>新增必要的相依性

本快速入門會使用 HTTP 要求的 Apache 類別。 在 `GetAnswer.java` 檔案頂端的 GetAnswer 類別上方，將必要的相依性新增至專案：

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>新增必要的常數

在 `GetAnswer.java` 類別的頂端，新增必要常數以存取 QnA Maker。 在您發佈知識庫之後，這些值都位於 [發佈]  頁面上。

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>新增 POST 要求以傳送問題

下列程式碼會對 QnA Maker API 提出 HTTPS 要求，以將問題傳送到知識庫並接收回應：

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

`Authorization` 標頭的值包含字串 `EndpointKey`。

深入了解[要求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[回應](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

## <a name="build-and-run-the-program"></a>建置並執行程式

從命令列建置並執行程式。 程式會自動將要求傳送至 QnA Maker API，然後在主控台視窗中輸出。

1. 建立檔案：

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. 執行檔案：

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)
