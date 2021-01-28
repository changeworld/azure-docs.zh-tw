---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a895af0f1a10796ad49898e37551f37d38418784
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948263"
---
## <a name="prerequisites"></a>必要條件

開始之前：

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java" target="_blank">為您的開發環境安裝語音 SDK 並建立空白範例專案<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="create-a-luis-app-for-intent-recognition"></a>建立意圖辨識的 LUIS 應用程式

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>開啟您的專案

1. 開啟您慣用的 IDE。
2. 載入您的專案，並開啟 `Main.java`。

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>建立語音設定

您必須先建立使用 LUIS 預測資源金鑰和位置的組態，才能夠初始化 `IntentRecognizer` 物件。  

將此程式碼插入 `main()` 的 Try / Catch 區塊中。 請務必更新這些值：

* 將 `"YourLanguageUnderstandingSubscriptionKey"` 取代為您的 LUIS 預測金鑰。
* 將 `"YourLanguageUnderstandingServiceRegion"` 取代為您的 LUIS 位置。 使用[區域](../../../../regions.md)中的 [區域識別碼]  。

>[!TIP]
> 如果您在尋找這些值時需要協助，請參閱[建立意圖辨識的 LUIS 應用程式](#create-a-luis-app-for-intent-recognition)。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

此範例會使用 `FromSubscription()` 方法來建置 `SpeechConfig`。 如需可用方法的完整清單，請參閱 [SpeechConfig 類別](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) \(英文\)。

語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

## <a name="initialize-an-intentrecognizer"></a>初始化 IntentRecognizer

現在，我們將建立一個 `IntentRecognizer`。 將此程式碼插入您的語音設定的下方。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>新增 LanguageUnderstandingModel 和意圖

您必須將 `LanguageUnderstandingModel` 與意圖辨識器建立關聯，並新增您要辨識的意圖。 我們將使用預先建置之網域中的意圖來進行家庭自動化。

在 `IntentRecognizer` 下方插入此程式碼。 請務必將 `"YourLanguageUnderstandingAppId"` 取代為您的 LUIS 應用程式識別碼。

>[!TIP]
> 如果您在尋找此值時需要協助，請參閱[建立意圖辨識的 LUIS 應用程式](#create-a-luis-app-for-intent-recognition)。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

這個範例會使用 `addIntent()` 函式來個別新增意圖。 如果要從模型新增所有意圖，請使用 `addAllIntents(model)` 並傳遞模型。

> [!NOTE]
> 語音 SDK 僅支援 LUIS v2.0 端點。
> 您必須手動修改在範例查詢欄位中找到的 v3.0 端點 URL，以使用 v2.0 URL 模式。
> LUIS v2.0 端點一律會遵循下列其中一種模式：
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>辨識意圖

從 `IntentRecognizer` 物件，您將呼叫 `recognizeOnceAsync()` 方法。 此方法可讓語音服務知道您要傳送單一片語以進行辨識，且一旦識別出該片語即停止辨識語音。

在您的模型下插入此程式碼：

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>顯示辨識結果 (或錯誤)

當語音服務傳回辨識結果時，建議您對其執行一些動作。 為了簡單起見，我們將結果列印到主控台。

在您對 `recognizeOnceAsync()` 的呼叫下插入此程式碼。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>釋放資源

語音資源使用完畢後，請務必釋放它們。 在 Try / Catch 區塊的結尾插入此程式碼：

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>檢查您的程式碼

此時，您的程式碼應會如下所示：

> [!NOTE]
> 我們已在此版本中新增一些註解。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>建置並執行您的應用程式

按 <kbd>F11</kbd> 鍵，或選取 [執行]   > [偵錯]  。
系統將會辨識接下來 15 秒來自您麥克風的語音輸入，並記錄在主控台視窗中。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
