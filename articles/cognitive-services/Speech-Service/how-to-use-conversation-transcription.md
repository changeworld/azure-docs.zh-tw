---
title: 即時對話轉錄（預覽） - 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何將即時對話轉錄與語音 SDK 一起使用。 可用於C++、C# 和 JAVA。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 64a9e11cec7164fb4421dd018238de9f0670382b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76263722"
---
# <a name="real-time-conversation-transcription-preview"></a>即時對話轉錄（預覽）

語音 SDK 的 **"對話轉錄器"API**允許您轉錄會議和其他對話，以便使用 或`PullStream``PushStream`將音訊資料流到語音服務，從而添加、刪除和識別多個參與者。 本主題要求您瞭解如何將語音到文本與語音 SDK（版本 1.8.0 或更高版本）一起使用。 有關詳細資訊，請參閱[什麼是語音服務](overview.md)。

## <a name="limitations"></a>限制

- 在 Windows、Linux 和 Android 上支援 C++、C# 和 JAVA 的會話轉錄器 API。
- 目前在以下區域提供"en-US"和"zh-CN"語言：_中亞_和_東亞_。
- 需要一個帶播放參考流的 7 麥克風圓形多麥克風陣列。 麥克風陣列應符合[我們的規格](https://aka.ms/sdsdk-microphone)。
- [語音設備 SDK](speech-devices-sdk.md)提供合適的設備和演示對話轉錄的示例應用。

## <a name="optional-sample-code-resources"></a>可選的示例代碼資源

語音設備 SDK 在 JAVA 中提供示例代碼，以便使用 8 個通道進行即時音訊捕獲。

- [ROOBO 設備示例代碼](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Azure Kinect 開發人員工具組示例代碼](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Prerequisites

語音服務訂閱。 如果沒有["語音試用版"訂閱，則可以獲取語音試用版訂閱](https://azure.microsoft.com/try/cognitive-services/)。

## <a name="create-voice-signatures"></a>創建語音簽名

第一步是為對話參與者創建語音簽名，以便有效地識別演講者。

### <a name="audio-input-requirements"></a>音訊輸入要求

- 用於創建語音簽名的輸入音訊波檔應採用 16 位採樣、16 kHz 採樣速率和單聲道（單聲道）格式。
- 每個音訊樣本的建議長度在三十秒到兩分鐘之間。

### <a name="sample-code"></a>範例程式碼

下面的示例顯示了使用 C# 中的 REST [API](https://aka.ms/cts/signaturegenservice)創建語音簽名的兩種不同方法。 請注意，您需要將真實資訊替換為"您的訂閱金鑰"，您的波檔案名稱為"揚聲器Voice.wav"，以及您所在區域`{region}`和"您的服務區域"（_中央_或_東亞_）。

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>轉錄對話

以下示例代碼演示如何即時轉錄三個揚聲器的對話。 它假定您已經為每個揚聲器創建了語音簽名，如上所示。 創建語音設定物件時，將真實資訊替換為"訂閱金鑰"和"您的服務區域"。

示例代碼亮點包括：

- 使用使用`Conversation`使用會議識別碼`SpeechConfig`生成的會議識別碼從物件創建物件`Guid.NewGuid()`
- 創建`ConversationTranscriber`物件並加入對話以`JoinConversationAsync()`開始轉錄
- 註冊感興趣的事件
- 使用對話物件向對話添加或刪除參與者
- 資料流音訊
- 在語音 SDK 版本 1.9.0`int`中`string`，語音簽名版本欄位中支援和數值型別。

轉錄和揚聲器識別碼在已註冊的事件中返回。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [非同步對話轉錄](how-to-async-conversation-transcription.md)
