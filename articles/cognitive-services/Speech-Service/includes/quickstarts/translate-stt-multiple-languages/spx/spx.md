---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: c73ee93d89a350763e8ced490187e1c47f8918de
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806559"
---
## <a name="run-the-speech-cli"></a>執行語音 CLI

現在您已準備好執行語音 CLI，以兩種不同的語言將語音翻譯成文字。

從命令列，變更為包含語音 CLI 二進位檔案的目錄，然後輸入：

```bash
spx translate --microphone --target de-DE --target es-MX
```

語音 CLI 會將自然語言口說英文翻譯成以德文 (墨西哥) 西班牙文列印的文字。
按 ENTER 以停止工具。

> [!NOTE]
> 語音 CLI 預設為英文。 您可以[從語音轉換文字資料表](../../../../language-support.md)選擇不同的語言。
> 例如，新增可辨識日文語音的 `--source ja-JP`。
