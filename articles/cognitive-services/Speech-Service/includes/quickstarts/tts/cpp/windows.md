---
title: 快速入門：合成語音，C++ (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 Windows 桌面上以 C++ 合成語音
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: ab2193a1ea34b176e5f97806f0099dfc86d75965
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925677"
---
## <a name="prerequisites"></a>Prerequisites

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=windows)

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟來源檔案 helloworld.cpp  。

1. 使用下列程式碼片段取代所有程式碼：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/text-to-speech/helloworld/helloworld.cpp#code)]

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)取代 `YourServiceRegion` 字串。

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

1. 從功能表列中，選取 [建置]   > [建置方案]  來建置應用程式。 程式碼現在應該可以編譯，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或按 **F5**) 以啟動 **helloworld** 應用程式。

1. 鍵入英文片語或句子。 應用程式會將您的文字傳送到語音服務，文字會轉換為合成語音並傳送回應用程式，以在喇叭上播放。

   ![語音合成成功後的主控台輸出](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>另請參閱

- [建立自訂語音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [錄製自訂語音樣本](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
