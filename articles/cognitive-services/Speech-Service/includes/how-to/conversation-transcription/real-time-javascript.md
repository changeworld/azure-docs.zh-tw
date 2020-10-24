---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 7ab4d7708dd75ff2e07e77f3be4f996068704797
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486768"
---
## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">適用於 JavaScript 的語音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>，才能執行動作。 根據您的平台，使用下列指示：

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">網頁瀏覽器 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-voice-signatures"></a>建立語音簽名

第一個步驟是建立對話參與者的語音簽章，讓他們可以識別為唯一的喇叭。 `.wav`用來建立語音簽章的輸入音訊檔應該是16位、16 kHz 的取樣率，以及單一通道 (mono) 格式。 每個音訊樣本的建議長度為30秒到2分鐘。 檔案 `.wav` 應該是 **一個人** 語音的範例，以便建立唯一的語音設定檔。

下列範例顯示如何使用 JavaScript 中 [的 REST API](https://aka.ms/cts/signaturegenservice) 來建立語音簽章。 請注意，您需要將、和路徑的實際資訊替換 `subscriptionKey` `region` 成範例檔案的路徑 `.wav` 。

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

執行此腳本會傳回變數中的語音簽名字串 `voiceSignatureString` 。 執行函式兩次，讓您有兩個字串可作為變數和下面的輸入使用 `voiceSignatureStringUser1` `voiceSignatureStringUser2` 。

> [!NOTE]
> 您 **只能** 使用 REST API 來建立聲音簽名。

## <a name="transcribe-conversations"></a>轉譯對話

下列範例程式碼示範如何即時轉譯兩個喇叭的對話。 它會假設您已經建立每個說話者的語音簽名字串，如上所示。 以 `subscriptionKey` 、 `region` 和您想要轉譯之音訊的路徑取代實際資訊 `filepath` 。

此範例程式碼會執行下列動作：

* 建立用於轉譯的推送資料流程，並將範例檔案寫入 `.wav` 其中。
* 使用建立 `Conversation` `createConversationAsync()` 。
* `ConversationTranscriber`使用函數建立。
* 將參與者加入至對話。 字串 `voiceSignatureStringUser1` 和 `voiceSignatureStringUser2` 應該會以上述步驟的輸出形式來執行。
* 註冊至事件並開始轉譯。

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```