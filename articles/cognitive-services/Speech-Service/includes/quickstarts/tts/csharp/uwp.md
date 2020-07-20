---
title: 快速入門：合成語音，C# (UWP) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您會使用認知服務語音 SDK，來建立 C# 通用 Windows 平台 (UWP) 應用程式。 您會從文字合成語音並即時傳至裝置的喇叭。 應用程式是使用語音 SDK NuGet 套件與 Microsoft Visual Studio 2019 所建置的。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89fc5fddffb291942b8f3a4db3dfdf4ccd6cf46a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275442"
---
> [!NOTE]
> 通用 Windows 平台可讓您開發應用程式，以在任何支援 Windows 10 的裝置 (包括 PC、Xbox、Surface Hub 與其他裝置) 上執行。

## <a name="prerequisites"></a>Prerequisites

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>新增範例程式碼

現在，新增可定義應用程式使用者介面的 XAML 程式碼，並新增程式 C# 程式碼後置實作。

1. 在 [方案總管]  中開啟 `MainPage.xaml`。

1. 在設計工具的 XAML 檢視中，將下列 XAML 程式碼片段插入**格線**標記 (在 `<Grid>` 和 `</Grid>` 之間)：

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. 在 [方案總管]  中，開啟程式碼後置原始檔 `MainPage.xaml.cs`。 (它分組在 `MainPage.xaml` 底下。)

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. 在來源檔案的 `Speak_ButtonClicked` 處理常式中，尋找字串 `YourSubscriptionKey`，並將其取代為與您的訂用帳戶金鑰相關聯的區域。

1. 在 `Speak_ButtonClicked` 處理常式中，尋找字串 `YourServiceRegion`，並將它取代為與您的訂用帳戶相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)。 (例如，針對免費試用訂用帳戶使用 `westus`。)

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  來儲存變更。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

現在，您可以開始建置和測試應用程式。

1. 從功能表列中，選擇 [建置]   > [建置方案]  來建置應用程式。 程式碼現在應該可以編譯，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或按 **F5**) 以啟動應用程式。 **helloworld** 視窗會出現。

   ![C# 範例 UWP 語音合成應用程式 - 快速入門](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. 在文字方塊中輸入一些文字，然後按一下 [說出]  。 您的文字會傳送至語音服務並合成為語音，然後以喇叭播放。

    ![語音合成使用者介面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>另請參閱

- [建立自訂語音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [錄製自訂語音樣本](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
