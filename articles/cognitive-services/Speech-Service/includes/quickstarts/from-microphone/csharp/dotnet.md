---
title: 快速入門：從麥克風辨識語音，C# (.NET) - 語音服務
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: c969b5e5daa4c4cfd84695fef70f0a2a5c50ce02
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925554"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * 確定您可以存取麥克風以擷取音訊

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中開啟您的專案

第一個步驟是確定您已在 Visual Studio 中開啟專案。

1. 啟動 Visual Studio 2019。
2. 載入您的專案，並開啟 `Program.cs`。

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。 請注意，您已建立名為 `RecognizeSpeechAsync()` 的非同步方法。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=5-15,43-52)]

## <a name="create-a-speech-configuration"></a>建立語音設定

您必須先建立使用訂用帳戶金鑰和訂用帳戶區域的組態 (選擇[區域](https://aka.ms/speech/sdkregion)中的 [區域識別碼]  )，才可以初始化 `SpeechRecognizer` 物件。 在 `RecognizeSpeechAsync()` 方法中插入此程式碼。

> [!NOTE]
> 此範例會使用 `FromSubscription()` 方法來建置 `SpeechConfig`。 如需可用方法的完整清單，請參閱 [SpeechConfig 類別](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) \(英文\)。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=16)]
> 語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

## <a name="initialize-a-speechrecognizer"></a>初始化 SpeechRecognizer

現在，我們將建立一個 `SpeechRecognizer`。 這個物件是在 using 陳述式內建立的，可確保適當釋放未受控資源。 將此程式碼插入您的語音設定下方的 `RecognizeSpeechAsync()` 方法中。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=17-19,42)]

## <a name="recognize-a-phrase"></a>辨識片語

從 `SpeechRecognizer` 物件，您將呼叫 `RecognizeOnceAsync()` 方法。 此方法可讓語音服務知道您要傳送單一片語以進行辨識，且一旦識別出該片語即停止辨識語音。

在 using 陳述式中，新增下列程式碼。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=20)]

## <a name="display-the-recognition-results-or-errors"></a>顯示辨識結果 (或錯誤)

當語音服務傳回辨識結果時，建議您對其執行一些動作。 為了簡單起見，我們將結果列印到主控台。

在 using 陳述式中的 `RecognizeOnceAsync()` 下方，新增此程式碼。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=22-41)]

## <a name="check-your-code"></a>檢查您的程式碼

此時，您的程式碼應會如下所示。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs)]

## <a name="build-and-run-your-app"></a>建置並執行您的應用程式

現在您已準備好使用語音服務來建立應用程式，並測試我們的語音辨識。

1. **編譯程式碼** - 從 Visual Studio 的功能表列中，選擇 [建置]   > [建置解決方案]  。
2. **啟動應用程式** - 從功能表列中，選擇 [偵錯]   > [開始偵錯]  ，或按 **F5**。
3. **開始辨識** - 它會提示您說英文片語。 您的語音會傳送至語音服務、轉譯為文字，並在主控台中轉譯。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
