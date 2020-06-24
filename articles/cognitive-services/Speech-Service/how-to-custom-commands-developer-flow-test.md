---
title: 測試您的自訂命令應用程式
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解測試自訂命令應用程式的不同方法。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: a7992a2a27822f87cc8667ff86f642d1d47d09aa
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308026"
---
# <a name="test-your-custom-commands-application"></a>測試您的自訂命令應用程式

在本文中，您將瞭解測試自訂命令應用程式的不同方法。

## <a name="test-in-the-portal"></a>在入口網站中測試

在入口網站中測試是檢查您的自訂命令應用程式是否如預期般運作的最簡單且最快速的方式。 成功訓練應用程式之後，按一下 `Test` 按鈕以開始測試。

> [!div class="mx-imgBorder"]
> ![在入口網站中測試](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>使用 Windows 語音助理用戶端進行測試

Windows 語音助理用戶端是 c # 中的 Windows Presentation Foundation （WPF）應用程式，可讓您在建立自訂用戶端應用程式之前，輕鬆地測試與 bot 的互動。

此工具可以接受自訂命令應用程式識別碼。 它可讓您測試工作完成或裝載于自訂命令服務上的命令和控制項案例。

若要設定用戶端，請簽出[Windows 語音助理用戶端](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf)。

> [!div class="mx-imgBorder"]
> ![WVAC 建立設定檔](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>使用啟用語音 SDK 的用戶端應用程式進行測試 
語音軟體發展工具組（SDK）公開許多語音服務功能，可讓您開發具備語音功能的應用程式。 這也適用于許多程式設計語言，以及跨所有平臺。

若要使用語音 SDK 設定通用 Windows 平臺（UWP）用戶端應用程式，並將它與您的自訂命令應用程式整合：  
- [如何：使用語音 SDK 與用戶端應用程式整合](./how-to-custom-commands-setup-speech-sdk.md)
- [如何：將活動傳送至用戶端應用程式](./how-to-custom-commands-send-activity-to-client.md)
- [如何：設定 web 端點](./how-to-custom-commands-setup-web-endpoints.md)

針對其他程式設計語言和平臺：
- [語音 SDK 程式設計語言，平臺，案例容量](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk)
- [語音助理範例代碼](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [請參閱 GitHub 上的範例](https://aka.ms/speech/cc-samples)

