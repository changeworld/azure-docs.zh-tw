---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806498"
---
## <a name="run-the-speech-cli"></a>執行語音 CLI

現在您已準備好執行語音 CLI，將語音從文字合成為新的音訊檔案。

從命令列，變更為包含語音 CLI 二進位檔案的目錄，然後輸入：

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

語音 CLI 會以英文產生自然語言成為 `greetings.wav` 音訊檔案。
在 Windows 中，您可以輸入 `start greetings.wav` 來播放音訊檔案。
