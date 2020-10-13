---
title: 即時對話轉譯 (預覽) -語音服務
titleSuffix: Azure Cognitive Services
description: '瞭解如何搭配語音 SDK 使用即時對話轉譯。 適用于 c + +、c # 和 JAVA。'
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.custom: devx-track-csharp
ms.openlocfilehash: a425c75dfd57f0d5f9c98b73d8b351972b70703a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88918650"
---
# <a name="real-time-conversation-transcription-preview"></a>即時對話轉譯 (預覽) 

語音 SDK 的 **ConversationTranscriber** API 可讓您透過使用或將音訊串流處理至語音服務，來轉譯會議和其他交談，以新增、移除及識別多個 `PullStream` 參與者 `PushStream` 。 本主題會要求您瞭解如何使用語音 SDK (1.8.0 版或更新版本的) 來使用語音轉換文字。 如需詳細資訊，請參閱 [什麼是語音服務](overview.md)。

## <a name="limitations"></a>限制

- Windows、Linux 和 Android 上的 c + +、c # 和 JAVA 都支援 ConversationTranscriber API。
- 目前在下欄區域的 "en-us" 和 "zh-CN" 語言中提供： _centralus_ 和 _eastasia_。
- 需要具有播放參考資料流的7個 mic 迴圈多重麥克風陣列。 麥克風陣列應符合 [我們的規格](https://aka.ms/sdsdk-microphone)。
- [語音裝置 SDK](speech-devices-sdk.md)提供適合的裝置，以及示範對話轉譯的範例應用程式。

## <a name="optional-sample-code-resources"></a>選擇性的範例程式碼資源

語音裝置 SDK 提供 JAVA 的範例程式碼，以使用8個通道進行即時音訊捕獲。

- [ROOBO 裝置範例程式碼](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Azure Kinect 開發工具組範例程式碼](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>必要條件

語音服務訂用帳戶。 您可以建立 [免費的 Azure 帳戶](https://azure.microsoft.com/free/cognitive-services/) （如果沒有的話）。

## <a name="create-voice-signatures"></a>建立語音簽名

第一個步驟是建立對話參與者的語音簽章，以提供有效率的說話者識別。

### <a name="audio-input-requirements"></a>音訊輸入需求

- 用來建立語音特徵標記的輸入音訊 wave 檔案應為16位樣本、16 kHz 取樣率，以及單一通道 (mono) 格式。
- 每個音訊樣本的建議長度為30秒到2分鐘。

### <a name="sample-code"></a>範例程式碼

下列範例示範如何使用 c # 中 [的 REST API](https://aka.ms/cts/signaturegenservice) ，透過兩種不同的方式來建立語音簽名。 請注意，您必須以 "YourSubscriptionKey"、"speakerVoice" 的 wave 檔案名，以及 `{region}` 和 "YourServiceRegion" (_centralus_ 或 _eastasia_) 的區域取代真實資訊。

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

## <a name="transcribe-conversations"></a>轉譯對話

下列範例程式碼示範如何針對三個喇叭即時轉譯交談。 它會假設您已建立每個說話者的語音簽名，如上所示。 建立 SpeechConfig 物件時，請替代 "YourSubscriptionKey" 和 "YourServiceRegion" 的真實資訊。

範例程式碼重點包含：

- `Conversation` `SpeechConfig` 使用以產生的會議識別碼，從物件建立物件`Guid.NewGuid()`
- 建立 `ConversationTranscriber` 物件並加入對話 `JoinConversationAsync()` 以開始轉譯
- 註冊感興趣的事件
- 使用交談物件在對話中新增或移除參與者
- 串流處理音訊
- 在語音 SDK 版本1.9.0 中，以及 `int` `string` 在 [語音簽名版本] 欄位中支援和數值型別。

轉譯和說話者識別碼會傳回已註冊的事件。

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
                using (var conversationTranscriber = new ConversationTranscriber(audioInput))
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
> [非同步對話轉譯](how-to-async-conversation-transcription.md)
