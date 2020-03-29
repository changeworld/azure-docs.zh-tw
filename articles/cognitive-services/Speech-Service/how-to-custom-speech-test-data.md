---
title: 為自訂語音 - 語音服務準備測試資料
titleSuffix: Azure Cognitive Services
description: 在測試 Microsoft 語音辨識的準確性或訓練自訂模型時，您需要音訊和文本資料。 在此頁上，我們將介紹資料類型、如何使用和管理它們。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 969c1450966d2754e6e8f00126da52a1e88181fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942694"
---
# <a name="prepare-data-for-custom-speech"></a>為自訂語音準備資料

在測試 Microsoft 語音辨識的準確性或訓練自訂模型時，您需要音訊和文本資料。 在此頁上，我們將介紹資料類型、如何使用和管理它們。

## <a name="data-types"></a>資料類型

此表列出了可接受的資料類型、應何時使用每種資料類型以及建議的數量。 創建模型並非需要每種資料類型。 資料要求因您創建測試還是訓練模型而異。

| 資料類型 | 用於測試 | 推薦數量 | 用於培訓 | 推薦數量 |
|-----------|-----------------|----------|-------------------|----------|
| [音訊](#audio-data-for-testing) | 是<br>用於目視檢查 | 5+ 音訊檔 | 否 | N/a |
| [音訊 + 人工標記的腳本](#audio--human-labeled-transcript-data-for-testingtraining) | 是<br>用於評估準確性 | 0.5-5 小時的音訊 | 是 | 1-1，000 小時的音訊 |
| [相關文本](#related-text-data-for-training) | 否 | N/a | 是 | 1-200 MB 的相關文本 |

檔應按類型分組到資料集中，並上載為 .ZIP 檔案。 每個資料集只能包含單個資料類型。

> [!TIP]
> 要快速入門，請考慮使用示例資料。 有關<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">自訂語音資料<span class="docon docon-navigate-external x-hidden-focus"></span>的示例</a>，請參閱此 GitHub 存儲庫

## <a name="upload-data"></a>上傳資料

要上傳資料，請導航到<a href="https://speech.microsoft.com/customspeech" target="_blank">自訂語音門戶<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 從門戶中，按一下 **"上傳資料**"以啟動嚮導並創建第一個資料集。 在允許您上傳資料之前，系統將要求您為資料集選擇語音資料類型。

![從語音門戶中選擇音訊](./media/custom-speech/custom-speech-select-audio.png)

您上傳的每個資料集都必須符合您選擇的資料類型的要求。 在上傳資料之前，必須正確格式化資料。 正確格式化的資料可確保自訂語音服務能夠準確處理這些資料。 要求列在以下各節中。

上傳資料集後，有幾個選項：

* 您可以導航到 **"測試"** 選項卡，並直觀地檢查音訊或音訊和人工標記的轉錄資料。
* 您可以導航到 **"訓練"** 選項卡，並使用音訊和人工轉錄資料或相關文本資料來訓練自訂模型。

## <a name="audio-data-for-testing"></a>用於測試的音訊資料

音訊資料是測試 Microsoft 基準語音到文本模型或自訂模型的準確性的最佳選擇。 請記住，音訊資料用於檢查特定模型性能的語音準確性。 如果要量化模型的準確性，請使用[音訊和人工標記的轉錄資料](#audio--human-labeled-transcript-data-for-testingtraining)。

使用此表可確保音訊檔的格式正確，以便與自訂語音一起使用：

| 屬性                 | 值                 |
|--------------------------|-----------------------|
| 檔案格式              | RIFF (WAV)            |
| 採樣速率              | 8，000 Hz 或 16，000 Hz |
| 聲道                 | 1 (mono)              |
| 每個音訊的最大長度 | 2 小時               |
| 樣本格式            | PCM，16 位元           |
| 封存格式           | .zip                  |
| 封存大小上限     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> 上傳訓練和測試資料時，.ZIP 檔案大小不能超過 2 GB。 如果需要更多資料進行培訓，請將其劃分為多個 .ZIP 檔案並單獨上載。 稍後，您可以選擇從*多個*資料集進行訓練。 但是，您只能從*單個*資料集進行測試。

使用<a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX<span class="docon docon-navigate-external x-hidden-focus"></span></a>驗證音訊屬性或將現有音訊轉換為適當的格式。 以下是如何通過 SoX 命令列完成每個這些活動的一些示例：

| 活動 | 描述 | SoX 命令 |
|----------|-------------|-------------|
| 檢查音訊格式 | 使用此命令可檢查<br>音訊檔案格式。 | `sox --i <filename>` |
| 轉換音訊格式 | 使用此命令可轉換<br>音訊檔到單聲道，16位，16 KHz。 | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>用於測試/培訓的音訊和人工標記的腳本資料

要測量 Microsoft 在處理音訊檔時的語音到文本準確性的準確性，必須提供人工標記的轉錄（逐字進行比較）。 雖然人工標記的轉錄通常很耗時，但有必要評估準確性並針對用例訓練模型。 請記住，識別方面的改進將只與提供的資料一樣好。 因此，只上傳高品質的成績單非常重要。

| 屬性                 | 值                               |
|--------------------------|-------------------------------------|
| 檔案格式              | RIFF (WAV)                          |
| 採樣速率              | 8，000 Hz 或 16，000 Hz               |
| 聲道                 | 1 (mono)                            |
| 每個音訊的最大長度 | 2 小時 （測試） / 60 s （培訓） |
| 樣本格式            | PCM，16 位元                         |
| 封存格式           | .zip                                |
| 最大拉鍊大小         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> 上傳訓練和測試資料時，.ZIP 檔案大小不能超過 2 GB。 只能從*單個*資料集進行測試，請確保將其保持在適當的檔案大小內。 此外，每個訓練檔不能超過 60 秒，否則它會出錯。

為了解決單詞刪除或替換等問題，需要大量資料來提高識別性。 通常，建議提供大約 10 到 1，000 小時的音訊逐字轉錄。 所有 WAV 檔案的文字記錄應包含在單一純文字檔案中。 文字記錄檔案的每一行都應包含其中一個音訊檔案的名稱，然後後面接著相對應的文字記錄。 檔案名稱和文字記錄應該以定位字元 (\t) 分隔。

  例如：
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> 文字記錄應使用 UTF-8 位元組順序標記 (BOM) 編碼。

文字記錄會經過文字正規化，以便系統進行處理。 但是，在將資料上載到語音工作室之前，必須執行一些重要的正常化。 有關準備轉錄時要使用的適當語言，請參閱[如何創建人工標記的轉錄](how-to-custom-speech-human-labeled-transcriptions.md)

收集音訊檔和相應的轉錄後，將它們打包為單個 .ZIP 檔案，然後再上載到<a href="https://speech.microsoft.com/customspeech" target="_blank">自訂語音門戶<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 下面是一個包含三個音訊檔和人工標記轉錄檔的示例資料集：

> [!div class="mx-imgBorder"]
> ![從語音門戶中選擇音訊](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>培訓的相關文本資料

產品名稱或功能是唯一的，應包括用於培訓的相關文本資料。 相關文本有助於確保正確識別。 可以提供兩種類型的相關文本資料，以提高識別性：

| 資料類型 | 這些資料如何提高識別性 |
|-----------|------------------------------------|
| 句子（句子） | 在句子上下文中識別產品名稱或行業特定詞彙時提高準確性。 |
| 發音 | 使用未定義的發音改進不常見術語、首字母縮略詞或其他單詞的發音。 |

句子可以作為單個文字檔或多個文字檔提供。 為提高準確性，請使用更接近預期口頭陳述的文本資料。 發音應作為單個文字檔提供。 一切都可以打包為單個 ZIP 檔案，並上傳到<a href="https://speech.microsoft.com/customspeech" target="_blank">自訂語音門戶<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

### <a name="guidelines-to-create-a-sentences-file"></a>創建句子檔的指南

要使用句子創建自訂模型，您需要提供示例陳述的清單。 陳述_不需要_完整或語法正確，但它們必須準確反映您在生產中期望的口頭輸入。 如果希望某些術語具有更高的權重，請添加包含這些特定術語的幾個句子。

作為一般指導，當培訓文本盡可能接近生產中預期的實際文本時，模型適應最為有效。 要增強的特定于域的行話和短語應包含在培訓文本中。 如果可能，請嘗試在單獨的行上控制一個句子或關鍵字。 對於對您重要的關鍵字和短語（例如產品名稱），您可以複製幾次。 但請記住，不要複製太多 - 它可能會影響整體識別率。

使用此表可確保正確格式化相關陳述的資料檔案：

| 屬性 | 值 |
|----------|-------|
| 文字編碼 | UTF-8 BOM |
| 每一行的語句數目 | 1 |
| 檔案大小上限 | 200 MB |

此外，您需要考慮以下限制：

* 避免重複字元超過四次。 例如："aaaa"或"uuuu"。
* 不要使用上面`U+00A1`的特殊字元或 UTF-8 字元。
* URI 將被拒絕。

### <a name="guidelines-to-create-a-pronunciation-file"></a>創建發音檔的指南

如果有不常見的術語，沒有標準發音，您的使用者將遇到或使用，你可以提供一個自訂的發音檔，以提高識別。

> [!IMPORTANT]
> 不建議使用自訂發音檔來更改常用單詞的發音。

這包括口頭陳述的示例，以及每個單詞的自訂發音：

| 識別/顯示表單 | 口語形式 |
|--------------|--------------------------|
| 3CPO | 三個 c p o |
| CNTK | c n t k |
| IEEE | i 三重 e |

口語形式是拼寫出來的拼音序列。它可以由字母、單詞、音節或所有三者的組合組成。

自訂發音有英文 （`en-US`） 和德語`de-DE`（ . 此表按語言顯示受支援的字元：

| 語言 | Locale | 字元 |
|----------|--------|------------|
| 英文 | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| 德文 | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

使用下表確保發音的相關資料檔案格式正確。 發音檔很小，應該只有幾千位元組的大小。

| 屬性 | 值 |
|----------|-------|
| 文字編碼 | UTF-8 BOM（英語也支援 ANSI） |
| 每行發音的 * | 1 |
| 檔案大小上限 | 1 MB（免費套餐 1 KB） |

## <a name="next-steps"></a>後續步驟

* [檢查資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [定型模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
