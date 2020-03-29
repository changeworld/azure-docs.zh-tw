---
title: 語音 SDK 音訊輸入資料流概念
titleSuffix: Azure Cognitive Services
description: 語音 SDK 音訊輸入資料流 API 功能概觀。
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74109945"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>關於語音 SDK 音訊輸入資料流 API

語音 SDK 的**音訊輸入流**API 提供了一種將音訊資料流到識別器的方法，而不是使用麥克風或輸入檔 API。

使用音訊輸入資料流時，必須執行下列步驟：

- 識別音訊資料流的格式。 語音 SDK 和語音服務必須支援此格式。 目前僅支援下列設定：

  PCM 格式、一個通道、每秒 16000 個樣本、每秒 32000 個位元組、兩個區塊對齊 (一個樣本 16 位元，包含填補)、每個樣本 16 位元 的音訊樣本。

  SDK 中用於建立音訊格式相對應程式碼看起來像這樣：

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- 請確定您的程式碼可以根據這些規格提供未經處理的音訊資料。 如果您的音訊來源資料不符合支援的格式，則必須將音訊轉碼成所需的格式。

- 建立自己衍生自 `PullAudioInputStreamCallback` 的音訊輸入資料流類別。 實作`Read()` 和 `Close()` 成員。 確切的函式簽章視語言而定，但程式碼看起來類似此程式碼範例：

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- 根據您的音訊格式與輸入資料流建立音訊設定。 在建立自己的辨識器時，請傳入一般語音設定和音訊輸入設定。 例如：

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
- [了解如何以 C# 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) (英文)
