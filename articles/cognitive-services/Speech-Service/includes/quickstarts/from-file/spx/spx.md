---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806288"
---
## <a name="find-a-file-that-contains-speech"></a>尋找內含語音的檔案

語音 CLI 可以辨識多種檔案格式和自然語言的語音。 在本快速入門中，您可以使用包含英文語音的 WAV 檔案 (16kHz 或 8kHz、16 位元和 Mono PCM)。

1. 下載 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a>。
2. 將 `whatstheweatherlike.wav` 檔案複製到與語音 CLI 二進位檔案相同的目錄。

## <a name="run-the-speech-cli"></a>執行語音 CLI

現在您已準備好執行語音 CLI，以辨識在音效檔中找到的語音。

從命令列，變更為包含語音 CLI 二進位檔案的目錄，然後輸入：

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> 語音 CLI 預設為英文。 您可以[從語音轉換文字資料表](../../../../language-support.md)選擇不同的語言。
> 例如，新增可辨識德文語音的 `--source de-DE`。

語音 CLI 會在畫面上顯示語音的文字轉譯。 然後，語音 CLI 將會關閉。
