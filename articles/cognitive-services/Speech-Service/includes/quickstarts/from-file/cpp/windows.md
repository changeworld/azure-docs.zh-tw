---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e3eac0311b8b24c8c6e1fbf4d8cae55c360a7352
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377487"
---
## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../overview.md#try-the-speech-service-for-free)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟來源檔案 helloworld.cpp。

1. 使用下列程式碼片段取代所有程式碼：
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 將 `YourServiceRegion` 字串取代為訂用帳戶所關聯[區域](https://aka.ms/speech/sdkregion)中的**區域識別碼**。

1. 以您自己的檔案名稱取代字串 `whatstheweatherlike.wav`。

1. 從功能表列中，選擇 [檔案] > [全部儲存]。

> [!NOTE]
> 語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

1. 從功能表列中，選取 [建置] > [建置方案] 來建置應用程式。 程式碼現在應該可以編譯，而且不會出現任何錯誤。

1. 選擇 [偵錯] > [開始偵錯] (或按 **F5**) 以啟動 **helloworld** 應用程式。

1. 您的音訊檔案會傳送到語音服務，且檔案中的第一個語句會轉譯為文字，該文字會出現在相同視窗中。

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]