---
title: 建立自訂關鍵字-語音服務
titleSuffix: Azure Cognitive Services
description: 您的裝置一律會接聽關鍵字（或片語）。 當使用者說出關鍵字時，裝置會將所有後續的音訊傳送到雲端，直到使用者停止說話為止。 自訂您的關鍵字是區分您的裝置並強化商標的有效方式。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/20/2019
ms.author: trbye
ms.openlocfilehash: cef47d1c6598e73d2c17c6799f3de90458206a8d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096258"
---
# <a name="custom-keyword-basics"></a>自訂關鍵字基本概念

在本文中，您將瞭解使用自訂關鍵字的基本概念，並使用語音 Studio 和語音 SDK。 關鍵字是一種單字或短片語，可讓您的產品啟用語音。 您會在語音 Studio 中建立關鍵字模型，然後在您的應用程式中匯出與語音 SDK 搭配使用的模型檔案。

## <a name="prerequisites"></a>先決條件

本文中的步驟需要語音訂用帳戶和語音 SDK。 如果您還沒有訂用帳戶，請[免費試用語音服務](get-started.md)。 若要取得 SDK，請參閱適用于您平臺的[安裝指南](quickstarts/setup-platform.md)。

## <a name="create-a-keyword-in-speech-studio"></a>在語音 Studio 中建立關鍵字

在您可以使用自訂關鍵字之前，您必須先使用[語音 Studio](https://aka.ms/sdsdk-speechportal)上的[自訂關鍵字](https://aka.ms/sdsdk-wakewordportal)頁來建立關鍵字。 在您提供關鍵字之後，它會產生 `.table` 可與語音 SDK 搭配使用的檔案。

> [!IMPORTANT]
> 自訂關鍵字模型和產生的檔案 `.table` **只能**在語音 Studio 中建立。
> 您無法從 SDK 或使用 REST 呼叫來建立自訂關鍵字。

1. 移至[語音 Studio](https://aka.ms/sdsdk-speechportal)並登**入**，或者，如果您還沒有語音訂用帳戶，請選擇 [[**建立訂用**](https://go.microsoft.com/fwlink/?linkid=2086754)帳戶]。

1. 在 [[自訂關鍵字](https://aka.ms/sdsdk-wakewordportal)] 頁面上，建立**新的專案**。 

1. 輸入**名稱**、選擇性**描述**，然後選取語言。 您需要每個語言一個專案，而且支援目前僅限於 `en-US` 語言。

    ![描述關鍵字專案](media/custom-keyword/custom-kws-portal-new-project.png)

1. 從清單中選取您的專案。 

    ![選取您的關鍵字專案](media/custom-keyword/custom-kws-portal-project-list.png)

1. 若要建立新的關鍵字模型，請按一下 [**定型模型**]。

1. 輸入模型的**名稱**、選擇性**描述**，以及您選擇的**關鍵字**，然後按 **[下一步]**。 請參閱選擇有效關鍵字的[指導方針](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword)。

    ![輸入您的關鍵字](media/custom-keyword/custom-kws-portal-new-model.png)

1. 入口網站會為您的關鍵字建立候選發音。 按一下 [播放] 按鈕，並移除任何不正確之發音旁的檢查，以接聽每個候選。 一旦核取了良好的發音，請按一下 [**定型**] 開始產生關鍵字模型。 

    ![檢查關鍵字](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 最多可能需要30分鐘的時間來產生模型。 當模型完成時，關鍵字清單會從**處理**變更為**成功**。 然後您就可以下載檔案。

    ![檢查關鍵字](media/custom-keyword/custom-kws-portal-download-model.png)

1. 下載的檔案是封存檔案 `.zip` 。 解壓縮封存，您會看到副檔名為的檔案 `.table` 。 這是您在下一節中與 SDK 搭配使用的檔案，因此請務必記下其路徑。 檔案名會反映您的關鍵字名稱，例如，關鍵字**啟用裝置**的檔案名 `Activate_device.table` 。

## <a name="use-a-keyword-model-with-the-sdk"></a>搭配使用關鍵字模型與 SDK

首先，使用靜態函式來載入關鍵字模型檔案，該函式會傳回 `FromFile()` `KeywordRecognitionModel` 。 使用 `.table` 您從語音 Studio 下載之檔案的路徑。 此外，您可以 `AudioConfig` 使用預設麥克風建立，然後使用音訊設定來具現化新的 `KeywordRecognizer` 。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

接下來，藉由呼叫來執行關鍵字辨識， `RecognizeOnceAsync()` 方法是傳遞您的模型物件。 這樣會啟動一個持續的關鍵字辨識會話，直到辨識出關鍵字為止。 因此，您通常會在多執行緒應用程式中使用此設計模式，或在您可能會無限期地等候喚醒字的使用案例中。

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> 此處顯示的範例會使用本機關鍵詞辨識，因為它不需要 `SpeechConfig` 驗證內容的物件，也不會與後端連線。 不過，您可以[使用連續的後端連線](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword)來執行關鍵字辨識和驗證。

## <a name="next-steps"></a>後續步驟

使用[語音裝置 SDK 快速入門](https://aka.ms/sdsdk-quickstart)測試您的自訂關鍵字。
