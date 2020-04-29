---
title: 如何使用語音 SDK C 從語音辨識意圖#
titleSuffix: Azure Cognitive Services
description: '在本指南中，您將瞭解如何使用適用于 c # 的語音 SDK 從語音辨識意圖。'
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: 41ebcb7b44ea88af06a30a611960fd8bb0ceddee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402229"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>如何使用適用于 C 的語音 SDK 從語音辨識意圖#

認知服務[語音 SDK](speech-sdk.md) 會與 [Language Understanding Intelligent Service (LUIS)](https://www.luis.ai/home) 整合以提供**意圖辨識**。 意圖是使用者想要做的事情：訂機票、查看天氣或打電話。 使用者可以使用任何他們覺得自然的字詞。 使用機器學習服務，LUIS 會將使用者要求對應至您已定義的意圖。

> [!NOTE]
> LUIS 應用程式 (application) 會定義您想要辨識的的意圖和實體。 其有別於使用語音服務的 C# 應用程式 (application)。 在本文中，「應用程式 (app)」意指 LUIS 應用程式 (app)，「應用程式 (application)」則意指 C# 程式碼。

在本指南中，您會使用語音 SDK 來開發 c # 主控台應用程式，透過裝置的麥克風從使用者語句衍生意圖。 您將學習如何：

> [!div class="checklist"]
>
> - 建立 Visual Studio 專案，並讓其參考語音 SDK NuGet 套件
> - 建立語音設定並取得意圖辨識器
> - 取得 LUIS 應用程式 (app) 的模型，並新增所需意圖
> - 指定語音辨識的語言
> - 從檔案辨識語音
> - 使用非同步的事件驅動連續辨識

## <a name="prerequisites"></a>先決條件

開始本指南之前，請確定您有下列專案：

- LUIS 帳戶。 您可以透過 [LUIS 入口網站](https://www.luis.ai/home)免費取得。
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (任何版本)。

## <a name="luis-and-speech"></a>LUIS 和語音

LUIS 會與語音服務整合以從語音辨識意圖。 您不需要語音服務訂用帳戶，只要有 LUIS 即可。

LUIS 會使用三種金鑰：

| 金鑰類型  | 目的                                               |
| --------- | ----------------------------------------------------- |
| 編寫 | 可讓您透過程式設計建立和修改 LUIS 應用程式 |
| 入門   | 可讓您只使用文字來測試 LUIS 應用程式   |
| 端點  | 可授權特定 LUIS 應用程式的存取權            |

在本指南中，您需要端點金鑰類型。 本指南會使用範例 Home Automation LUIS 應用程式，您可以遵循[使用預先建立的首頁自動化應用程式](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)快速入門來建立。 如果您已建立自有 LUIS 應用程式，則可改用此應用程式。

當您建立 LUIS 應用程式時，LUIS 會自動產生入門金鑰，讓您可以使用文字查詢來測試應用程式。 此金鑰不會啟用語音服務整合，也不會與本指南搭配使用。 在 Azure 儀表板中建立 LUIS 資源，並將它指派給 LUIS 應用程式。 您可以使用本指南的免費訂用帳戶層。

在 Azure 儀表板中建立 LUIS 資源之後，登入 [LUIS 入口網站](https://www.luis.ai/home)，在 [我的應用程式]**** 頁面上選擇您的應用程式，然後切換至應用程式的 [管理]**** 頁面。 最後，選取提要欄位中的 [金鑰和端點]****。

![LUIS 入口網站的金鑰和端點設定](media/sdk/luis-keys-endpoints-page.png)

在 [金鑰和端點設定]**** 頁面上：

1. 向下捲動至 [資源與金鑰]**** 區段，然後選取 [指派資源]****。
1. 在 [對應用程式指派金鑰]**** 對話方塊中，進行下列變更：

   - 在 [租用戶]**** 下，選擇 [Microsoft]****。
   - 在 [訂用帳戶名稱]**** 下，選擇包含所要使用 LUIS 資源的 Azure 訂用帳戶。
   - 在 [金鑰]**** 下，選擇要與應用程式搭配使用的 LUIS 資源。

   稍等一會兒，新的訂用帳戶就會出現在頁面底部的資料表中。

1. 選取金鑰旁邊的圖示，將它複製到剪貼簿。 (您可以使用任一金鑰。)

![LUIS 應用程式 (app) 訂用帳戶金鑰](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>在 Visual Studio 中建立語音專案

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>新增程式碼

接下來，將程式碼新增至專案。

1. 在 [方案總管]**** 中，開啟 **Program.cs** 檔案。

1. 以下列宣告取代檔案開頭的 `using` 陳述式區塊：

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. 以下列非同步`Main()`對應項取代提供的方法：

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. 建立空白的非同步方法 `RecognizeIntentAsync()`，如下所示：

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. 在此新方法的主體中，新增此程式碼：

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. 將這個方法中的預留位置更換為 LUIS 訂用帳戶金鑰、區域和應用程式 (app) 識別碼，如下所示。

   | 預留位置 | 更換為 |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | LUIS 端點金鑰。 同樣地，您必須從 Azure 儀表板取得此項目，而不是「入門金鑰」。 您可以在[LUIS 入口網站](https://www.luis.ai/home)中，于應用程式的 [**金鑰和端點**] 頁面上找到它（在 [**管理**] 底下）。 |
   | `YourLanguageUnderstandingServiceRegion` | LUIS 訂用帳戶所在區域的簡短識別碼，例如美國西部是 `westus`。 請參閱[區域](regions.md)。 |
   | `YourLanguageUnderstandingAppId` | LUIS 應用程式識別碼。 您可以在 [LUIS 入口網站](https://www.luis.ai/home)中應用程式的 [設定]**** 頁面上找到此值。 |

進行這些變更之後，您就可以建立（**Control + Shift + B**）並執行（**F5**）應用程式。 出現提示時，請試著對電腦的麥克風說「關燈」。 應用程式會在主控台視窗中顯示結果。

下列幾節會討論程式碼。

## <a name="create-an-intent-recognizer"></a>建立意圖辨識器

首先，您必須從 LUIS 端點金鑰和區域建立語音設定。 您可以使用語音設定來建立各種語音 SDK 功能的辨識器。 語音設定有多種方式可指定您想要使用的訂用帳戶，在此我們使用 `FromSubscription`，其會採用訂用帳戶金鑰和區域。

> [!NOTE]
> 使用 LUIS 訂用帳戶的金鑰和區域，而不是語音服務訂用帳戶。

下一步，使用 `new IntentRecognizer(config)` 建立意圖辨識器。 因為設定已經知道所要使用的訂用帳戶，因此不需在建立辨識器時再次指定訂用帳戶的金鑰和端點。

## <a name="import-a-luis-model-and-add-intents"></a>匯入 LUIS 模型及新增意圖

現在，使用 `LanguageUnderstandingModel.FromAppId()` 從 LUIS 應用程式 (app) 匯入模型，並透過辨識器的 `AddIntent()` 方法新增您想要辨識的 LUIS 意圖。 這兩個步驟可藉由指出使用者可能會在其要求中使用的字組，來改善語音辨識的精確度。 如果您不需要辨識應用程式中的所有應用程式意圖，則不需要新增所有意圖。

若要新增意圖，您必須提供三個引數：LUIS 模型 (已建立並命名為 `model`)、意圖名稱和意圖識別碼。 識別碼與名稱之間的差異如下。

| `AddIntent()`&nbsp;引數 | 目的 |
| --------------------------- | ------- |
| `intentName` | LUIS 應用程式 (app) 中所定義的意圖名稱。 此值必須完全符合 LUIS 意圖名稱。 |
| `intentID` | 語音 SDK 指派給可辨識意圖的識別碼。 此值可以是任何值；不需要對應至 LUIS 應用程式中所定義的意圖名稱。 舉例來說，如果由相同程式碼處理多個意圖，您可以讓這些意圖使用相同的識別碼。 |

「家庭自動化」LUIS 應用程式有兩個意圖：一個是要開啟裝置，另一個是要關閉裝置。 下列程式行會將這些意圖新增至辨識器；請將 `RecognizeIntentAsync()` 方法中的三個 `AddIntent` 程式行更換為此程式碼。

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

您也可以使用 `AddAllIntents` 方法來將模型中的所有意圖新增至辨識器，而不是新增個別的意圖。

## <a name="start-recognition"></a>開始辨識

建立辨識器並新增意圖後，就可以開始辨識。 語音 SDK 同時支援一次性和連續辨識。

| 辨識模式 | 要呼叫的方法 | 結果 |
| ---------------- | --------------- | ------ |
| 一次性 | `RecognizeOnceAsync()` | 在說完一個語句之後傳回可辨識的意圖 (如果有的話)。 |
| 連續 | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | 辨識多個語句；在可取得結果時發出事件 (例如，`IntermediateResultReceived`)。 |

應用程式會使用一次模式，因此會`RecognizeOnceAsync()`呼叫以開始辨識。 結果是 `IntentRecognitionResult` 物件，其中包含有關所辨識意圖的資訊。 您使用下列運算式來擷取 LUIS JSON 回應：

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

應用程式不會剖析 JSON 結果。 它只會在主控台視窗中顯示 JSON 文字。

![單一 LUIS 辨識結果](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>指定辨識語言

根據預設，LUIS 會辨識美式英文 (`en-us`) 的意圖。 藉由對語音設定的 `SpeechRecognitionLanguage` 屬性指派地區設定代碼，即可辨識其他語言的意圖。 例如，先在`config.SpeechRecognitionLanguage = "de-de";`應用程式中新增，然後再建立辨識器來辨識德文中的意圖。 如需詳細資訊，請參閱[LUIS language support](../LUIS/luis-language-support.md#languages-supported)。

## <a name="continuous-recognition-from-a-file"></a>從檔案進行連續辨識

下列程式碼說明兩個使用語音 SDK 來辨識意圖的額外功能。 第一個是前面提過的連續辨識，辨識器會在有結果時發出事件。 然後，由您提供的事件處理常式處理這些事件。 在進行連續辨識時，您會呼叫辨識器的 `StartContinuousRecognitionAsync()` 方法 (而非 `RecognizeOnceAsync()`) 開始辨識。

另一個功能是從 WAV 檔案讀取含有所要處理語音的音訊。 實作包括建立音訊設定以供建立意圖辨識器時使用。 檔案必須是取樣率為 16 kHz 的單聲道 (mono)。

若要試用這些功能，請刪除或以註解排除 `RecognizeIntentAsync()` 方法的主體，並在其位置新增下列程式碼。

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

請修訂程式碼，在其中納入 LUIS 端點金鑰、區域和應用程式 (app) 識別碼，以及如過去一樣新增「家庭自動化」意圖。 將 `whatstheweatherlike.wav` 變更為所錄製音訊檔案的名稱。 接著建置音訊檔案並複製到組建目錄，然後執行應用程式。

例如，如果您說「關燈」並暫停片刻，然後在所錄製的音訊檔案中說「開燈」，則主控台輸出會如下所示：

![音訊檔案 LUIS 辨識結果](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 **samples/csharp/sharedcontent/console** 資料夾中尋找本文中的程式碼。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：從麥克風辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)
