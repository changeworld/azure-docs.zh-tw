---
title: 快速入門：將語音翻譯成多種目標語言，Java (Windows、Linux) - 語音服務
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: erhopf
ms.openlocfilehash: 0bc5a6a77c676931a7c74159fbff476baef5647c
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226228"
---
## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)

## <a name="add-sample-code"></a>新增範例程式碼

1. 若要將新的空白類別新增到您的 Java 專案，請選取 [檔案] > [新增] > [類別]。

1. 在 [新增 Java 類別] 視窗中，於 [套件] 欄位中輸入 **speechsdk.quickstart**，並在 [名稱] 欄位中輸入 **Main**。

   ![[新增 Java 類別] 視窗的螢幕擷取畫面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. 使用下列程式碼片段來取代 `Main.java` 中的所有程式碼：

   ```Java
   package quickstart;

   import java.io.IOException;
   import java.util.concurrent.Future;
   import java.util.Map;
   import java.util.concurrent.ExecutionException;
   import com.microsoft.cognitiveservices.speech.*;
   import com.microsoft.cognitiveservices.speech.translation.*;

   public class Main {

       public static void translationWithMicrophoneAsync() throws InterruptedException, ExecutionException, IOException
       {
           // Creates an instance of a speech translation config with specified
           // subscription key and service region. Replace with your own subscription key
           // and region identifier from here: https://aka.ms/speech/sdkregion

           int exitCode = 1;
           SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey",  "YourServiceRegion");
           assert(config != null);

           // Sets source and target languages.
           String fromLanguage = "en-US";
           config.setSpeechRecognitionLanguage(fromLanguage);
           config.addTargetLanguage("de");
           config.addTargetLanguage("fr");

           // Creates a translation recognizer using the default microphone audio input device.
           TranslationRecognizer recognizer = new TranslationRecognizer(config);
           assert(recognizer != null);

           System.out.println("Say something...");

           // Starts translation, and returns after a single utterance is recognized. The end of a
           // single utterance is determined by listening for silence at the end or until a maximum of 15
           // seconds of audio is processed. The task returns the recognized text as well as the translation.
           // Note: Since recognizeOnceAsync() returns only a single utterance, it is suitable only for single
           // shot recognition like command or query.
           // For long-running multi-utterance recognition, use startContinuousRecognitionAsync() instead.
           Future<TranslationRecognitionResult> task = recognizer.recognizeOnceAsync();
           assert(task != null);

           TranslationRecognitionResult result = task.get();
           assert(result != null);

           if (result.getReason() == ResultReason.TranslatedSpeech) {
               System.out.println("RECOGNIZED '" + fromLanguage + "': " + result.getText());

               Map<String, String> map = result.getTranslations();
               for(String toLanguage: map.keySet()) {
                   System.out.println("TRANSLATED into '" + toLanguage + "': " + map.get(toLanguage));
               }
               exitCode = 0;
           }
           else if (result.getReason() == ResultReason.RecognizedSpeech) {
               System.out.println("RECOGNIZED '" + fromLanguage + "': " + result.getText() + "(text could not be  translated)");
               exitCode = 0;
           }
           else if (result.getReason() == ResultReason.NoMatch) {
               System.out.println("NOMATCH: Speech could not be recognized.");
           }
           else if (result.getReason() == ResultReason.Canceled) {
               CancellationDetails cancellation = CancellationDetails.fromResult(result);
               System.out.println("CANCELED: Reason=" + cancellation.getReason());

               if (cancellation.getReason() == CancellationReason.Error) {
                   System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                   System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                   System.out.println("CANCELED: Did you update the subscription info?");
               }
           }

           recognizer.close();

           System.exit(exitCode);
       }

       public static void main(String[] args) {
           try {
               translationWithMicrophoneAsync();
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());
               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。

1. 將字串 `YourServiceRegion` 取代為與您的訂用帳戶相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

按 F11 鍵，或選取 [執行] > [偵錯]。

1. 請說英文片語或句子。 應用程式會將您的語音傳送到語音服務，語音服務會進行翻譯並轉換為文字 (在此案例中，為法文轉換為德文)。 語音服務接著會將文字傳送回應用程式以供顯示。

````
Say something...
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
TRANSLATED into 'fr': Quel temps fait-il à Seattle ?
````

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
