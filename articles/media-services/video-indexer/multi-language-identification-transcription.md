---
title: 使用視頻索引子自動識別和轉錄多語言內容
titleSuffix: Azure Media Services
description: 本主題演示如何使用視頻索引子自動識別和轉錄多語言內容。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72968747"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>自動識別和轉錄多語言內容（預覽）

視頻索引子支援多語言內容中的自動語言識別和轉錄。 此過程涉及從音訊自動識別不同段中的口語，發送要轉錄的每個介質檔的段，並將轉錄合併回一個統一轉錄。 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>使用門戶索引時選擇多語言標識

在上傳視頻並編制索引視頻時，您可以選擇**多語言檢測**。 或者，您可以在重新索引視頻時選擇**多語言檢測**。 以下步驟描述了如何重新編制索引：

1. 瀏覽至[影片索引子](https://vi.microsoft.com/)網站並登入。
1. 轉到 **"庫"** 頁面，並將滑鼠懸停在要重新索引的視頻名稱上。 
1. 在右下角，按一下 **"重新索引視頻**"按鈕。 
1. 在 **"重新索引視頻"對話方塊**中，從 **"視頻來源語言**"下拉清單中選擇**多語言檢測**。

    * 當視頻索引為多語言時，見解頁將包含該選項，並會出現其他見解類型，使使用者能夠查看以哪種語言"口語"轉錄的分段。
    * 從多語言成績單中可以完全翻譯所有語言。
    * 所有其他見解將以檢測到的主語言顯示- 即音訊中出現最多的語言。
    * 播放機上的隱藏字幕也可使用多語言版本。

![入口網站體驗](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>使用 API 索引時選擇多語言標識

使用 API 對視頻編制索引或[重新編制索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)時，在`multi-language detection`參數中選擇該`sourceLanguage`選項。

### <a name="model-output"></a>模型輸出

模型將在一個清單中檢索視頻中檢測到的所有語言

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

此外，轉錄部分中的每個實例都將包括轉錄它的語言

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

* 支援語言集：英語、法語、德語、西班牙文。
* 支援最多包含三種受支援語言的多語言內容。
* 如果音訊包含上面支援清單以外的語言，則結果是意外的。
* 每種語言檢測的段長度最小 = 15 秒。
* 語言檢測偏移量平均為 3 秒。
* 演講是連續的。 語言之間的頻繁交替可能會影響模型的性能。
* 非母語人士的語音可能會影響模型性能（例如，當消費者使用母語並切換到其他語言時）。
* 該模型旨在識別具有合理音訊聲學（而不是語音命令、唱歌等）的自發對話語音。
* 專案創建和編輯目前不適用於多語言視頻。
* 使用多語言檢測時，自訂語言模型不可用。
* 不支援添加關鍵字。
* 匯出隱藏字幕檔時，語言指示將不會顯示。
* 更新腳本 API 不支援多種語言檔。

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)
