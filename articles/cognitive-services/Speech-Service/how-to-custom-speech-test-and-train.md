---
title: 準備自訂語音-語音服務的資料
titleSuffix: Azure Cognitive Services
description: 測試 Microsoft 語音辨識的精確度或訓練自訂模型時，您需要音訊和文字資料。 在這個頁面中，我們將討論資料類型、如何使用和管理它們。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: af5ed0296ce99a4450fffec6b047285307ed0ff2
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2020
ms.locfileid: "97709294"
---
# <a name="prepare-data-for-custom-speech"></a>準備自訂語音的資料

測試 Microsoft 語音辨識的精確度或訓練自訂模型時，您需要音訊和文字資料。 在此頁面上，我們會討論自訂語音模型所需的資料類型。

## <a name="data-diversity"></a>資料多樣性

用來測試和定型自訂模型的文字和音訊，需要包含一組不同的喇叭和案例的範例，您需要模型才能辨識。
在收集自訂模型測試和定型的資料時，請考慮下列因素：

* 您的文字和語音音訊資料必須涵蓋您的使用者在與模型互動時所要進行的口頭語句類型。 例如，引發並降低溫度需求的模型，可能會要求使用者提出這類變更。
* 您的資料必須包含您的模型需要辨識的所有語音差異。 許多因素可能會改變語音，包括重音、方言、語言混合、年齡、性別、語音音調、壓力等級和當日時間。
* 您必須包含不同環境的範例 (室內、戶外、公路雜訊) 將使用您的模型。
* 音訊必須使用生產系統將使用的硬體裝置來收集。 如果您的模型需要找出記錄在品質不同的裝置上的語音，您提供來定型模型的音訊資料也必須代表這些不同的案例。
* 您稍後可以將更多資料新增至您的模型，但請小心保持資料集的多樣性，並代表您的專案需求。
* 包含 *不* 在自訂模型辨識需求中的資料可能會危害整體辨識品質，因此請勿包含您的模型不需要轉譯的資料。

以案例子集定型的模型只能在這些案例中順利執行。 請小心選擇資料，以代表您需要自訂模型辨識的完整案例範圍。

> [!TIP]
> 從符合您模型所遇到之語言和聲場的小型樣本資料集開始。
> 例如，在相同的硬體上，或是在生產案例中，您的模型會在相同的聲場環境中，記錄小型但具代表性的音訊範例。
> 小型資料集的代表性資料可能會在您投資收集更大型的資料集進行定型之前，暴露問題。

## <a name="data-types"></a>資料類型

此資料表會列出已接受的資料類型、應使用的每個資料類型，以及建議的數量。 建立模型時，不需要每種資料類型。 資料需求會根據您是建立測試或定型模型而有所不同。

| 資料類型 | 用於測試 | 建議數量 | 用於定型 | 建議數量 |
|-----------|-----------------|----------|-------------------|----------|
| [音訊](#audio-data-for-testing) | 是<br>用於視覺化檢查 | 5 + 音訊檔案 | 否 | N/A |
| [音訊 + 人類標記的文字記錄](#audio--human-labeled-transcript-data-for-testingtraining) | 是<br>用來評估精確度 | 0.5-5 小時的音訊 | 是 | 1-20 小時的音訊 |
| [相關文字](#related-text-data-for-training) | 否 | N/a | 是 | 1-200 MB 的相關文字 |

當您將新模型定型時，請從 [相關文字](#related-text-data-for-training)開始。 這項資料將已改善特殊條款和片語的辨識。

檔案應該依類型分組至資料集，並以 .zip 檔案的形式上傳。 每一個資料集只能包含單一資料類型。

> [!TIP]
> 若要快速入門，請考慮使用範例資料。 請參閱此 GitHub 存放庫以取得<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">範例 <span class="docon docon-navigate-external x-hidden-focus"></span> 自訂語音資料</a>

## <a name="upload-data"></a>上傳資料

若要上傳您的資料，請流覽至<a href="https://speech.microsoft.com/customspeech" target="_blank">自訂語音入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。 從入口網站中，按一下 [ **上傳資料** ] 啟動精靈，並建立您的第一個資料集。 在允許您上傳資料之前，系統會要求您選取資料集的語音資料類型。

![顯示語音入口網站中音訊上傳選項的螢幕擷取畫面。](./media/custom-speech/custom-speech-select-audio.png)

您上傳的每個資料集都必須符合您所選擇之資料類型的需求。 您的資料必須在上傳之前正確格式化。 正確格式化的資料可確保自訂語音服務會正確地處理資料。 下列各節會列出需求。

上傳資料集之後，您有幾個選項：

* 您可以流覽至 [ **測試** ] 索引標籤，並以視覺化方式檢查僅限音訊或音訊 + 人為標記的轉譯資料。
* 您可以流覽至 [ **定型** ] 索引標籤，並使用音訊 + 人工轉譯資料或相關的文字資料來定型自訂模型。

## <a name="audio-data-for-testing"></a>用於測試的音訊資料

音訊資料最適合用來測試 Microsoft 的基準語音轉換文字模型或自訂模型的精確度。 請記住，音訊資料會用來檢查特定模型效能的語音精確度。 如果您想要量化模型的精確度，請使用 [音訊 + 人標示](#audio--human-labeled-transcript-data-for-testingtraining)的轉譯資料。

您可以使用此表格來確保正確格式化您的音訊檔案，以搭配使用自訂語音：

| 屬性                 | 值                 |
|--------------------------|-----------------------|
| 檔案格式              | RIFF (WAV)            |
| 採樣速率              | 8000 hz 或 16000 Hz |
| 通道                 | 1 (mono)              |
| 每個音訊的最大長度 | 2 小時               |
| 樣本格式            | PCM，16 位元           |
| 封存格式           | .zip                  |
| 封存大小上限     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> 上傳定型和測試資料時，.zip 檔案的大小不能超過 2 GB。 如果您需要更多資料來進行定型，請將它分成數個 .zip 檔案，然後個別上傳。 稍後，您可以選擇從 *多個* 資料集進行定型。 不過，您只能從 *單一* 資料集進行測試。

使用<a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX <span class="docon docon-navigate-external x-hidden-focus"></span> </a>驗證音訊屬性，或將現有的音訊轉換成適當的格式。 以下是如何透過 SoX 命令列來完成這些活動的一些範例：

| 活動 | 描述 | SoX 命令 |
|----------|-------------|-------------|
| 檢查音訊格式 | 使用此命令來檢查<br>音訊檔案格式。 | `sox --i <filename>` |
| 轉換音訊格式 | 使用此命令來轉換<br>將音訊檔案轉換成單一通道16位、16 KHz。 | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>適用于測試/訓練的音訊 + 人為標記的文字記錄資料

若要在處理您的音訊檔案時測量 Microsoft 語音轉換文字精確度的精確度，您必須提供人類標記的轉譯 (單字) 以進行比較。 雖然人為標記的轉譯通常很耗時，但必須評估精確度，並針對您的使用案例來定型模型。 請記住，辨識的改進只會與提供的資料一樣好。 基於這個理由，請務必只上傳高品質的文字記錄。

音訊檔案在錄製的開頭和結尾可能會有無聲。 可能的話，請在每個範例檔案的語音前後至少包含一半秒的無聲。 雖然具有低錄製量或干擾性背景雜訊的音訊並不實用，但它應該不會損害您的自訂模型。 在收集音訊範例之前，請務必先升級麥克風和處理硬體的信號。

| 屬性                 | 值                               |
|--------------------------|-------------------------------------|
| 檔案格式              | RIFF (WAV)                          |
| 採樣速率              | 8000 hz 或 16000 Hz               |
| 通道                 | 1 (mono)                            |
| 每個音訊的最大長度 | 2小時 (測試) /60 s (訓練)  |
| 樣本格式            | PCM，16 位元                         |
| 封存格式           | .zip                                |
| 最大 zip 大小         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> 上傳定型和測試資料時，.zip 檔案的大小不能超過 2 GB。 您只能從 *單一* 資料集進行測試，請務必將其保留在適當的檔案大小內。 此外，每個定型檔案都不能超過60秒，否則會發生錯誤。

若要解決文字刪除或替代等問題，需要大量的資料以改善辨識。 一般來說，建議您針對大約10到20小時的音訊提供單字轉譯。 所有 WAV 檔案的文字記錄應包含在單一純文字檔案中。 文字記錄檔案的每一行都應包含其中一個音訊檔案的名稱，然後後面接著相對應的文字記錄。 檔案名稱和文字記錄應該以定位字元 (\t) 分隔。

  例如：
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> 文字記錄應使用 UTF-8 位元組順序標記 (BOM) 編碼。

文字記錄會經過文字正規化，以便系統進行處理。 不過，在將資料上傳至語音 Studio 之前，必須先完成一些重要的正規化。 若要在準備轉譯時使用適當的語言，請參閱[如何建立人為標記](how-to-custom-speech-human-labeled-transcriptions.md)的轉譯

在您收集音訊檔案和對應的轉譯之後，請將它們封裝成單一 .zip 檔案，然後再上傳至<a href="https://speech.microsoft.com/customspeech" target="_blank">自 <span class="docon docon-navigate-external x-hidden-focus"></span> 定義語音入口網站</a>。 以下是包含三個音訊檔案的範例資料集，以及一個人為標記的轉譯檔案：

> [!div class="mx-imgBorder"]
> ![從語音入口網站選取音訊](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

請參閱 [設定您的 Azure 帳戶](custom-speech-overview.md#set-up-your-azure-account) ，以取得語音服務訂用帳戶的建議區域清單。 在其中一個區域中設定語音訂用帳戶，將可縮短定型模型所花費的時間。

## <a name="related-text-data-for-training"></a>定型的相關文字資料

產品名稱或獨特的功能，應該包含用於定型的相關文字資料。 相關文字可協助確保正確的辨識。 您可以提供兩種類型的相關文字資料來改善辨識：

| 資料類型 | 這種資料如何改善辨識 |
|-----------|------------------------------------|
| 句子 (語句)  | 在辨識產品名稱或句子內容內的產業特定詞彙時，改善精確度。 |
| 發音 | 使用未定義的發音來改善不尋常詞彙、縮寫或其他單字的發音。 |

句子可以提供為單一文字檔或多個文字檔。 若要改善精確度，請使用更接近預期說話語句的文字資料。 發音應以單一文字檔的形式提供。 所有專案都可以封裝成單一 zip 檔案，並上傳至<a href="https://speech.microsoft.com/customspeech" target="_blank">自訂 <span class="docon docon-navigate-external x-hidden-focus"></span> 語音入口網站</a>。

### <a name="guidelines-to-create-a-sentences-file"></a>建立句子檔案的指導方針

若要使用句子建立自訂模型，您需要提供範例語句的清單。 語句 _不_ 需要完整或語法正確，但必須正確地反映您預期在生產環境中的語音輸入。 如果您想要讓特定詞彙具有更高的權數，請新增包含這些特定詞彙的數個句子。

作為一般指引，當定型文字盡可能接近生產環境中預期的實際文字時，模型調整最有效。 您要增強的特定領域術語和片語，應該包含在訓練文字中。 可能的話，請嘗試在個別行上控制一個句子或關鍵字。 對於您很重要的關鍵字和片語 (例如，產品名稱) ，您可以將它們複製數次。 但請記住，請不要複製太多，它可能會影響整體的辨識率。

您可以使用此資料表來確保語句的相關資料檔案格式正確：

| 屬性 | 值 |
|----------|-------|
| 文字編碼 | UTF-8 BOM |
| 每一行的語句數目 | 1 |
| 檔案大小上限 | 200 MB |

此外，您也會想要考慮下列限制：

* 避免重複字元超過四次。 例如： "aaaa" 或 "uuuu"。
* 請勿使用上述的特殊字元或 UTF-8 字元 `U+00A1` 。
* Uri 將會遭到拒絕。

### <a name="guidelines-to-create-a-pronunciation-file"></a>建立發音檔案的指導方針

如果您的使用者將遇到或使用的非標準發音有不尋常的詞彙，您可以提供自訂發音檔案來改善辨識。

> [!IMPORTANT]
> 不建議使用自訂發音檔案來改變常見單字的發音。

這包括說語句的範例，以及每個範例的自訂發音：

| 辨識/顯示的表單 | 口語形式 |
|--------------|--------------------------|
| 3CPO | 三個 c p o |
| CNTK | c n t k |
| IEEE | i 三重 e |

說出的是發音形式的語音順序。它可以由字母、字組、音節或全部三個的組合組成。

自訂的發音提供英文 (`en-US`) 和德文 (`de-DE`) 。 下表顯示支援的字元（依語言）：

| Language | 地區設定 | 字元 |
|----------|--------|------------|
| 英文 | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| 德文 | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

您可以使用下表來確保發音的相關資料檔案格式正確。 發音檔很小，而且應該只有數 kb 的大小。

| 屬性 | 值 |
|----------|-------|
| 文字編碼 | 英文) 也支援 UTF-8 BOM (ANSI |
| 每行的發音數 | 1 |
| 檔案大小上限 | 免費層) 1 MB (1 KB |

## <a name="next-steps"></a>後續步驟

* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [定型模型](how-to-custom-speech-train-model.md)
* [部署模型](./how-to-custom-speech-train-model.md)