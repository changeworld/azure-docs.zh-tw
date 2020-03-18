---
title: 快速入門：從麥克風辨識語音，C# (Xamarin) - 語音服務
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 93a5a29c225912a48cf99ba6126690c298dfef9d
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925069"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * 確定您可以存取麥克風以擷取音訊

如果您已經這麼做，很棒。 讓我們繼續進行。

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中開啟您的專案

第一個步驟是確定您已在 Visual Studio 中開啟專案。

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。

1. 在 [方案總管]  中開啟 `MainPage.xaml`。

2. 在設計工具的 XAML 檢視中，將下列 XAML 程式碼片段插入**格線**標記 (在 `<Grid>` 和 `</Grid>` 之間)：

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. 在 [方案總管]  中，開啟程式碼後置原始檔 `MainPage.xaml.cs`。 (它分組在 `MainPage.xaml` 底下。)

4. 將程式碼取代為下列基底程式碼：

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>建立語音設定

您必須先建立使用訂用帳戶金鑰和訂用帳戶區域的設定，才可以初始化 `SpeechRecognizer` 物件。 在 `RecognizeSpeechAsync()` 方法中插入此程式碼。

> [!NOTE]
> 此範例會使用 `FromSubscription()` 方法來建置 `SpeechConfig`。 如需可用方法的完整清單，請參閱 [SpeechConfig 類別](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)] \(英文\)

## <a name="initialize-a-speechrecognizer"></a>初始化 SpeechRecognizer

現在，我們將建立一個 `SpeechRecognizer`。 這個物件是在 using 陳述式內建立的，可確保適當釋放未受控資源。 將此程式碼插入您的語音設定下方的 `RecognizeSpeechAsync()` 方法中。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>辨識片語

從 `SpeechRecognizer` 物件，您將呼叫 `RecognizeOnceAsync()` 方法。 此方法可讓語音服務知道您要傳送單一片語以進行辨識，且一旦識別出該片語即停止辨識語音。

在 using 陳述式中，新增下列程式碼。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>顯示辨識結果 (或錯誤)

當語音服務傳回辨識結果時，建議您對其執行一些動作。 我們將以簡單為原則，將結果列印到狀態面板。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>建置並執行應用程式

現在，您可以開始建置和測試應用程式。

1. 從功能表列中，選擇 [建置]   > [建置方案]  來建置應用程式。 程式碼現在應該可以編譯，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或按 **F5**) 以啟動應用程式。 **helloworld** 視窗會出現。

   ![C# 範例 UWP 語音辨識應用程式 - 快速入門](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. 選取 [啟用麥克風]  ，當存取權限要求出現時，選取 [是]  。

   ![麥克風存取權限要求](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. 選取 [使用麥克風輸入的語音辨識]  ，然後對裝置的麥克風說出英文片語或句子。 您的語音會傳送到語音服務，並且轉譯為文字，出現在視窗中。

   ![語音辨識使用者介面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
