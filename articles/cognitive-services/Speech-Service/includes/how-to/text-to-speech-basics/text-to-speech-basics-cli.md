---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: b68025c1b33a94f03e2d84693a7d6407a18abd88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332416"
---
在本快速入門中，您將了解如何使用語音 SDK 進行文字轉換語音合成的常見設計模式。 首先，您會進行基本設定與合成，並繼續處理更多用於自訂應用程式開發的高階範例，包括：

* 以記憶體內部資料流的形式取得回應
* 自訂輸出採樣速率和位元速率
* 使用 SSML 提交合成要求 (語音合成標記語言)
* 使用神經語音

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

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