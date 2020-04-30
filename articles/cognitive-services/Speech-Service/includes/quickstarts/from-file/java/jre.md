---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 85124a51b986b6e3d6444b35aa264e136abcdb76
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400488"
---
## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 若要將新的空白類別新增到您的 Java 專案，請選取 [檔案]   > [新增]   > [類別]  。

1. 在 [新增 Java 類別]  視窗中，於 [套件]  欄位中輸入 **speechsdk.quickstart**，並在 [名稱]  欄位中輸入 **Main**。

   ![[新增 Java 類別] 視窗的螢幕擷取畫面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. 使用下列程式碼片段來取代 `Main.java` 中的所有程式碼：

   ```java
   package speechsdk.quickstart;

   import java.util.concurrent.Future;
   import com.microsoft.cognitiveservices.speech.*;

   /**
    * Quickstart: recognize speech using the Speech SDK for Java.
    */
   public class Main {

       /**
        * @param args Arguments are ignored in this sample.
        */
       public static void main(String[] args) {
           try {
               // Replace below with your own subscription key
               String speechSubscriptionKey = "YourSubscriptionKey";

               // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
               String serviceRegion = "YourServiceRegion";

               // Replace below with your own filename.
               String audioFileName = "whatstheweatherlike.wav";

               int exitCode = 1;
               SpeechConfig config = SpeechConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
               assert(config != null);

               AudioConfig audioInput = AudioConfig.fromWavFileInput(audioFileName);
               assert(audioInput != null);

               SpeechRecognizer reco = new SpeechRecognizer(config, audioInput);
               assert(reco != null);

               System.out.println("Recognizing first result...");

               Future<SpeechRecognitionResult> task = reco.recognizeOnceAsync();
               assert(task != null);

               SpeechRecognitionResult result = task.get();
               assert(result != null);

               switch (result.getReason()) {
                   case ResultReason.RecognizedSpeech: {
                           System.out.println("We recognized: " + result.getText());
                           exitCode = 0;
                       }
                       break;
                   case ResultReason.NoMatch:
                       System.out.println("NOMATCH: Speech could not be recognized.");
                       break;
                   case ResultReason.Canceled: {
                           CancellationDetails cancellation = CancellationDetails.fromResult(result);
                           System.out.println("CANCELED: Reason=" + cancellation.getReason());
        
                           if (cancellation.getReason() == CancellationReason.Error) {
                               System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                               System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                               System.out.println("CANCELED: Did you update the subscription info?");
                           }
                       }
                       break;
               }

               reco.close();

               System.exit(exitCode);
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());

               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)取代 `YourServiceRegion` 字串。

1. 以您自己的檔案名稱取代字串 `whatstheweatherlike.wav`。

1. 儲存專案的變更。

> [!NOTE]
> 語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

按 F11 鍵，或選取 [執行]   > [偵錯]  。
系統會從音訊檔案中辨識語音輸入的前 15 秒，並記錄在主控台視窗中。

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]