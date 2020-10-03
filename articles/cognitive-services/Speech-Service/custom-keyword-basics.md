---
title: Create 關鍵字快速入門-語音服務
titleSuffix: Azure Cognitive Services
description: 您的裝置一律會接聽關鍵字 (或片語) 。 當使用者說出關鍵字時，裝置會將所有後續的音訊傳送到雲端，直到使用者停止說話。 自訂關鍵字是用來區分您的裝置和強化品牌的有效方式。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: f2f333cd01057ff5f6f904924880f88b0685c72f
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667688"
---
# <a name="get-started-with-custom-keyword"></a>開始使用自訂關鍵字

在本快速入門中，您將瞭解使用「語音 Studio」和「語音 SDK」來處理自訂關鍵字的基本概念。 關鍵字是可讓您的產品啟用語音的單字或簡短片語。 您可以在「語音 Studio」中建立關鍵字模型，然後在您的應用程式中匯出與「語音 SDK」搭配使用的模型檔案。

## <a name="prerequisites"></a>Prerequisites

本文中的步驟需要語音訂用帳戶和語音 SDK。 如果您還沒有訂用帳戶，請 [免費試用語音服務](overview.md#try-the-speech-service-for-free)。 若要取得 SDK，請參閱適用于您平臺的 [安裝指南](quickstarts/setup-platform.md) 。

## <a name="create-a-keyword-in-speech-studio"></a>在語音 Studio 中建立關鍵字

在您可以使用自訂關鍵字之前，您必須使用[Speech Studio](https://aka.ms/sdsdk-speechportal)上的[自訂關鍵字](https://aka.ms/sdsdk-wakewordportal)頁面建立關鍵字。 提供關鍵字之後，它會產生可搭配 `.table` 語音 SDK 使用的檔案。

> [!IMPORTANT]
> 自訂關鍵字模型和產生的檔案 `.table` **只能** 在 Speech Studio 中建立。
> 您無法從 SDK 或使用 REST 呼叫來建立自訂關鍵字。

1. 移至 [語音 Studio](https://aka.ms/sdsdk-speechportal) 並登 **入** ，如果您還沒有語音訂用帳戶，請選擇 [ [**建立訂用**](https://go.microsoft.com/fwlink/?linkid=2086754)帳戶]。

1. 在 [ [自訂關鍵字](https://aka.ms/sdsdk-wakewordportal) ] 頁面上，建立 **新專案**。 

1. 輸入 **名稱**和選擇性 **描述**，然後選取語言。 您每一種語言都需要一個專案，而且目前支援的 `en-US` 語言有限。

    ![描述關鍵字專案](media/custom-keyword/custom-kws-portal-new-project.png)

1. 從清單中選取您的專案。 

    ![選取關鍵字專案](media/custom-keyword/custom-kws-portal-project-list.png)

1. 若要建立新的關鍵字模型，請按一下 [ **定型模型**]。

1. 輸入模型的 **名稱** 、選擇性 **描述**，以及您選擇的 **關鍵字** ，然後按 **[下一步]**。 請參閱選擇有效關鍵字的 [指導方針](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) 。

    ![輸入您的關鍵字](media/custom-keyword/custom-kws-portal-new-model.png)

1. 入口網站會建立關鍵字的候選發音。 按一下 [播放] 按鈕，並移除任何不正確發音旁的檢查，以聆聽每個候選。 只要勾選了良好的發音，請按一下 [ **定型** ] 開始產生關鍵字模型。 

    ![檢查關鍵字](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 產生模型最多可能需要30分鐘的時間。 當模型完成時，關鍵字清單會從 **處理** 變更為「 **成功** 」。 然後您就可以下載檔案。

    ![檢查關鍵字](media/custom-keyword/custom-kws-portal-download-model.png)

1. 下載的檔案是封存 `.zip` 。 將封存解壓縮，您會看到副檔名為的檔案 `.table` 。 這是您在下一節中用來搭配 SDK 使用的檔案，因此請務必記下其路徑。 檔案名會鏡像您的關鍵字名稱，例如，關鍵字 **啟用裝置** 的檔案名 `Activate_device.table` 。

## <a name="use-a-keyword-model-with-the-sdk"></a>搭配使用關鍵字模型與 SDK

首先，使用靜態函式載入關鍵字模型檔案，此函式會傳回 `FromFile()` `KeywordRecognitionModel` 。 使用 `.table` 您從 Speech Studio 下載之檔案的路徑。 此外，您可以 `AudioConfig` 使用預設的麥克風建立，然後使用音訊設定來具現化新的 `KeywordRecognizer` 。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

接下來，藉 `RecognizeOnceAsync()` 由傳遞您的模型物件，透過一次呼叫來執行關鍵字識別。 這會啟動一個持續的關鍵字辨識會話，直到辨識關鍵字為止。 因此，您通常會在多執行緒應用程式中使用此設計模式，或在可能會無限期等候喚醒字的使用案例中使用此設計模式。

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> 此處所示的範例會使用本機關鍵詞辨識，因為它不需要 `SpeechConfig` 驗證內容的物件，也不會與後端連線。 不過，您可以 [使用連續的後端連接](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword)來執行關鍵字辨識和驗證。

## <a name="next-steps"></a>後續步驟

使用 [語音裝置 SDK 快速入門](https://aka.ms/sdsdk-quickstart)測試您的自訂關鍵字。
