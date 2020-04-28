---
title: 使用影片索引子自動識別和轉譯多語言內容
titleSuffix: Azure Media Services
description: 本主題示範如何使用影片索引子來自動識別和轉譯多語言內容。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "72968747"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>自動識別並轉譯多語言內容（預覽）

影片索引子支援多語言內容中的自動語言識別和轉譯。 此程式牽涉到從音訊的不同區段自動識別說話語言，傳送要轉譯之媒體檔案的每個區段，並將轉譯方式合併回一個整合轉譯。 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>選擇使用入口網站編制索引時的多語系識別

您可以在上傳影片並為其編制索引時選擇**多語言偵測**。 或者，您也可以在重新編制影片索引時選擇**多語言偵測**。 下列步驟說明如何重新編制索引：

1. 瀏覽至[影片索引子](https://vi.microsoft.com/)網站並登入。
1. 移至 [連結**庫**] 頁面，將滑鼠停留在您想要重新編制索引的影片名稱上。 
1. 按一下右下角的 [**重新編制索引] 影片**按鈕。 
1. 在 [**重新編制索引] 影片**對話方塊中，從 [**影片來來源語言**] 下拉式方塊中選擇 [**多語言偵測**]。

    * 當影片以多國語言編制索引時，[深入解析] 頁面將會包含該選項，並會出現額外的深入解析類型，讓使用者能夠查看哪些區段是轉譯的語言「語音語言」。
    * 您可以從多語言文字記錄中完整取得所有語言的翻譯。
    * 所有其他見解會以偵測到的主要語言顯示，也就是最常出現在音訊中的語言。
    * 播放播放機上的隱藏式字幕也適用于多國語言。

![入口網站體驗](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>選擇使用 API 編制索引時的多語系識別

使用 API 編制影片的索引或重新[編制](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)索引時， `multi-language detection`請選擇`sourceLanguage`參數中的選項。

### <a name="model-output"></a>模型輸出

此模型會以一份清單來抓取影片中偵測到的所有語言

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

此外，轉譯區段中的每個實例都將包含其轉譯的語言

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>指導方針和限制

* 一組支援的語言：英文、法文、德文、西班牙文。
* 支援具有最多三種支援語言的多語言內容。
* 如果音訊包含上述支援清單以外的語言，則會產生非預期的結果。
* 每種語言偵測的最社區段長度–15秒。
* 語言偵測位移平均為3秒。
* 語音應該是連續的。 在語言之間經常替代可能會影響模型效能。
* 非原生喇叭的語音可能會影響模型效能（例如，當說話者使用其原生吐和切換到其他語言時）。
* 此模型是設計用來辨識具有合理聲場的自發交談語音（而非語音命令、唱歌等等）。
* 專案建立和編輯目前無法供多語言影片使用。
* 使用多語言偵測時，無法使用自訂語言模型。
* 不支援新增關鍵字。
* 匯出隱藏式輔助字幕檔案時，將不會出現語言指示。
* 更新文字記錄 API 不支援多種語言的檔案。

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)
