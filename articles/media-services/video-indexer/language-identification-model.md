---
title: 使用視訊索引器自動辨識埠語 - Azure
titleSuffix: Azure Media Services
description: 本文介紹如何使用視頻索引器語言標識模型自動標識視頻中的口語。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687131"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>使用語言識別模型自動識別口語

視頻索引器支援自動語言識別 (LID),這是從音訊自動辨識口語內容以及發送媒體檔以主要標識語言轉錄的過程。 

目前LID支援:英語、西班牙文、法語、德語、義大利語、普通話、日語、俄語和葡萄牙文(巴西)。 

請務必查看下面的[「指南和限制」](#guidelines-and-limitations)部分。

## <a name="choosing-auto-language-identification-on-indexing"></a>在索引時選擇自動語言識別

使用 API 對影片編制索引或[重新索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)時`auto detect`,`sourceLanguage`在參數中選擇 該 選項。

使用門戶時,轉到[視頻索引器](https://www.videoindexer.ai/)主頁上的**帳戶視頻**,並將滑鼠懸停在要重新索引的視頻名稱上。 在右下角按一下重新索引按鈕。 在 **「重新索引視頻」** 對話方塊中,從 **「視頻源語言**」下拉框中選擇 *「自動偵測*」。

![自動偵測](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>模型輸出

如果對該語言的信心是`> 0.6`,則視頻索引器會根據最可能的語言轉錄視頻。 如果語言不能自信地識別,則假定口語是英語。 

模型主導語言在見解 JSON`sourceLanguage`中作為 屬性(在根/視頻/見解下)提供。 屬性下`sourceLanguageConfidence`也提供了相應的置信度分數。

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

* 自動語言辨識 (LID) 支援以下語言: 

    英語、西班牙文、法語、德語、義大利語、普通話、日語、俄語和葡萄牙文(巴西文)。
* 儘管視訊索引器支援阿拉伯文(現代標準和 Levantine)、印地語和韓語,但 LID 中不支援這些語言。
* 如果音訊包含上面支援清單以外的語言,則結果是意外的。
* 如果視訊索引器無法以足夠高的置信度`>0.6`() 標識語言),則回退語言為英語。
* 當前不支援具有混合語言音訊的檔。 如果音訊包含混合語言,則結果為意外。 
* 低品質音頻可能會影響模型結果。
* 該模型需要音訊中至少一分鐘的語音。
* 該模型旨在識別自發的對話語音(而不是語音命令、唱歌等)。

## <a name="next-steps"></a>後續步驟

* [概觀](video-indexer-overview.md)
* [自動辨識並轉錄多語言內容](multi-language-identification-transcription.md)
