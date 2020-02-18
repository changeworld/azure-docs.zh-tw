---
title: 快速入門：從麥克風辨識語音，C++ (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 Windows 桌面上以 C++ 辨識語音
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: cc349c2d5e43946648533d546398e95d4fa98382
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156036"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定開發環境](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [建立空的範例專案](../../../../quickstarts/create-project.md?tabs=windows)
> * 確定您可以存取麥克風以擷取音訊

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟來源檔案 helloworld.cpp  。

1. 使用下列程式碼片段取代所有程式碼：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 將 `YourServiceRegion` 字串替換成訂用帳戶所關聯[區域](https://aka.ms/speech/sdkregion)中的「語音 SDK 參數」(例如，免費試用訂用帳戶的區域是 `westus`)。

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  。

> [!NOTE]
> 語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

1. 從功能表列中，選取 [建置]   > [建置方案]  來建置應用程式。 程式碼現在應該可以編譯，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或按 **F5**) 以啟動 **helloworld** 應用程式。

1. 請說英文片語或句子。 應用程式會將您的語音傳送到語音服務，語音會轉換為文字並傳送回應用程式顯示。

   ![成功辨識後的主控台輸出](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]