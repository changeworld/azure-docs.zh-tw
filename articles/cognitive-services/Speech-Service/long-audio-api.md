---
title: 長音訊 API （預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何針對長格式文字轉換語音的非同步合成，進行長音訊 API 的設計。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81401065"
---
# <a name="long-audio-api-preview"></a>長音訊 API （預覽）

較長的音訊 API 是針對長格式文字轉換語音的非同步合成（例如：「音訊書籍」）而設計。 此 API 不會即時傳回合成的音訊，而是預期您會輪詢回應，並取用輸出（從服務中取得），而不會有任何結果。 不同于語音 SDK 所使用的文字轉換語音 API，較長的音訊 API 可以建立超過10分鐘的合成音訊，使其適合用於發行者和音訊內容平臺。

較長音訊 API 的其他優點：

* 服務所傳回的合成語音會使用類神經語音，以確保高精確度的音訊輸出。
* 由於不支援即時回應，因此不需要部署語音端點。

> [!NOTE]
> 長音訊 API 現在僅支援[自訂類神經語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)。

## <a name="workflow"></a>工作流程

一般而言，使用完整的音訊 API 時，您會提交要合成的文字檔、輪詢狀態，然後如果狀態為 [成功]，則您可以下載音訊輸出。

此圖表提供工作流程的高階總覽。

![長音訊 API 工作流程圖表](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>準備內容以進行合成

準備您的文字檔時，請確定它：

* 這是純文字（.txt）或 SSML 文字（.txt）
* [使用位元組順序標記（BOM）](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)編碼為 utf-8
* 是單一檔案，而不是 zip
* 針對 SSML 文字，包含超過400個字元的純文字或400個可[計費字元](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note)，且小於10000個段落
  * 若為純文字，則會叫用**Enter/Return** -View[純文字輸入範例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)來分隔每個段落
  * 針對 SSML 文字，每個 SSML 片段都會被視為一個段落。 SSML 片段應以不同段落分隔-View [SSML 文字輸入範例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> 若為中文（大陸）、中文（香港特別行政區）、中文（臺灣）、日文和韓文，則會將一個單字計為兩個字元。 

## <a name="submit-synthesis-requests"></a>提交合成要求

準備輸入內容之後，請遵循[長格式的音訊合成快速入門](https://aka.ms/long-audio-python)來提交要求。 如果您有多個輸入檔案，就必須提交多個要求。 有一些限制需要注意： 
* 針對每個 Azure 訂用帳戶，允許用戶端每秒提交最多5個對伺服器的要求。 如果超過限制，用戶端會收到429錯誤碼（太多要求）。 請減少每秒的要求數量
* 允許伺服器執行，並將每個 Azure 訂用帳戶的最多120個要求排入佇列。 如果超過限制，伺服器會傳回429錯誤碼（太多要求）。 請等候並避免提交新的要求，直到部分要求完成
* 伺服器會針對每個 Azure 訂用帳戶保留最多20000個要求。 如果超過限制，請在提交新要求之前先刪除

## <a name="audio-output-formats"></a>音訊輸出格式

我們支援彈性的音訊輸出格式。 您可以藉由設定 ' concatenateResult ' 參數，為每個段落產生音訊輸出，或將音訊串連成一個輸出。 較長的音訊 API 支援下列音訊輸出格式：

> [!NOTE]
> 預設的音訊格式為 riff-riff-16khz-16bit-mono-pcm-dxil 16 位-mono-pcm。

* riff-8khz-dxil 16 位-mono-pcm
* riff-riff-16khz-16bit-mono-pcm-dxil 16 位-mono-pcm
* riff-24khz-dxil 16 位-mono-pcm
* riff-48khz-dxil 16 位-mono-pcm
* 音訊-riff-16khz-16bit-mono-pcm-32kbitrate-單聲道-mp3
* 音訊-riff-16khz-16bit-mono-pcm-64kbitrate-單聲道-mp3
* 音訊-riff-16khz-16bit-mono-pcm-128kbitrate-單聲道-mp3
* 音訊-24khz-48kbitrate-單聲道-mp3
* 音訊-24khz-96kbitrate-單聲道-mp3
* 音訊-24khz-160kbitrate-單聲道-mp3

## <a name="quickstarts"></a>快速入門

我們提供的快速入門旨在協助您順利執行長音訊 API。 此表格包含以語言組織的長音訊 API 快速入門清單。

* [快速入門： Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>範例程式碼
長音訊 API 的範例程式碼可在 GitHub 上取得。

* [範例程式碼： Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [範例程式碼： C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [範例程式碼： JAVA](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
