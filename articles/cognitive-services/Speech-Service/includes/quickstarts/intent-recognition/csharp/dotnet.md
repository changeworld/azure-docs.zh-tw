---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c23122024533871dcdb374fa0c21a82faf670f85
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156489"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

* 如果這是您的第一個 C# 專案，請使用本指南<a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=dotnet" target="_blank">建立空的範例專案<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet" target="_blank">為您的開發環境安裝語音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="create-a-luis-app-for-intent-recognition"></a>建立意圖辨識的 LUIS 應用程式

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中開啟您的專案

然後，在 Visual Studio 中開啟專案。

1. 啟動 Visual Studio 2019。
2. 載入您的專案，並開啟 `Program.cs`。

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。 請注意，您已建立名為 `RecognizeIntentAsync()` 的非同步方法。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>建立語音設定

您必須先建立使用 LUIS 預測資源金鑰和位置的組態，才能夠初始化 `IntentRecognizer` 物件。

> [!IMPORTANT]
> 您的入門金鑰和撰寫金鑰將無法使用。 您必須使用您先前建立的預測金鑰和位置。 如需詳細資訊，請參閱[建立意圖辨識的 LUIS 應用程式](#create-a-luis-app-for-intent-recognition)。

在 `RecognizeIntentAsync()` 方法中插入此程式碼。 請務必更新這些值：

* 將 `"YourLanguageUnderstandingSubscriptionKey"` 取代為您的 LUIS 預測金鑰。
* 將 `"YourLanguageUnderstandingServiceRegion"` 取代為您的 LUIS 位置。 使用[區域](https://aka.ms/speech/sdkregion)中的「語音 SDK 參數」。

>[!TIP]
> 如果您在尋找這些值時需要協助，請參閱[建立意圖辨識的 LUIS 應用程式](#create-a-luis-app-for-intent-recognition)。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

此範例會使用 `FromSubscription()` 方法來建置 `SpeechConfig`。 如需可用方法的完整清單，請參閱 [SpeechConfig 類別](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) \(英文\)。

語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

## <a name="initialize-an-intentrecognizer"></a>初始化 IntentRecognizer

現在，我們將建立一個 `IntentRecognizer`。 這個物件是在 using 陳述式內建立的，可確保能夠適當釋放非受控資源。 將此程式碼插入您的語音設定下方的 `RecognizeIntentAsync()` 方法中。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>新增 LanguageUnderstandingModel 和意圖

您必須將 `LanguageUnderstandingModel` 與意圖辨識器建立關聯，並新增您要辨識的意圖。 我們將使用預先建置之網域中的意圖來進行家庭自動化。 在上一節的 using 陳述式中插入此程式碼。 請務必將 `"YourLanguageUnderstandingAppId"` 取代為您的 LUIS 應用程式識別碼。

>[!TIP]
> 如果您在尋找此值時需要協助，請參閱[建立意圖辨識的 LUIS 應用程式](#create-a-luis-app-for-intent-recognition)。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

## <a name="recognize-an-intent"></a>辨識意圖

從 `IntentRecognizer` 物件，您將呼叫 `RecognizeOnceAsync()` 方法。 此方法可讓語音服務知道您要傳送單一片語以進行辨識，且一旦識別出該片語即停止辨識語音。

在 using 陳述式中，將下列程式碼新增至您的模型下方：[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>顯示辨識結果 (或錯誤)

當語音服務傳回辨識結果時，建議您對其執行一些動作。 為了簡單起見，我們將結果列印到主控台。

在 using 陳述式中的 `RecognizeOnceAsync()` 下方，新增此程式碼：

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>檢查您的程式碼

此時，您的程式碼應會如下所示：

> [!NOTE]
> 我們已在此版本中新增一些註解。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>建置並執行您的應用程式

現在您已準備好使用語音服務來建立應用程式，並測試我們的語音辨識。

1. **編譯程式碼** - 從 Visual Studio 的功能表列中，選擇 [建置]   > [建置解決方案]  。
2. **啟動應用程式** - 從功能表列中，選擇 [偵錯]   > [開始偵錯]  ，或按 <kbd>F5</kbd>。
3. **開始辨識** - 它會提示您說英文片語。 您的語音會傳送至語音服務、轉譯為文字，並在主控台中轉譯。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
