---
title: 快速入門：建立自訂語音助理，Java (Android) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 Android 上以 Java 建立自訂語音助理。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: travisw
ms.openlocfilehash: 8a1dd07fd567f41c2b406aabccd0421b5a6983af
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80671277"
---
## <a name="prerequisites"></a>Prerequisites

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](~/articles/cognitive-services/speech-service/get-started.md)
> * [設定您的開發環境並建立空白專案](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * 建立連線至 [Direct Line Speech 通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)的 Bot
> * 確定您可以存取麥克風以擷取音訊

  > [!NOTE]
  > 請參閱[語音助理支援的區域清單](~/articles/cognitive-services/speech-service/regions.md#voice-assistants)，並確定您的資源已部署於其中一個區域。

## <a name="create-and-configure-a-project"></a>建立和設定專案

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>建立使用者介面

在本節中，我們將建立應用程式的基本使用者介面 (UI)。 首先請開啟主要活動：`activity_main.xml`。 基本範本包含具有應用程式名稱的標題列，和含有訊息 "Hello world!" 的 `TextView`。

接著，請將 `activity_main.xml` 的內容取代為下列程式碼：

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

此 XML 會定義一個與您的 Bot 互動的簡單 UI。

- `button` 元素經點按後即會起始互動並叫用 `onBotButtonClicked` 方法。
- `recoText` 元素會在您與 Bot 交談時顯示語音轉文字的結果。
- `activityText` 元素會針對來自 Bot 的最新 Bot Framework 活動顯示 JSON 承載。

UI 的文字表現方式和畫面現在應會如下：

![](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟 `MainActivity.java`，然後以下列程式碼取代內容：

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * `onCreate` 方法包含要求麥克風和網際網路權限的程式碼。

   * 如先前所述，方法 `onBotButtonClicked` 是按鈕點擊處理常式。 按下按鈕就會觸發您與 Bot 之間的單一互動 (「回合」)。

   * `registerEventListeners` 方法會示範 `DialogServiceConnector` 所使用的事件和傳入活動的基本處理方式。

1. 在相同的檔案中，將組態字串取代為與您的資源相符的字串：

    * 將 `YourSpeechSubscriptionKey` 取代為訂用帳戶金鑰。

    * 將 `YourServiceRegion` 取代為與您的訂用帳戶相關聯的[區域](~/articles/cognitive-services/speech-service/regions.md)。目前只有部分語音服務區域支援 Direct Line Speech。 如需詳細資訊，請參閱[區域](~/articles/cognitive-services/speech-service/regions.md#voice-assistants)。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 將 Android 裝置連接到開發電腦。 請確定您已在裝置上啟用[開發模式和 USB 偵錯](https://developer.android.com/studio/debug/dev-options)。

1. 若要建置應用程式，請按 Ctrl+F9，或從功能表列中選擇 [建置]   > [建立專案]  。

1. 若要啟動應用程式，請按 Shift+F10，或選擇 [執行]   > [執行應用程式]  。

1. 在出現的 [部署目標] 視窗中，選擇您的 Android 裝置。

   ![選取部署目標視窗的螢幕擷取畫面](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-12-deploy.png)

在應用程式及其活動啟動後，按一下按鈕開始與 Bot 交談。 當您說話時，將會顯示轉譯的文字，且如果您接收到來自 Bot 的最新活動，這些活動將會在接收時顯示。 如果您的 Bot 會設定為提供語音回應，就會自動播放語音轉文字。

![Android 應用程式的螢幕擷取畫面](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]

