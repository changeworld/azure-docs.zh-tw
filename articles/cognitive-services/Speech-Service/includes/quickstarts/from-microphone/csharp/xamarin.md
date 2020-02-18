---
title: 快速入門：從麥克風辨識語音，C# (Xamarin) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您會使用認知服務語音 SDK 建立適用於通用 Windows 平台 (UWP)、Android 和 iOS 的跨平台 C# Xamarin 應用程式。 您會將來自裝置或模擬器麥克風的語音即時轉譯為文字。 應用程式是使用語音 SDK NuGet 套件與 Microsoft Visual Studio 2019 所建置的。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 3f315f29eab107c9e0e145bd25db71a8cb8b2ace
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156708"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定開發環境](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * [建立空的範例專案](../../../../quickstarts/create-project.md?tabs=xamarin)
> * 確定您可以存取麥克風以擷取音訊

如果您已經這麼做，很棒。 讓我們繼續進行。

## <a name="add-sample-code-for-the-common-helloworld-project"></a>新增一般 helloworld 專案的範例程式碼

一般 helloworld 專案包含跨平台應用程式的平台通用實作。 現在，新增可定義應用程式使用者介面的 XAML 程式碼，並新增程式 C# 程式碼後置實作。

1. 在 [方案總管]  中的一般 helloworld 專案下方，開啟 `MainPage.xaml`。

1. 在設計工具的 XAML 檢視中，將下列 XAML 程式碼片段插入 `<StackLayout>` 和 `</StackLayout>` 之間的**格線**標記：

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. 在 [方案總管]  中，開啟程式碼後置原始檔 `MainPage.xaml.cs`。 其分組在 `MainPage.xaml` 底下。

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. 在來源檔案的 `OnRecognitionButtonClicked` 處理常式中，尋找字串 `YourSubscriptionKey`，並將其取代為與您的訂用帳戶金鑰相關聯的區域。


1. 在 `OnRecognitionButtonClicked` 處理常式中，尋找字串 `YourServiceregion`，並將其取代為訂用帳戶所關聯[區域](https://aka.ms/speech/sdkregion)中的「語音 SDK 參數」。 (例如，針對免費試用訂用帳戶使用 `westus`。)

1. 接下來，您必須建立 [Xamarin 服務](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)，以用來查詢不同平台專案 (例如 UWP、Android 和 iOS) 的麥克風權限。 若要這麼做，請在 helloworld 專案下新增名為 Services  的新資料夾，並在其中建立新的 C# 原始程式檔。 您可以用滑鼠右鍵按一下 [Services]  資料夾，然後選取 [新增]   > [新增項目]   > [程式碼檔案]  。 重新命名 `IMicrophoneService.cs` 檔案，並將下列程式碼片段中的所有程式碼放在該檔案中：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="androidtabx-android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>新增 `helloworld.Android` 專案的範例程式碼

現在，新增 C# 程式碼以定義應用程式的 Android 特定部分。

1. 在 [方案總管]  中的 helloworld.Android 專案下方，開啟 `MainActivity.cs`。

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. 接著，在 helloworld.Android 專案下建立新的 Services  資料夾，以新增 `MicrophoneService` 的 Android 特定實作。 之後，在其中建立新的 C# 原始程式檔。 重新命名 `MicrophoneService.cs`檔案。 將以下程式碼片段複製並貼上到該檔案中：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. 之後，請在 [Properties]  資料夾底下開啟 `AndroidManifest.xml`。 在 `<manifest>` 與 `</manifest>` 之間新增麥克風的下列使用權限設定：

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="iostabios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>新增 `helloworld.iOS` 專案的範例程式碼

現在，新增 C# 程式碼以定義應用程式的 iOS 特定部分。 另請建立 helloworld.iOS 專案的 Apple 裝置特定設定。

1. 在 [方案總管]  中的 helloworld.iOS 專案下方，開啟 `AppDelegate.cs`。

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. 接著，在 helloworld.iOS 專案下建立新的 Services  資料夾，以新增 `MicrophoneService` 的 iOS 特定實作。 之後，在其中建立新的 C# 原始程式檔。 重新命名 `MicrophoneService.cs`檔案。 將以下程式碼片段複製並貼上到該檔案中：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. 在文字編輯器中，開啟 helloworld.iOS 專案底下的 `Info.plist`。 在 dict 區段下新增下列索引鍵值組：

   <key>NSMicrophoneUsageDescription</key>
   <string>此範例應用程式需要存取麥克風</string>

   > [!NOTE]
   > 如果您是針對 iPhone 裝置進行建置，請確定 `Bundle Identifier` 符合您裝置的佈建設定檔應用程式識別碼。 否則，建置將會失敗。 對於 iPhoneSimulator，您可以將它保持原狀。

1. 如果您是在 Windows 電腦上建置，則必須與 Mac 裝置建立連線，以透過 [工具]   > [iOS]   > [與 Mac 配對]  進行建置。 請依照 Visual Studio 提供的指示精靈操作，以啟用 Mac 裝置的連線。

#### <a name="uwptabhelloworlduwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>新增 `helloworld.UWP` 專案的範例程式碼

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>新增 helloworld.UWP 專案的範例程式碼

現在，新增 C# 程式碼以定義應用程式的 UWP 特定部分。

1. 在 [方案總管]  中的 helloworld.UWP 專案下方，開啟 `MainPage.xaml.cs`。

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. 接著，在 helloworld.UWP 專案下建立新的 Services  資料夾，以新增 `MicrophoneService` 的 UWP 特定實作。 之後，在其中建立新的 C# 原始程式檔。 重新命名 `MicrophoneService.cs`檔案。 將以下程式碼片段複製並貼上到該檔案中：

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. 接下來，在 Visual Studio 中的 helloworld.UWP 專案底下按兩下 `Package.appxmanifest` 檔案。 在 [功能]  底下，確認已選取 [麥克風]  ，然後儲存檔案。

1. 接下來，在 Visual Studio 中按兩下 `helloworld.UWP` 專案下的 `Package.appxmanifest` 檔案，並在勾選 [功能]   > [麥克風]  後儲存檔案。
   > 注意:如果您看到警告：憑證檔案不存在：helloworld.UWP_TemporaryKey.pfx，請檢查 [語音轉文字](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)範例以取得詳細資訊。

1. 從功能表列中選取 [檔案]   > [全部儲存]  來儲存變更。

## <a name="build-and-run-the-uwp-application"></a>建置並執行 UWP 應用程式

1. 將 helloworld.UWP 設定為啟始專案。 以滑鼠右鍵按一下 helloworld.UWP 專案，然後選取 [建置]  來建置應用程式。

1. 選取 [偵錯]   > [開始偵錯]  (或選取 **F5**) 以啟動應用程式。 **helloworld** 視窗會出現。

   ![C# 範例 UWP 語音辨識應用程式 - 快速入門](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. 選取 [啟用麥克風]  。 當存取權限的要求出現時，請選取 [是]  。

   ![麥克風存取權限要求](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. 選取 [啟動語音辨識]  ，然後對裝置的麥克風說出英文片語或句子。 您的語音會傳送到語音服務，並且轉譯為文字，出現在視窗中。

   ![語音辨識使用者介面](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>建置並執行 Android 和 iOS 應用程式

在裝置或模擬器中建置並執行 Android 和 iOS 應用程式的方式與 UWP 類似。 請確定本文＜必要條件＞一節中所需的所有 SDK 均已正確安裝。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
