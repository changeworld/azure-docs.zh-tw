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
ms.custom: devx-track-csharp
ms.openlocfilehash: 3c8d3162e13c31204ed317edc653756b04ef8dd4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934118"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>關於語音 SDK 音訊輸入資料流 API

語音 SDK 的 **音訊輸入串流** API 提供了將音訊串流到辨識器中的方法，而不是使用麥克風或輸入檔 api。

使用音訊輸入資料流時，必須執行下列步驟：

- 識別音訊資料流的格式。 語音 SDK 和語音服務必須支援此格式。 目前僅支援下列設定：

  PCM 格式的音訊範例、一個通道、每個樣本16個位、每秒8000或16000個樣本 (16000 或每秒32000位元組) 、兩個區塊對齊 (16 位，包括樣本) 的填補。

  SDK 中用於建立音訊格式相對應程式碼看起來像這樣：

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000; // or 8000
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

- [建立 Azure 免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
- [了解如何以 C# 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) (英文)
