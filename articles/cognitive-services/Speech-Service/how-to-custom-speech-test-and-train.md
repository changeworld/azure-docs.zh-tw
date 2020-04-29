---
title: 準備自訂語音語音服務的資料
titleSuffix: Azure Cognitive Services
description: 測試 Microsoft 語音辨識的正確性或訓練您的自訂模型時，您將需要音訊和文字資料。 在此頁面上，我們將討論資料類型、如何使用及管理它們。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 78857709447f99895c36f23d8760f44f8468ba7c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402133"
---
# <a name="prepare-data-for-custom-speech"></a>準備自訂語音的資料

測試 Microsoft 語音辨識的正確性或訓練您的自訂模型時，您將需要音訊和文字資料。 在此頁面上，我們將討論資料類型、如何使用及管理它們。

## <a name="data-types"></a>資料類型

此資料表會列出已接受的資料類型、應使用的每個資料類型，以及建議的數量。 建立模型時，不需要每種資料類型。 資料需求會根據您是建立測試或定型模型而有所不同。

| 資料類型 | 用於測試 | 建議數量 | 用於定型 | 建議數量 |
|-----------|-----------------|----------|-------------------|----------|
| [音訊](#audio-data-for-testing) | 是<br>用於視覺檢查 | 5 + 音訊檔案 | 否 | N/a |
| [音訊 + 人為標記的文字記錄](#audio--human-labeled-transcript-data-for-testingtraining) | 是<br>用來評估精確度 | 0.5-5 小時的音訊 | 是 | 1-1000 小時的音訊 |
| [相關文字](#related-text-data-for-training) | 否 | N/a | 是 | 1-200 MB 的相關文字 |

檔案應該依類型分組至資料集，並上傳為 .zip 檔案。 每個資料集只能包含單一資料類型。

> [!TIP]
> 若要快速開始，請考慮使用範例資料。 如需<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">範例自訂語音資料<span class="docon docon-navigate-external x-hidden-focus"></span> </a> ，請參閱此 GitHub 存放庫

## <a name="upload-data"></a>上傳資料

若要上傳您的資料，請流覽至<a href="https://speech.microsoft.com/customspeech" target="_blank">自訂語音入口網站<span class="docon docon-navigate-external x-hidden-focus"></span> </a>。 在入口網站中，按一下 [**上傳資料**] 以啟動精靈，並建立您的第一個資料集。 系統會要求您選取資料集的語音資料類型，然後才允許您上傳資料。

![從語音入口網站選取音訊](./media/custom-speech/custom-speech-select-audio.png)

您上傳的每個資料集都必須符合所選資料類型的需求。 您的資料必須在上傳之前正確格式化。 正確格式化的資料可確保「自訂語音服務」能夠精確地處理它。 下列各節會列出需求。

在您的資料集上傳之後，您有幾個選項：

* 您可以流覽至 [**測試**] 索引標籤，並以視覺化方式檢查 [僅音訊] 或 [音訊 + 人為標記的轉譯資料]
* 您可以流覽至 [**定型**] 索引標籤，並使用音訊 + 人為轉譯資料或相關的文字資料來定型自訂模型。

## <a name="audio-data-for-testing"></a>用於測試的音訊資料

音訊資料是測試 Microsoft 基準語音轉換文字模型或自訂模型精確度的最佳做法。 請記住，音訊資料是用來檢查特定模型效能的語音準確度。 如果您想要量化模型的精確度，請使用[音訊 + 人為標記](#audio--human-labeled-transcript-data-for-testingtraining)的轉譯資料。

使用此表格來確保您的音訊檔案格式正確，可與自訂語音搭配使用：

| 屬性                 | 值                 |
|--------------------------|-----------------------|
| 檔案格式              | RIFF (WAV)            |
| 採樣速率              | 8000 hz 或 16000 Hz |
| 聲道                 | 1 (mono)              |
| 每一音訊的最大長度 | 2 小時               |
| 樣本格式            | PCM，16 位元           |
| 封存格式           | .zip                  |
| 封存大小上限     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> 上傳定型和測試資料時，.zip 檔案大小不能超過 2 GB。 如果您需要更多資料來進行定型，請將其分成數個 .zip 檔案，並分別上傳它們。 稍後，您可以選擇從*多個*資料集進行定型。 不過，您只能從*單一*資料集進行測試。

使用<a href="http://sox.sourceforge.net" target="_blank" rel="noopener">[ <span class="docon docon-navigate-external x-hidden-focus"></span> SoX</a> ] 來驗證音訊內容，或將現有的音訊轉換成適當的格式。 以下是每個活動如何透過 SoX 命令列來完成的一些範例：

| 活動 | 描述 | SoX 命令 |
|----------|-------------|-------------|
| 檢查音訊格式 | 使用此命令來檢查<br>音訊檔案格式。 | `sox --i <filename>` |
| 轉換音訊格式 | 使用此命令轉換<br>音訊檔案到單聲道，16位，16 KHz。 | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>音訊 + 用於測試/定型的人為標記的文字記錄資料

若要在處理您的音訊檔案時測量 Microsoft 的語音轉換文字精確度準確度，您必須提供人為標記的轉譯（單字）以進行比較。 雖然人加上標記的轉譯經常耗用時間，但還是必須評估精確度，並針對您的使用案例來定型模型。 請記住，辨識的改進只會與所提供的資料一樣好。 基於這個理由，請務必只上傳高品質的文字記錄。

| 屬性                 | 值                               |
|--------------------------|-------------------------------------|
| 檔案格式              | RIFF (WAV)                          |
| 採樣速率              | 8000 hz 或 16000 Hz               |
| 聲道                 | 1 (mono)                            |
| 每一音訊的最大長度 | 2小時（測試）/60 秒（訓練） |
| 樣本格式            | PCM，16 位元                         |
| 封存格式           | .zip                                |
| 最大 zip 大小         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> 上傳定型和測試資料時，.zip 檔案大小不能超過 2 GB。 您只能從*單一*資料集進行測試，請務必將它保留在適當的檔案大小內。 此外，每個定型檔案不能超過60秒，否則會發生錯誤。

若要解決 word 刪除或替代等問題，需要大量的資料來改善辨識。 一般來說，建議您提供大約10到1000小時音訊的單字文字轉譯。 所有 WAV 檔案的文字記錄應包含在單一純文字檔案中。 文字記錄檔案的每一行都應包含其中一個音訊檔案的名稱，然後後面接著相對應的文字記錄。 檔案名稱和文字記錄應該以定位字元 (\t) 分隔。

  例如：
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> 文字記錄應使用 UTF-8 位元組順序標記 (BOM) 編碼。

文字記錄會經過文字正規化，以便系統進行處理。 不過，在將資料上傳到語音 Studio 之前，必須先完成一些重要的正規化。 若要在準備轉譯時使用適當的語言，請參閱[如何建立人為標記](how-to-custom-speech-human-labeled-transcriptions.md)的轉譯

收集音訊檔案和對應的轉譯之後，請將它們封裝成單一 .zip 檔案，然後再上傳至<a href="https://speech.microsoft.com/customspeech" target="_blank">自訂語音<span class="docon docon-navigate-external x-hidden-focus"></span>入口網站</a>。 以下是包含三個音訊檔案和人為標記轉譯檔案的範例資料集：

> [!div class="mx-imgBorder"]
> ![從語音入口網站選取音訊](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>定型的相關文字資料

唯一的產品名稱或功能應該包含用於定型的相關文字資料。 相關的文字有助於確保正確辨識。 可以提供兩種類型的相關文字資料來改善辨識：

| 資料類型 | 此資料如何改善辨識 |
|-----------|------------------------------------|
| 句子（語句） | 在辨識產品名稱或句子內容中的產業特定詞彙時，改善正確性。 |
| 發音 | 使用未定義的發音來改善不常見詞彙、縮略字或其他單字的發音。 |

句子可以當做單一文字檔或多個文字檔來提供。 若要改善正確性，請使用更接近預期說話語句的文字資料。 發音應以單一文字檔的形式提供。 所有專案都可以封裝成單一 zip 檔案，並上傳至<a href="https://speech.microsoft.com/customspeech" target="_blank">自訂<span class="docon docon-navigate-external x-hidden-focus"></span>語音入口網站</a>。

### <a name="guidelines-to-create-a-sentences-file"></a>建立句子檔案的指導方針

若要使用句子建立自訂模型，您必須提供範例語句的清單。 語句_不_需要完整或語法正確，但必須精確地反映您預期在生產環境中的說話輸入。 如果您想要特定詞彙的權數增加，請新增數個包含這些特定詞彙的句子。

作為一般指引，當定型文字盡可能接近生產環境中所預期的實際文字時，模型調整就最有效。 您要增強的目標網域特定術語和片語應包含在訓練文字中。 可能的話，請嘗試在個別行上控制一個句子或關鍵字。 對於您很重要的關鍵字和片語（例如，產品名稱），您可以將它們複製幾次。 但請記住，不要複製太多-它可能會影響整體辨識速率。

請使用此資料表來確保語句的相關資料檔案格式正確：

| 屬性 | 值 |
|----------|-------|
| 文字編碼 | UTF-8 BOM |
| 每一行的語句數目 | 1 |
| 檔案大小上限 | 200 MB |

此外，您會想要考慮下列限制：

* 避免重複字元超過四次。 例如： "aaaa" 或 "uuuu"。
* 請不要使用上述`U+00A1`的特殊字元或 utf-8 字元。
* Uri 將會遭到拒絕。

### <a name="guidelines-to-create-a-pronunciation-file"></a>建立發音檔案的指導方針

如果有不尋常的詞彙，而您的使用者將會遇到或使用標準發音，您可以提供自訂發音檔案來改善辨識。

> [!IMPORTANT]
> 不建議使用自訂發音檔案來改變常用單字的發音。

這包括說語句的範例，以及每個的自訂發音：

| 已辨識/已顯示的表單 | 口語形式 |
|--------------|--------------------------|
| 3CPO | 三個 c p o |
| CNTK | c n t k |
| IEEE | 我三個 e |

說出的形式就是語音順序。它可以包含字母、字組、音節或全部三種組合。

自訂發音提供英文（`en-US`）和德文（`de-DE`）。 下表依語言顯示支援的字元：

| Language | Locale | 字元 |
|----------|--------|------------|
| 英文 | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| 德文 | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

請使用下表來確保發音的相關資料檔案格式正確。 發音檔案很小，而且應該只有幾 kb 的大小。

| 屬性 | 值 |
|----------|-------|
| 文字編碼 | UTF-8 BOM （ANSI 也支援英文） |
| 每行的發音數 | 1 |
| 檔案大小上限 | 1 MB （免費層為 1 KB） |

## <a name="next-steps"></a>後續步驟

* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [定型模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
