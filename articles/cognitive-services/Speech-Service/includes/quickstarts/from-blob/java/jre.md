---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 02e0e1494a897b31cb6ef28083677fa48f854c91
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925916"
---
## <a name="prerequisites"></a>Prerequisites

開始之前，請務必：

> [!div class="checklist"]
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [將原始程式檔上傳至 Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)


## <a name="open-your-project-in-eclipse"></a>以 Eclipse 開啟您的專案

第一個步驟是確定您已在 Eclipse 中開啟專案。

1. 啟動 Eclipse
2. 載入您的專案，並開啟 `Main.java`。

## <a name="add-a-reference-to-gson"></a>新增對 Gson 的參考
我們將在本快速入門中使用外部 JSON 序列化程式/還原序列化程式。 針對 Java，我們選擇 [Gson](https://github.com/google/gson) \(英文\)。

開啟您的 pom.xml，並新增下列參考。

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON 包裝函式

由於 REST 的 API 會取得 JSON 格式的要求，而且也會以 JSON 格式傳回結果，因此我們只能使用字串與它們互動，但並不建議這麼做。
為了能更容易管理要求和回應，我們將宣告幾個類別以用來序列化/還原序列化 JSON。

請繼續進行，並將其宣告放在 `Main` 之前。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>建立與設定 HTTP 用戶端
我們需要的第一件事是具有正確基底 URL 和驗證集的 HTTP 用戶端。
在 `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)] 中插入此程式碼

## <a name="generate-a-transcription-request"></a>產生轉譯要求
接下來，我們將產生轉譯要求。 將此程式碼新增至 `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>傳送要求並檢查其狀態
現在，我們會將要求張貼到語音服務，並檢查初始的回應碼。 此回應碼只會指出服務是否已收到要求。 服務將會在回應標頭中傳回 URL，這是儲存轉譯狀態的位置。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>請等候轉譯完成
因為服務會以非同步方式處理轉譯，所以我們每隔一段時間就需要輪詢其狀態。 我們會每隔 5 秒檢查一次。

我們可以藉由在張貼要求時，從我們所取得的 URL 處擷取內容來檢查狀態。 當我們取回內容時，會將它還原序列化為我們其中一個協助程式類別，讓互動變得更容易。

以下為輪詢程式碼，其顯示除了成功完成之外所有一切的狀態，這是我們接下來將執行的作業。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>顯示轉譯結果
一旦服務成功完成轉譯，系統會將轉譯結果儲存在另一個我們可以從狀態回應取得的 URL 中。

我們將下載該 URL 的內容、將 JSON 還原序列化，並在結果中執行迴圈，以列印出顯示文字。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>檢查您的程式碼
此時，您的程式碼應會如下所示：(我們已在此版本中新增一些註解) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>建置並執行您的應用程式

現在您已準備好使用語音服務來建立應用程式，並測試我們的語音辨識。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
