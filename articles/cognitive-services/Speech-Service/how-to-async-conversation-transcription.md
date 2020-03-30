---
title: 非同步對話轉錄（預覽） - 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用語音服務使用非同步對話轉錄。 僅適用于 JAVA。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 57543f4a3779145ce66259eec1abac195b63c7ba
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384291"
---
# <a name="asynchronous-conversation-transcription-preview"></a>非同步對話轉錄（預覽）

在本文中，使用**遠端對話轉錄用戶端**API 演示非同步對話轉錄。 如果已配置對話轉錄以執行非同步轉錄，並且具有`conversationId`，則可以使用`conversationId`**遠端對話轉錄用戶端**API 獲取與此關聯的轉錄。

## <a name="asynchronous-vs-real-time--asynchronous"></a>非同步與即時 + 非同步

使用非同步轉錄，您可以資料流對話音訊，但不需要即時返回轉錄。 相反，在發送音訊後，使用 查詢`conversationId``Conversation`的非同步轉錄的狀態。 當非同步轉錄準備就緒時，您將獲得一個`RemoteConversationTranscriptionResult`。

使用即時加上非同步，您可以即時獲取轉錄，但也通過查詢`conversationId`（類似于非同步方案）獲得轉錄。

完成非同步轉錄需要兩個步驟。 第一步是上傳音訊，選擇僅非同步或即時加非同步。 第二步是獲取轉錄結果。

## <a name="upload-the-audio"></a>上傳音訊

在可以執行非同步轉錄之前，您需要使用 Microsoft 認知語音用戶端 SDK（版本 1.8.0 或以上）將音訊發送到對話轉錄服務。

此示例代碼演示如何為僅非同步模式創建對話轉錄器。 為了將音訊資料流到轉錄器，您需要[使用語音 SDK 即時添加從轉錄對話](how-to-use-conversation-transcription-service.md)派生的音訊流代碼。 請參閱該主題的 **"限制"** 部分，查看支援的平臺和語言 API。

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

如果需要即時_加上_非同步，請注釋和取消注釋相應的程式碼，如下所示：

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>獲取轉錄結果

此步驟獲取非同步轉錄結果，但假定可能需要的任何即時處理都已完成其他位置。 有關詳細資訊，請參閱[使用語音 SDK 即時轉錄對話](how-to-use-conversation-transcription-service.md)。

對於此處顯示的代碼，您需要**遠端對話版本 1.8.0**，僅支援 Windows、Linux 和 Android 上的 JAVA（1.8.0 或以上）（僅限 API 級別 26 或以上）。

### <a name="obtaining-the-client-sdk"></a>獲取用戶端 SDK

您可以通過編輯 pom.xml 檔來獲取**遠端對話**，如下所示。

1. 在檔末尾，在結束標記`</project>`之前，創建一個`repositories`元素，其中引用語音 SDK 的 Maven 存儲庫：

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. 還添加一`dependencies`個元素，遠端對話用戶端-sdk 1.8.0作為依賴項：

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. 保存更改

### <a name="sample-transcription-code"></a>示例轉錄代碼

之後，`conversationId`在用戶端應用程式中創建遠端對話轉錄用戶端**RemoteConversation 轉錄用戶端**，以查詢非同步轉錄的狀態。 在**遠端對話腳本用戶端**中使用**get 轉錄操作**方法獲取[PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java)物件。 PollerFlux 物件將包含有關遠端操作狀態**遠端對話轉錄操作**和最終結果**遠端對話轉錄結果的資訊**。 操作完成後，通過在[SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java)上調用**getFinalResult**獲取**遠端對話記錄結果**。 在此代碼中，我們只需將結果內容列印到系統輸出。

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
