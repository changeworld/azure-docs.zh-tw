---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 905eacc3751b3d5d6c66a2fdb0e1391a747ab895
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327018"
---
語音服務的核心功能之一，就是能夠辨識並轉譯人類語音 (通常稱為語音轉文字)。 在本快速入門中，您將了解如何在您的應用程式和產品中使用語音 CLI，以執行高品質的語音轉換文字辨識。

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>從麥克風進行語音轉換文字

插入並開啟您的電腦麥克風，然後關閉任何可能也會使用麥克風的應用程式。 有些電腦具有內建的麥克風，有些則需要設定藍牙裝置。

現在您已準備好執行語音 CLI，以從您的麥克風辨識語音。 從命令列，變更為包含語音 CLI 二進位檔案的目錄，然後執行下列命令。

```bash
spx recognize --microphone
```

> [!NOTE]
> 語音 CLI 預設為英文。 您可以[從語音轉換文字資料表](../../../../language-support.md)選擇不同的語言。
> 例如，新增可辨識德文語音的 `--source de-DE`。

用麥克風說話，您會看到單字即時轉譯成文字。 語音 CLI 會在一段無回應時間之後，或當您按 Ctrl + C 時停止。

## <a name="speech-to-text-from-audio-file"></a>從音訊檔案進行語音轉換文字

語音 CLI 可以辨識多種檔案格式和自然語言的語音。 在本範例中，您可以使用包含英文語音的任何 WAV 檔案 (16kHz 或 8kHz、16 位元和 Mono PCM)。 或者，如果想取得快速範例，請下載 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav<span class="docon docon-download x-hidden-focus"></span></a> 檔案，然後複製到與語音 CLI 二進位檔案相同的目錄。

現在您已準備好執行語音 CLI，執行下列命令以辨識在語音檔中找到的語音。

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> 語音 CLI 預設為英文。 您可以[從語音轉換文字資料表](../../../../language-support.md)選擇不同的語言。
> 例如，新增可辨識德文語音的 `--source de-DE`。

語音 CLI 會在畫面上顯示語音的文字轉譯。