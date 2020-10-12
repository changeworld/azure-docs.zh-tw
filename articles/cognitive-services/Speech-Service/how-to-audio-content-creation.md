---
title: 音訊內容建立-語音服務
titleSuffix: Azure Cognitive Services
description: 建立音訊內容是一種線上工具，可讓您針對應用程式和產品自訂和微調 Microsoft 的文字到語音轉換輸出。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 72fecbdc173a6174e54a28e48f983965f397ba6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86224582"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>使用音訊內容建立工具改進合成

[建立音訊內容](https://aka.ms/audiocontentcreation) 是一種線上工具，可讓您針對應用程式和產品自訂和微調 Microsoft 的文字到語音轉換輸出。 您可以使用此工具來微調公用和自訂語音，以取得更精確的自然運算式，並在雲端中管理您的輸出。

音訊內容建立工具是以 [語音合成標記語言 (SSML) ](speech-synthesis-markup.md)為基礎。 為了簡化自訂和調整，音訊內容的建立可讓您以視覺化方式即時檢查文字轉換語音的輸出。

請參閱建立音訊內容的 [影片教學](https://www.youtube.com/watch?v=O1wIJ7mts_w) 課程。

## <a name="how-does-it-work"></a>運作方式

下圖顯示微調文字到語音轉換輸出所需要的步驟。 您可以使用下列連結來深入瞭解每個步驟。

![微調文字轉語音輸出所需的步驟圖表。](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. [設定您的 Azure 帳戶和語音資源](#set-up-your-azure-account-and-speech-resource) 以開始使用。
2. 使用純文字或 SSML 腳本[建立音訊微調](#create-an-audio-tuning-file)檔。
3. 選擇您的腳本內容的語音和語言。 音訊內容的建立包含所有的 [Microsoft 文字轉換語音](language-support.md#text-to-speech)語音。 您可以使用標準、類神經或您自己的自訂語音。
   >[!NOTE]
   > 閘道存取可供自訂神經語音使用，可讓您建立與自然發音語音類似的高定義語音。 如需其他詳細資訊，請參閱管制 [流程](https://aka.ms/ignite2019/speech/ethics)。

4. 查看預設合成輸出。 然後藉由調整發音、斷路、音調、速率、聲調、聲音樣式等，來改善輸出。 如需完整的選項清單，請參閱 [語音合成標記語言](speech-synthesis-markup.md)。 以下 [影片](https://youtu.be/mUvf2NbfuYU) 示範如何在建立音訊內容時微調語音輸出。 
5. 儲存並 [匯出您的微調音訊](#export-tuned-audio)。 當您將微調播放軌儲存在系統中時，您可以繼續工作並逐一查看輸出。 當您滿意輸出時，可以使用匯出功能建立音訊建立工作。 您可以觀察匯出工作的狀態，並下載輸出以搭配您的應用程式和產品使用。

## <a name="set-up-your-azure-account-and-speech-resource"></a>設定您的 Azure 帳戶和語音資源

1. 若要使用音訊內容建立，您必須擁有 Azure 帳戶。 您可以使用您的 Microsoft 帳戶建立 Azure 帳戶。 請遵循下列指示來 [設定 Azure 帳戶](get-started.md#new-resource)。 
2. 在您的 Azure 帳戶中[建立語音資源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource)。 請確定您的定價層已設定為 **S0**。 如果您使用其中一個類神經語音，請務必在 [支援的區域](regions.md#standard-and-neural-voices)中建立您的資源。
2. 取得 Azure 帳戶和語音資源之後，您可以使用語音服務並存取 [音訊內容建立](https://aka.ms/audiocontentcreation)。
3. 選取您需要處理的語音資源。 您也可以在這裡建立新的語音資源。 
4. 您可以隨時使用 [ **設定** ] 選項（位於頂端導覽）來修改您的語音資源。

## <a name="create-an-audio-tuning-file"></a>建立音訊微調檔

有兩種方式可讓您將內容帶入音訊內容建立工具。

**選項 1：**

1. 按一下 [ **新增** 檔案] 以建立新的音訊微調檔。
2. 在編輯視窗中輸入或貼上您的內容。 每個檔案的字元上限為20000。 如果腳本的長度超過20000個字元，您可以使用選項2自動將您的內容分割成多個檔案。 
3. 別忘了儲存。

**選項 2：**

1. 按一下 **[上傳** ] 以匯入一或多個文字檔。 支援純文字和 SSML。
2. 如果您的腳本檔案超過20000個字元，請依段落、字元或正則運算式來分割檔案。 
3. 當您上傳文字檔時，請確定該檔案符合這些需求。

   | 屬性 | 值/附注 |
   |----------|---------------|
   | 檔案格式 | 純文字 (.txt)<br/> SSML 文字 ( .txt) <br/> 不支援壓縮檔案 |
   | 編碼格式 | UTF-8 |
   | 檔案名稱 | 每個檔案都必須有唯一的名稱。 不支援重複的專案。 |
   | 文字長度 | 文字檔不得超過20000個字元。 |
   | SSML 限制 | 每個 SSML 檔案只能包含單一的 SSML 片段。 |

### <a name="plain-text-example"></a>純文字範例

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML 文字範例

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>匯出微調音訊

在您審核音訊輸出並對調整和調整感到滿意之後，就可以匯出音訊。

1. 按一下 [ **匯出** ] 以建立音訊建立工作。 建議**匯出至音訊媒體**櫃，因為它支援長音訊輸出和完整音訊輸出體驗。 您也可以直接將音訊下載到本機磁片，但只有前10分鐘可用。 
2. 選擇您調整音訊的輸出格式。 以下提供支援的格式清單和取樣率。
3. 您可以在 [ **匯出** 工作] 索引標籤上查看工作的狀態。如果工作失敗，請參閱詳細資訊頁面以取得完整的報告。
4. 當工作完成時，您可以在 [ **音訊媒體** 櫃] 索引標籤上下載音訊。
5. 按一下 [下載] 。 現在您已準備好在您的應用程式或產品中使用自訂的微調音訊。

### <a name="supported-audio-formats"></a>支援的音訊格式

| [格式] | 16 kHz 取樣率 | 24 kHz 取樣率 |
|--------|--------------------|--------------------|
| wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | 音訊-16khz-128kbitrate-mono-mp3 | 音訊-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>另請參閱

* [長音訊 API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com) \(英文\)
