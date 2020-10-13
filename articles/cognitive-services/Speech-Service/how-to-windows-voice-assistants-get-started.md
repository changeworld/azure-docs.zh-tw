---
title: Windows 上的語音助理-開始
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82997752"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>開始使用 Windows 上的語音助理

本指南將引導您完成在 Windows 上開始開發語音助理的步驟。

## <a name="set-up-your-development-environment"></a>設定開發環境

若要開始開發適用于 Windows 的語音助理，您必須確定您有適當的開發環境。

- **Visual Studio：**  您將需要安裝 [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/)、社區版或更高版本
- **Windows 版本**：一台電腦，具有 Windows 的 Windows 測試人員快速環形組建和 Windows SDK 的 Windows 測試人員版本。此範例程式碼會使用 Windows SDK 19018，確認 Windows 測試人員發行組建 19025.vb_release_analog .191112-1600。指定版本以上的任何組建或 SDK 都必須相容。
- **UWP 開發工具**： Visual Studio 的通用 Windows 平臺開發工作負載。請參閱 UWP 的 [ [設定](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) ] 頁面，讓您的電腦準備好開發 UWP 應用程式。
- **工作的麥克風和音訊輸出**

## <a name="obtain-resources-from-microsoft"></a>取得 Microsoft 的資源

Windows 上完全自訂的語音代理程式所需的某些資源，將需要來自 Microsoft 的資源。 [UWP 語音助理範例](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)提供這些資源的範例版本，供初始開發和測試之用，因此不需要這一節就能進行初始開發。

- **關鍵字模型：**  語音啟用需要 Microsoft 的關鍵字模型（以 bin 檔案的形式）。 UWP 語音助理範例中提供的 bin 檔案，是以關鍵字 "Contoso" 來定型。
- **有限存取功能權杖：** 由於 ConversationalAgent Api 會提供麥克風音訊的存取權，因此在受限的存取功能限制下會受到保護。若要使用有限的存取功能，您必須從 Microsoft 取得連線到應用程式套件身分識別的有限存取功能權杖。

## <a name="establish-a-dialog-service"></a>建立對話服務

若要取得完整的語音助理體驗，應用程式將需要一個對話方塊服務，

- 偵測指定音訊檔中的關鍵字
- 接聽使用者輸入，並將其轉換成文字
- 提供文字給 bot
- 將 bot 的文字回應轉譯為音訊輸出

這些是使用 Direct Line 語音建立基本對話服務的需求。

- **語音服務訂** 用帳戶：適用于語音轉換文字和文字轉換語音轉換的認知語音服務訂用帳戶。 [在這裡](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started)免費試用語音服務。
- **Bot Framework Bot：**  使用 Bot Framework 4.2 版或更新版本所建立的 bot，其已訂閱 [Direct Line 語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/direct-line-speech) 來啟用語音輸入和輸出。 [本指南](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk) 包含逐步指示，可讓您建立「echo bot」並訂閱 Direct Line 語音。 您也可以在 [這裡](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) 取得如何建立自訂 bot 的步驟，然後依照 [此處](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk) 的相同步驟來訂閱 Direct Line 語音，但使用新的 bot，而不是「echo bot」。

## <a name="try-out-the-sample-app"></a>試用範例應用程式

使用您的語音服務訂用帳戶金鑰和 echo bot 的 bot 識別碼，您就可以開始試用 [UWP 語音助理範例](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)。 遵循讀我檔案中的指示來執行應用程式，並輸入您的認證。

## <a name="create-your-own-voice-assistant-for-windows"></a>為 Windows 建立您自己的語音助理

一旦您收到來自 Microsoft 的有限存取功能權杖和 bin 檔案，您就可以在 Windows 上自己的語音助理開始。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [閱讀語音助理實行指南](windows-voice-assistants-implementation-guide.md)
