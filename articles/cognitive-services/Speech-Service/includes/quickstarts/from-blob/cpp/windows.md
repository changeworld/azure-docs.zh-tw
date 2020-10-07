---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: e1d2493474736ebbcdb54aeb697716e1dc7d6ee6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377619"
---
## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programmming-language-cpp)
> * [建立 Azure 語音資源](../../../../overview.md#try-the-speech-service-for-free)
> * [將原始程式檔上傳至 Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中開啟您的專案

第一個步驟是確定您已在 Visual Studio 中開啟專案。

1. 啟動 Visual Studio 2019。
2. 載入您的專案，並開啟 `helloworld.cpp`。

## <a name="add-a-references"></a>新增參考

為了加速程式碼開發，我們將使用幾個外部元件：
* [CPP Rest SDK](https://github.com/microsoft/cpprestsdk)，此為用來對 REST 服務進行 REST 呼叫的用戶端程式庫。
* [nlohmann/json](https://github.com/nlohmann/json)，此為方便使用的 JSON 剖析/序列化/還原序列化程式庫。

這兩者皆可以使用 [vcpkg](https://github.com/Microsoft/vcpkg/) \(英文\) 安裝。

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON 包裝函式

由於 REST 的 API 會取得 JSON 格式的要求，而且也會以 JSON 格式傳回結果，因此我們只能使用字串與它們互動，但並不建議這麼做。
為了能更容易管理要求和回應，我們將宣告幾個類別以用來序列化/還原序列化 JSON 和一些方法，以協助 nlohmann/json。

請繼續進行，並將其宣告放在 `recognizeSpeech` 之前。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>建立與設定 HTTP 用戶端
我們需要的第一件事是具有正確基底 URL 和驗證集的 HTTP 用戶端。
在 `recognizeSpeech` 中插入此程式碼

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>產生轉譯要求
接下來，我們將產生轉譯要求。 將此程式碼新增至 `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>傳送要求並檢查其狀態
現在，我們會將要求張貼到語音服務，並檢查初始的回應碼。 此回應碼只會指出服務是否已收到要求。 服務將會在回應標頭中傳回 URL，這是儲存轉譯狀態的位置。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>請等候轉譯完成
因為服務會以非同步方式處理轉譯，所以我們每隔一段時間就需要輪詢其狀態。 我們會每隔 5 秒檢查一次。

我們可以藉由在張貼要求時，從我們所取得的 URL 處擷取內容來檢查狀態。 當我們取回內容時，會將它還原序列化為我們其中一個協助程式類別，讓互動變得更容易。

以下為輪詢程式碼，其顯示除了成功完成之外所有一切的狀態，這是我們接下來將執行的作業。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>顯示轉譯結果
一旦服務成功完成轉譯，系統會將轉譯結果儲存在另一個我們可以從狀態回應取得的 URL 中。

我們將下載該 URL 的內容、將 JSON 還原序列化，並在結果中執行迴圈，以列印出顯示文字。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>檢查您的程式碼
此時，您的程式碼應會如下所示：(我們已在此版本中新增一些註解)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>建置並執行您的應用程式

現在您已準備好使用語音服務來建立應用程式，並測試我們的語音辨識。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
