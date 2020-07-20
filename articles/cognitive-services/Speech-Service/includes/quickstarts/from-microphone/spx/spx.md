---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806400"
---
## <a name="enable-microphone"></a>啟用麥克風

插入並開啟您的電腦麥克風，然後關閉任何可能也會使用麥克風的應用程式。 有些電腦具有內建的麥克風，有些則需要設定藍牙裝置。

## <a name="run-the-speech-cli"></a>執行語音 CLI

現在您已準備好執行語音 CLI，以從您的麥克風辨識語音。

1. **啟動您的應用程式** - 從命令列，變更為包含語音 CLI 二進位檔案的目錄，然後輸入：
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > 語音 CLI 預設為英文。 您可以[從語音轉換文字資料表](../../../../language-support.md)選擇不同的語言。
    > 例如，新增可辨識德文語音的 `--source de-DE`。

2. **開始辨識** - 對麥克風說話。 您會看到單字即時轉譯成文字。 語音 CLI 會在一段無回應時間之後，或當您按 Ctrl + C 時停止。
