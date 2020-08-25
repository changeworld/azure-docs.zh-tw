---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170122"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>將語音合成至喇叭

現在您已準備好執行語音 CLI，從文字合成語音。 從命令列，變更為包含語音 CLI 二進位檔案的目錄。 然後，執行下列命令。

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

語音 CLI 會透過電腦喇叭，以英文產生自然語言。

## <a name="synthesize-speech-to-a-file"></a>將語音合成至檔案

執行下列命令，將說話者的輸出變更為 `.wav` 檔案。

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

語音 CLI 會以英文產生自然語言成為 `greetings.wav` 音訊檔案。
在 Windows 中，您可以輸入 `start greetings.wav` 來播放音訊檔案。