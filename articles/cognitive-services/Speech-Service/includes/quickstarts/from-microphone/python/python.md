---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85839066"
---
## <a name="prerequisites"></a>必要條件

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

現在您已準備好使用語音服務來測試語音辨識。 

如果您是在 macOS 上執行此程式，而且是使用麥克風所建置的第一個 Python 應用程式，您可能需要讓終端機存取麥克風。 開啟 [系統設定]，然後選取 [安全性與隱私權]。 接下來，選取 [隱私權] 並且在清單中找到 [麥克風]。 最後，選取 [終端機] 並且儲存。 

1. **啟動應用程式** - 從命令列輸入：
    ```bash
    python quickstart.py
    ```
2. **開始辨識** - 系統會提示您說英文片語。 您的語音會傳送至語音服務、轉譯為文字，並在主控台中轉譯。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
