---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 3613d190ef079d0e477d42b426a224d8e4dda7e6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400733"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">建立 Azure 語音資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * 確定您可以存取麥克風以擷取音訊

## <a name="source-code"></a>原始程式碼

建立名為 *quickstart.py* 的檔案，並在其中貼上下列 Python 程式碼。

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>程式碼說明

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>建置並執行應用程式

現在您已經準備好要測試應用程式，並使用語音服務來驗證語音辨識功能。

1. **啟動應用程式** - 從命令列輸入：
    ```bash
    python quickstart.py
    ```
2. **開始辨識** - 系統會提示您說英文片語。 您的語音會傳送至語音服務、轉譯為文字，並在主控台中轉譯。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

