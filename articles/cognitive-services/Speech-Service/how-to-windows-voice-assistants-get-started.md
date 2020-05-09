---
title: Windows 上的語音助理-開始使用
titleSuffix: Azure Cognitive Services
description: 開始開發 windows 語音代理程式的步驟，包括範例程式碼快速入門的參考。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 4ea2f88b02738645af3f8fc32d5fdb99168a1122
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997752"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Windows 上的語音助理使用者入門

本指南將引導您完成在 Windows 上開始開發語音助理的步驟。

## <a name="set-up-your-development-environment"></a>設定開發環境

若要開始開發 Windows 的語音助理，您必須確定您擁有適當的開發環境。

- **Visual Studio：** 您將需要安裝 [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/)、社區版或更高版本
- **Windows 版本**：具有 windows Windows 測試人員快速通道組建的電腦，以及 Windows SDK 的 Windows 測試人員版本。此範例程式碼已驗證為在 Windows 測試人員發行組建19025上運作。請使用 Windows SDK 19018 vb_release_analog .191112-1600。指定版本之上的任何組建或 SDK 都應該相容。
- **UWP 開發工具**： Visual Studio 中的通用 Windows 平臺開發工作負載。請參閱 UWP 的 [[設定](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)] 頁面，讓您的機器準備好開發 UWP 應用程式。
- **運作中的麥克風和音訊輸出**

## <a name="obtain-resources-from-microsoft"></a>取得 Microsoft 的資源

在 Windows 上完全自訂語音代理程式所需的某些資源，將需要 Microsoft 的資源。 [UWP 語音助理範例](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)提供這些資源的範例版本，以便進行初始開發和測試，因此這一節不需要用於初始開發。

- **關鍵字模型：** 語音啟用需要來自 Microsoft 的關鍵字模型，格式為 bin 檔案。 UWP 語音助理範例中提供的 bin 檔案是以關鍵字 "Contoso" 進行定型。
- **有限的存取功能權杖：** 由於 ConversationalAgent Api 會提供麥克風音訊的存取權，因此會受到有限的存取功能限制。若要使用有限的存取功能，您必須取得連接到 Microsoft 應用程式之套件識別的有限存取功能權杖。

## <a name="establish-a-dialog-service"></a>建立對話方塊服務

如需完整的語音助理體驗，應用程式將需要一個對話方塊服務，

- 偵測指定音訊檔案中的關鍵字
- 接聽使用者輸入，並將其轉換成文字
- 將文字提供給 bot
- 將 bot 的文字回應轉譯成音訊輸出

這些是使用 Direct Line 語音建立基本對話服務的需求。

- **語音服務訂**用帳戶：適用于語音轉換文字和文字轉換語音的認知語音服務訂用帳戶。 [免費試用](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started)語音服務。
- **Bot Framework bot：** 使用 Bot Framework 4.2 版或更新版本所建立的 bot，其訂閱了[Direct Line 語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/direct-line-speech)以啟用語音輸入和輸出。 [本指南](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk)包含逐步指示，可讓您建立「echo bot」並訂閱 Direct Line Speech。 您也可以移至[這裡](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/)，以取得如何建立自訂 bot 的步驟，然後依照[這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk)的相同步驟來訂閱 Direct Line 語音，但使用新的 bot，而不是「echo bot」。

## <a name="try-out-the-sample-app"></a>試用範例應用程式

使用您的語音服務訂用帳戶金鑰和 echo bot 的 bot 識別碼，您就可以試用[UWP 語音助理範例](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)。 遵循讀我檔案中的指示來執行應用程式，並輸入您的認證。

## <a name="create-your-own-voice-assistant-for-windows"></a>為 Windows 建立您自己的語音助理

當您收到來自 Microsoft 的有限存取權功能權杖和 bin 檔案之後，就可以在 Windows 上開始使用您自己的語音助理。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [閱讀語音助理執行指南](windows-voice-assistants-implementation-guide.md)
