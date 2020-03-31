---
title: 使用視頻索引子自動識別口語 - Azure
titleSuffix: Azure Media Services
description: 本文介紹如何使用視頻索引子語言標識模型自動標識視頻中的口語。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 7a2e03b8dacbf6c3ff20e02c804804b671e86d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513876"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>使用語言識別模型自動識別口語

視頻索引子支援自動語言識別 （LID），這是從音訊自動識別口語內容以及發送媒體檔案以主要標識語言轉錄的過程。 目前，LID 支援英語、西班牙文、法語、德語、義大利文、中文（簡體）、日語、俄語和葡萄牙文（巴西語）。 

## <a name="choosing-auto-language-identification-on-indexing"></a>在索引時選擇自動語言標識

使用 API 對視頻編制索引或[重新索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)時，在參數中選擇`auto detect`該`sourceLanguage`選項。

使用門戶時，轉到[視頻索引子](https://www.videoindexer.ai/)主頁上的**帳戶視頻**，並將滑鼠懸停在要重新索引的視頻名稱上。 在右下角按一下重新索引按鈕。 在 **"重新索引視頻"** 對話方塊中，從 **"視頻來源語言**"下拉清單中選擇 *"自動檢測*"。

![自動檢測](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>模型輸出

如果對該語言的信心是`> 0.6`，則視頻索引子會根據最可能的語言轉錄視頻。 如果語言不能自信地識別，則假定口語是英語。 

模型主導語言在見解 JSON 中作為`sourceLanguage`屬性（在根/視頻/見解下）提供。 屬性下`sourceLanguageConfidence`也提供了相應的置信度分數。

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>指導方針和限制

* 支援的語言包括英語、西班牙文、法語、德語、義大利文、中文（簡體）、日語、俄語和巴西葡萄牙文。
* 如果音訊包含上面支援清單以外的語言，則結果是意外的。
* 如果視頻索引子無法以足夠高的置信度 （）`>0.6`標識語言），則遞補語言為英語。
* 當前不支援具有混合語言音訊的檔。 如果音訊包含混合語言，則結果為意外。 
* 低品質音訊可能會影響模型結果。
* 該模型需要音訊中至少一分鐘的語音。
* 該模型旨在識別自發的對話語音（而不是語音命令、唱歌等）。

## <a name="next-steps"></a>後續步驟

* [概觀](video-indexer-overview.md)
* [自動識別和轉錄多語言內容](multi-language-identification-transcription.md)
