---
title: 建立音訊內容-語音服務
titleSuffix: Azure Cognitive Services
description: 建立音訊內容是一種線上工具，可讓您針對應用程式和產品自訂及微調 Microsoft 的文字到語音轉換輸出。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: aed9c0a72af063c84c0744757772e11c689ac14c
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561029"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>透過建立音訊內容來改善合成

[建立音訊內容](https://aka.ms/audiocontentcreation)是一種線上工具，可讓您針對應用程式和產品自訂及微調 Microsoft 的文字到語音轉換輸出。 您可以使用此工具來微調公用和自訂語音，以取得更精確的自然運算式，並管理您在雲端中的輸出。

音訊內容建立工具是以[語音合成標記語言（SSML）](speech-synthesis-markup.md)為基礎。 為了簡化自訂和微調，音訊內容的建立可讓您以視覺化方式即時檢查文字轉換語音的輸出。

## <a name="how-does-it-work"></a>運作方式

下圖顯示微調和匯出自訂的語音轉換文字輸出所需的步驟。 若要深入瞭解每個步驟，請使用下列連結。

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. 第一個步驟是[建立 Azure 帳戶、註冊語音資源，並取得訂用帳戶金鑰](#create-a-speech-resource)。 擁有訂用帳戶金鑰之後，您就可以使用它來呼叫語音服務，以及存取[音訊內容的建立](https://aka.ms/audiocontentcreation)。
2. 使用純文字或 SSML[建立音訊微調](#create-an-audio-tuning-file)檔案。
3. 選擇您想要微調的語音和語言。 音訊內容的建立包含所有[Microsoft 文字轉換語音的聲音](language-support.md#text-to-speech)。 您可以使用標準、類神經或您自己的自訂語音。
   >[!NOTE]
   > 閘道存取適用于自訂類神經語音，可讓您建立類似自然發音語音的高定義語音。 如需其他詳細資訊，請參閱控制[流程](https://aka.ms/ignite2019/speech/ethics)。

4. 檢查預設結果。 然後使用微調工具來調整發音、音調、速率、聲調、語音樣式等等。 如需完整的選項清單，請參閱[語音合成標記語言](speech-synthesis-markup.md)。
5. 儲存並[匯出您調整的音訊](#export-tuned-audio)。 當您在系統中儲存微調播放軌時，您可以繼續工作並逐一查看輸出。 當您對輸出感到滿意時，可以使用匯出功能來建立音訊建立工作。 您可以觀察匯出工作的狀態，並下載輸出以用於您的應用程式和產品。
6. 最後一個步驟是在您的應用程式和產品中使用自訂調整語音。

## <a name="create-a-speech-resource"></a>建立語音資源

請遵循下列步驟來建立語音資源，並將它與語音 Studio 連線。

1. 請遵循這些指示來[註冊 Azure 帳戶](get-started.md#try-the-speech-service-using-a-new-azure-account)，並[建立語音資源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)。 請確定您的定價層已設定為**S0**。 如果您使用其中一個類神經語音，請務必在[支援的區域](regions.md#standard-and-neural-voices)中建立您的資源。
2. 登入[音訊內容建立](https://aka.ms/audiocontentcreation)。
3. 選取現有的專案，或按一下 **[新建]。**
4. 您可以隨時使用 [**設定**] 選項（位於頂端導覽）修改訂閱。

## <a name="create-an-audio-tuning-file"></a>建立音訊微調檔案

有兩種方式可讓您的內容進入音訊內容建立工具。

**選項 1：**

1. 在您登入[音訊內容建立](https://aka.ms/audiocontentcreation)之後，請按一下 [**音訊微調**] 來建立新的音訊微調檔案。
2. 當編輯視窗出現時，您最多可以輸入10000個字元。
3. 別忘了儲存。

**選項 2：**

1. 在您登入[音訊內容建立](https://aka.ms/audiocontentcreation)之後，請按一下 **[上傳**] 以匯入一或多個文字檔。 支援純文字和 SSML。
2. 當您上傳文字檔時，請確定內容符合這些需求。

   | 屬性 | 值/附注 |
   |----------|---------------|
   | 檔案格式 | 純文字 (.txt)<br/> SSML 文字（.txt）<br/> 不支援 Zip 檔案 |
   | 編碼格式 | UTF-8 |
   | 檔案名稱 | 每個檔案都必須有唯一的名稱。 不支援重複的專案。 |
   | 文字長度 | 文字檔不得超過10000個字元。 |
   | SSML 限制 | 每個 SSML 檔案只能包含一段 SSML。 |

### <a name="plain-text-example"></a>純文字範例

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML 文字範例

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>匯出調整的音訊

在您審核音訊輸出並滿足微調和調整之後，您可以匯出音訊。

1. 從 [[音訊內容建立](https://aka.ms/audiocontentcreation)] 工具中，按一下 [**匯出**] 以建立音訊建立工作。
2. 選擇您調整音訊的輸出格式。 以下提供支援的格式清單和取樣率。
3. 您可以在 [**匯出**工作] 索引標籤上，查看工作的狀態。如果工作失敗，請參閱詳細資訊頁面以取得完整的報表。
4. 當工作完成時，您可以在 [**音訊媒體**櫃] 索引標籤上下載您的音訊。
5. 按一下 [下載]。 現在您已準備好在應用程式或產品中使用您的自訂調整音訊。

### <a name="supported-audio-formats"></a>支援的音訊格式

| [格式] | 16 kHz 取樣率 | 24 kHz 取樣率 |
|--------|--------------------|--------------------|
| wav | riff-riff-16khz-16bit-mono-pcm-dxil 16 位-mono-pcm | riff-24khz-dxil 16 位-mono-pcm |
| mp3 | 音訊-riff-16khz-16bit-mono-pcm-128kbitrate-單聲道-mp3 | 音訊-24khz-160kbitrate-單聲道-mp3 |

## <a name="see-also"></a>另請參閱

* [長音訊 API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com) \(英文\)
