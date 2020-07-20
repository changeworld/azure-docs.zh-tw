---
title: 非同步對話轉譯（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用語音服務來進行非同步對話轉譯。 僅適用于 JAVA。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 57543f4a3779145ce66259eec1abac195b63c7ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80384291"
---
# <a name="asynchronous-conversation-transcription-preview"></a>非同步對話轉譯（預覽）

在本文中，非同步對話轉譯會使用**RemoteConversationTranscriptionClient** API 來示範。 如果您已設定對話轉譯來執行非同步轉譯，而且有`conversationId`，您可以`conversationId`使用**RemoteConversationTranscriptionClient** API 取得與相關聯的轉譯。

## <a name="asynchronous-vs-real-time--asynchronous"></a>非同步與即時 + 非同步

透過非同步轉譯，您可以串流處理對話音訊，但不需要即時傳回的轉譯。 相反地，在傳送音訊之後，請使用`conversationId`的`Conversation`來查詢非同步轉譯的狀態。 當非同步轉譯準備就緒時，您會取得`RemoteConversationTranscriptionResult`。

透過即時加上非同步，您可以即時取得轉譯，但也會使用來查詢`conversationId` （類似于非同步案例）來取得轉譯。

完成非同步轉譯需要兩個步驟。 第一個步驟是上傳音訊，選擇 [僅限非同步] 或 [即時加上非同步]。 第二個步驟是取得轉譯結果。

## <a name="upload-the-audio"></a>上傳音訊

在執行非同步轉譯之前，您必須使用 Microsoft 認知語音用戶端 SDK （1.8.0 或更新版本）將音訊傳送至對話轉譯服務。

此範例程式碼示範如何針對非同步模式建立對話 transcriber。 若要將音訊串流至 transcriber，您必須[使用語音 SDK 來即時](how-to-use-conversation-transcription-service.md)新增衍生自轉譯交談的音訊串流程式碼。 請參閱該主題的**限制**一節，以查看支援的平臺和語言 api。

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

如果您想要即時_加上_非同步，請將適當的程式程式碼批註並取消批註，如下所示：

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>取得轉譯結果

此步驟會取得非同步轉譯結果，但會假設您可能需要的任何即時處理都是在其他地方完成。 如需詳細資訊，請參閱[使用語音 SDK 即時轉譯對話](how-to-use-conversation-transcription-service.md)。

針對此處顯示的程式碼，您需要**遠端對話版本 1.8.0**，僅支援 Windows、Linux 和 Android 上的 JAVA （1.8.0 或更新版本）（僅限 API 層級26或以上）。

### <a name="obtaining-the-client-sdk"></a>取得用戶端 SDK

您可以編輯 pom .xml 檔案來取得**遠端交談**，如下所示。

1. 在檔案結尾的結束記號`</project>`之前，建立`repositories`元素，其中包含適用于語音 SDK 的 Maven 存放庫參考：

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. 同時新增`dependencies`元素，並以 remoteconversation-client-sdk 1.8.0 作為相依性：

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. 儲存變更

### <a name="sample-transcription-code"></a>範例轉譯程式碼

在您擁有之後`conversationId`，請在用戶端應用程式建立遠端對話轉譯用戶端**RemoteConversationTranscriptionClient** ，以查詢非同步轉譯的狀態。 使用**RemoteConversationTranscriptionClient**中的**getTranscriptionOperation**方法來取得[PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java)物件。 PollerFlux 物件將會有遠端作業狀態**RemoteConversationTranscriptionOperation**和最終結果**RemoteConversationTranscriptionResult**的相關資訊。 作業完成後，請呼叫[SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java)上的**GetFinalResult**來取得**RemoteConversationTranscriptionResult** 。 在此程式碼中，我們只會將結果內容列印到系統輸出。

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上探索我們的範例](https://aka.ms/csspeech/samples)
