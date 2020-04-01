---
title: 音效內容建立 - 語音服務
titleSuffix: Azure Cognitive Services
description: 音訊內容創建是一個線上工具,允許您自定義和微調 Microsoft 的應用和產品的文本到語音輸出。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: 8184d30471b4d9171a23c8d03e17b2a54dca2ece
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397828"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>以音效內容建立改進合成

[音訊內容創建](https://aka.ms/audiocontentcreation)是一個線上工具,允許您自定義和微調 Microsoft 的應用和產品的文本到語音輸出。 您可以使用此工具微調公共和自定義語音,以便更準確的自然表達,並在雲中管理輸出。

音訊內容創建工具基於[語音合成標記語言 (SSML)。](speech-synthesis-markup.md) 為了簡化自定義和調優,音訊內容創建允許您即時直觀地檢查文本到語音輸出。

## <a name="how-does-it-work"></a>運作方式

此圖顯示了調整和匯出自定義語音到文本輸出的步驟。 使用以下連結瞭解有關每個步驟的更多內容。

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. 第一步是建立[Azure 帳號、註冊語音資源並獲得訂閱金鑰](#create-a-speech-resource)。 擁有訂閱金鑰後,可以使用它呼叫語音服務,並存[取音效內容建立](https://aka.ms/audiocontentcreation)。
2. 使用純文字或 SSML[建立音訊調諧檔](#create-an-audio-tuning-file)。
3. 選擇要調整的聲音和語言。 音效內容建立包括所有[Microsoft 文本到語音語音](language-support.md#text-to-speech)。 您可以使用標準、神經或您自己的自定義語音。
   >[!NOTE]
   > 門控訪問可用於自定義神經語音,允許您創建類似於自然語音的高清語音。 有關詳細資訊,請參閱[門控過程](https://aka.ms/ignite2019/speech/ethics)。

4. 查看默認結果。 然後使用調優工具調整發音、音調、速率、語調、語音樣式等。 有關選項的完整清單,請參閱[語音合成標記語言](speech-synthesis-markup.md)。
5. 儲存並[匯出已調諧的音訊](#export-tuned-audio)。 在系統中保存調諧軌道時,可以繼續工作並在輸出上反覆運算。 當您對輸出滿意時,可以使用匯出功能創建音訊創建任務。 您可以觀察匯出任務的狀態,並下載輸出以用於應用和產品。
6. 最後一步是在應用和產品中使用自定義調諧語音。

## <a name="create-a-speech-resource"></a>建立語音資源

按照以下步驟創建語音資源並將其連接到語音工作室。

1. 以這些說明註冊[Azure 帳號](get-started.md#new-resource)並[建立語音資源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource)。 請確定您的定價層設定為**S0**。 如果使用「神經」語音之一,請確保在[受支援的區域](regions.md#standard-and-neural-voices)中創建資源。
2. 登入[音效內容建立](https://aka.ms/audiocontentcreation)。
3. 選擇現有專案,或單擊"**新建**"。
4. 您可以隨時使用位於頂部導航的 **「設定」** 選項修改訂閱。

## <a name="create-an-audio-tuning-file"></a>建立音訊調優檔案

有兩種方法可以將內容放入「音訊內容創建」工具中。

**選項 1：**

1. 登錄[音訊內容創建](https://aka.ms/audiocontentcreation)後,按一下 **「音訊調優」** 以創建新的音訊調優檔。
2. 當出現編輯視窗時,您最多可以輸入 10,000 個字元。
3. 別忘了存錢。

**選項 2：**

1. 登入[音效內容建立](https://aka.ms/audiocontentcreation)後,按一下「**上傳**」以匯入一個或多個文字檔。 支援純文字和 SSML。
2. 上傳文本檔時,請確保內容滿足這些要求。

   | 屬性 | 值/註解 |
   |----------|---------------|
   | 檔案格式 | 純文字 (.txt)<br/> SSML 文字 (.txt)<br/> 不支援 Zip 檔案 |
   | 編碼格式 | UTF-8 |
   | 檔案名稱 | 每個文件必須具有唯一的名稱。 不支持重複項。 |
   | 文字長度 | 文本檔不得超過 10,000 個字元。 |
   | SSML 限制 | 每個 SSML 檔只能包含一段 SSML。 |

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

## <a name="export-tuned-audio"></a>匯出調諧音訊

查看音訊輸出並對調優和調整感到滿意后,可以匯出音訊。

1. 在[「音訊內容建立](https://aka.ms/audiocontentcreation)」工具中,按一下「**匯出**」以建立音訊建立任務。
2. 選擇已調諧音訊的輸出格式。 支援格式和採樣率的清單如下。
3. 您可以在 **「匯出任務」** 選項卡上查看任務的狀態。如果任務失敗,請參閱完整報表的詳細資訊頁。
4. 任務完成後,您的音訊可在 **「音訊庫」** 選項卡上下載。
5. 按一下 [下載]**** 現在,您可以在應用或產品中使用自定義調諧音訊。

### <a name="supported-audio-formats"></a>支援的音訊格式

| [格式] | 16 kHz 取樣率 | 24 kHz 取樣率 |
|--------|--------------------|--------------------|
| wav | 里夫-16khz-16位單件 | riff-24khz-16 位單位 pcm |
| mp3 | 音訊-16khz-128kbitrate-單聲道mp3 | 音訊-24khz-160kbitrate-單聲道mp3 |

## <a name="see-also"></a>另請參閱

* [長音訊 API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com) \(英文\)
