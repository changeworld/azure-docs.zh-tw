---
title: Windows 上的語音助理-常見問題
titleSuffix: Azure Cognitive Services
description: Windows 語音代理程式開發通常會出現的常見問題。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 3cb4dd16592f3af962b32fe3005d2ad4b241d831
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84456997"
---
# <a name="samples-and-faqs"></a>範例和常見問題

## <a name="the-uwp-voice-assistant-sample"></a>UWP 語音助理範例

UWP 語音助理範例是 Windows 上的語音助理，可用來

- 示範如何使用 Windows ConversationalAgent Api
- 顯示語音代理程式在 Windows 上的最佳作法
- 為您的 MVP 代理程式應用程式提供可調整的應用程式和可重複使用的元件
- 示範如何搭配使用 Direct Line 語音和 Bot Framework 或自訂命令，以及如何搭配 Windows ConversationalAgent Api 使用，以提供端對端語音代理程式體驗

範例應用程式隨附的檔會逐步解說語音啟用和代理程式管理的程式碼路徑，從啟動的必要條件到適當的清除。

> [!div class="nextstepaction"]
> [流覽適用于 UWP 範例的 GitHub 存放庫](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>如何? 與 Microsoft 聯繫，以取得有限存取功能權杖和關鍵字模型檔案等資源？

winvoiceassistants@microsoft.com要求這些資源的連絡人。

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>我的應用程式在按語音啟動時，會顯示在小型視窗中。 如何從 compact view 轉換成完整的應用程式視窗？

當您的應用程式第一次由語音啟動時，就會以精簡視圖的方式啟動。 如需有關不同視圖的指引，以及在 Windows 上的語音助理之間的轉換，請閱讀 [語音啟用預覽的設計指引](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) 。

若要將 compact view 轉換成完整的應用程式視圖，請使用 appView API `TryEnterViewModeAsync` ：

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>為什麼 Windows 上的語音助理功能只針對 UWP 應用程式啟用？

由於與語音啟用等功能相關的隱私權風險，UWP 平臺的功能是必要的，讓 Windows 上的語音助理功能得以充分保障安全性。

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>UWP 語音助理範例會使用 Direct Line 語音。 我是否必須在 Windows 上使用語音助理的 Direct Line 語音？

UWP 範例應用程式是使用 Direct Line 語音和語音服務 SDK 來開發，以示範如何使用對話服務搭配 Windows 對話式代理程式功能。 不過，您可以使用任何服務進行本機和雲端關鍵字驗證、語音轉換文字轉換、bot 對話和文字轉換語音的轉換。