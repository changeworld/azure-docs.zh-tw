---
title: 長音訊 API（預覽） - 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解長音訊 API 如何設計為將長格式文本非同步合成到語音。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.openlocfilehash: 033103e10971be2f6c220ccdb2c3586c7dc2ef05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76900258"
---
# <a name="long-audio-api-preview"></a>長音訊 API（預覽版）

長音訊 API 設計用於將長格式文本非同步合成到語音（例如：有聲讀物）。 此 API 不會即時返回合成音訊，而是期望您輪詢回應並消耗從服務中提供的輸出。 與語音 SDK 使用的文本到語音 API 不同，長音訊 API 可以創建超過 10 分鐘的合成音訊，使其成為發行者和音訊內容平臺的理想選擇。

長音訊 API 的其他優勢：

* 服務返回的合成語音使用神經語音，確保高保真音訊輸出。
* 由於不支援即時回應，因此無需部署語音終結點。

> [!NOTE]
> 長音訊 API 現在僅支援[自訂神經語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)。

## <a name="workflow"></a>工作流程

通常，使用長音訊 API 時，您將提交要合成的文字檔或檔，輪詢狀態，然後，如果狀態成功，則可以下載音訊輸出。

此圖提供了工作流的高級概述。

![長音訊 API 工作流圖](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>準備供合成的內容

準備文字檔時，請確保：

* 是純文字 （.txt） 或 SSML 文本 （.txt）
* 編碼為[UTF-8，帶位元組訂單標記 （BOM）](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* 是單個檔，而不是 zip
* 包含 400 多個字元的純文字或 400 個[計費字元](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note)的 SSML 文本，以及少於 10，000 個段落
  * 對於純文字，每個段落通過點擊 **"輸入/返回**- 查看[純文字輸入"示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)進行分隔
  * 對於 SSML 文本，每個 SSML 部分都被視為段落。 SSML 部分應按不同的段落分隔 - 查看[SSML 文本輸入示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> 對於中文（大陸）、中文（香港）、中文（臺灣）、日語和韓語，一個單詞將計為兩個字元。 

## <a name="submit-synthesis-requests"></a>提交綜合請求

準備輸入內容後，按照[長格式音訊合成快速啟動](https://aka.ms/long-audio-python)提交請求。 如果您有多個輸入檔，則需要提交多個請求。 需要注意一些限制： 
* 允許用戶端為每個 Azure 訂閱帳戶每秒向伺服器提交最多 5 個請求。 如果超出限制，用戶端將獲得 429 錯誤代碼（請求太多）。 請減少每秒的請求金額
* 允許伺服器為每個 Azure 訂閱帳戶運行和排隊多達 120 個請求。 如果超過限制，伺服器將返回 429 錯誤代碼（請求太多）。 請等待並避免提交新請求，直到某些請求完成
* 伺服器將為每個 Azure 訂閱帳戶保留多達 20，000 個請求。 如果超過限制，請在提交新請求之前刪除一些請求

## <a name="audio-output-formats"></a>音訊輸出格式

我們支援靈活的音訊輸出格式。 您可以通過設置"串聯Result"參數，生成每個段落的音訊輸出或將音訊串聯到一個輸出中。 長音訊 API 支援以下音訊輸出格式：

> [!NOTE]
> 預設音訊格式為 riff-16khz-16 位單聲道 pcm。

* 裡夫-8khz-16位單位pcm
* 裡夫-16khz-16位單件
* riff-24khz-16 位單位 pcm
* riff-48khz-16位單位pcm
* 音訊-16khz-32kbitrate-單聲道mp3
* 音訊-16khz-64kbitrate-單聲道mp3
* 音訊-16khz-128kbitrate-單聲道mp3
* 音訊-24khz-48kbitrate-單聲道mp3
* 音訊-24khz-96kbitrate-單聲道mp3
* 音訊-24khz-160kbitrate-單聲道mp3

## <a name="quickstarts"></a>快速入門

我們提供快速入門，旨在説明您成功運行長音訊 API。 此表包括按語言組織的長音訊 API 快速啟動的清單。

* [快速入門：Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>範例程式碼
GitHub 上提供了長音訊 API 的示例代碼。

* [示例代碼：Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [示例代碼： C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [示例代碼：JAVA](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
