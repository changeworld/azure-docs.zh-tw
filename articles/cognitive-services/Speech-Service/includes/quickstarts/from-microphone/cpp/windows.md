---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: db87f31f4702d3487a11a6d833755105455d74c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400688"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">建立 Azure 語音資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)
> * 確定您可以存取麥克風以擷取音訊

## <a name="source-code"></a>原始程式碼

建立名為 *helloworld.cpp* 的 C++ 原始程式檔，然後在其中貼上下列程式碼。

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>程式碼說明

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>建置並執行應用程式

1. 從功能表列中，選取 [建置]   > [建置方案]  來建置應用程式。 程式碼現在應該可以編譯，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或按 <kbd>F5</kbd>) 以啟動 **helloworld** 應用程式。

1. 請說英文片語或句子。 應用程式會將您的語音傳送到語音服務，語音會轉換為文字並傳送回應用程式顯示。

   ![成功辨識後的主控台輸出](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]