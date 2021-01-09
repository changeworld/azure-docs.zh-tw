---
title: 使用影片索引子自動識別及轉譯多國語言內容
titleSuffix: Azure Media Services
description: 本主題示範如何使用影片索引子，自動識別及轉譯多語言內容。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 657ccafa0e7b7f640122fd6b397b3fa2a7c5f0fc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015550"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>自動識別並轉譯多語言內容

影片索引子支援在多語言內容中進行自動語言識別和轉譯。 此程式牽涉到自動從音訊識別不同區段中的語音語言，傳送媒體檔案的每個區段以進行轉譯，並將轉譯合併回一個統一轉譯。 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>選擇使用入口網站編制索引時的多語系識別

您可以在上傳影片並為影片編制索引時，選擇 **多重語言偵測** 。 或者，您可以在重新編制影片的索引時選擇 **多重語言偵測**  。 下列步驟說明如何重新索引：

1. 瀏覽至[影片索引子](https://vi.microsoft.com/)網站並登入。
1. 移至 [連結 **庫** ] 頁面，並將滑鼠停留在您想要重新索引的影片名稱上方。 
1. 在右下角，按一下 [ **重新索引] 影片** 按鈕。 
1. 在 [**重新索引影片**] 對話方塊中，從 [**影片來來源語言**] 下拉式方塊中選擇 [**多語言偵測**]。

    * 當影片以多國語言編制索引時，[深入解析] 頁面會包含該選項，並會顯示其他深入解析類型，讓使用者能夠查看哪個區段是以何種語言的語言「說話語言」來轉譯。
    * 所有語言的翻譯都可從多語言文字記錄中完整取得。
    * 所有其他見解都會出現在偵測到的主要語言中-這是最常出現在音訊中的語言。
    * 播放程式上的隱藏式字幕也提供多種語言。

![入口網站體驗](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>使用 API 在編制索引時選擇多語系識別

使用 API 對影片編制索引或重新 [編制](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 索引時，請選擇 `multi-language detection` 參數中的選項 `sourceLanguage` 。

### <a name="model-output"></a>模型輸出

此模型將會在一個清單中取出影片中偵測到的所有語言

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

此外，轉譯區段中的每個實例都會包含其轉譯的語言。

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
* 支援多國語言的內容，最多可支援三種語言。
* 如果音訊包含上述支援清單以外的語言，則會產生非預期的結果。
* 要針對每個語言（15秒）偵測的基本區段長度。
* 語言偵測位移平均為3秒。
* 語音預期會是連續的。 語言之間的頻繁替代可能會影響模型的效能。
* 非原生說話者的語音可能會影響模型效能 (例如，當喇叭使用其原生吐和切換至另一種語言) 時。
* 此模型是設計用來辨識具有合理音訊聲場的自發對話語音， (非語音命令、唱歌等 ) 。
* 目前無法在多國語言的影片中使用專案建立和編輯。
* 使用多重語言偵測時，無法使用自訂語言模型。
* 不支援新增關鍵字。
* 匯出隱藏式輔助字幕檔案時，將不會出現語言指示。
* 更新文字記錄 API 不支援多種語言檔案。

## <a name="next-steps"></a>後續步驟

[影片索引子概觀](video-indexer-overview.md)
