---
title: 快速入門：合成語音，C# (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 您可以使用本指南，透過 .NET Framework for Windows 和語音 SDK 建立文字轉語音的主控台應用程式。 完成後，您可以從文字合成語音，並即時以喇叭聽取語音。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89f491aae91787ec6b67b9aa2daee262f3bab62e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81274768"
---
## <a name="prerequisites"></a>Prerequisites

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟 **Program.cs**，並以此範例取代自動產生的程式碼：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. 尋找字串 `YourSubscriptionKey`，然後將其取代為您的語音服務訂用帳戶金鑰。

1. 尋找字串 `YourServiceRegion`，並將它取代為與您的訂用帳戶相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)。 例如，如果您使用免費試用訂用帳戶，則區域為 `westus`。

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

1. 從功能表列中，選擇 [建置]   > [建置方案]  來建置應用程式。 程式碼現在應該可以編譯，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或選取 **F5**) 以啟動 **helloworld** 應用程式。

1. 輸入英文片語或句子。 應用程式會將您的文字傳送到語音服務，文字會轉換為合成語音並傳送回應用程式，以在喇叭上播放。

   ![語音合成使用者介面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>另請參閱

- [建立自訂語音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [錄製自訂語音樣本](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
