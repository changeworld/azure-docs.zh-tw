---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: c2b844b3b1aa7a848e2668b3dca5a1cac5b5baaf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400804"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">建立 Azure 語音資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)
> * 確定您可以存取麥克風以擷取音訊

## <a name="source-code"></a>原始程式碼

若要將新的空白類別新增到您的 Java 專案，請選取 [檔案]   > [新增]   > [類別]  。 在 [新增 Java 類別]  視窗中，於 [套件]  欄位中輸入 **speechsdk.quickstart**，並在 [名稱]  欄位中輸入 **Main**。

![[新增 Java 類別] 視窗的螢幕擷取畫面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

下列程式碼片段取代 *Main.java* 檔案的內容：

[!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>程式碼說明

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>建置並執行應用程式

按 <kbd>F11</kbd> 鍵，或選取 [執行]   > [偵錯]  。
系統將會辨識接下來 15 秒來自您麥克風的語音輸入，並記錄在主控台視窗中。

![成功辨識後主控台輸出的螢幕擷取畫面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

