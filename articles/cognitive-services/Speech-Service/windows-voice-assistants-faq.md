---
title: Windows 上的語音助理-常見問題
titleSuffix: Azure Cognitive Services
description: Windows 語音代理程式開發期間經常會遇到的常見問題。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 3cb4dd16592f3af962b32fe3005d2ad4b241d831
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456997"
---
# <a name="samples-and-faqs"></a>範例和常見問題

## <a name="the-uwp-voice-assistant-sample"></a>UWP 語音助理範例

UWP 語音助理範例是 Windows 上的語音助理，其適用于

- 示範如何使用 Windows ConversationalAgent Api
- 顯示 Windows 上語音代理程式的最佳作法
- 為您的 MVP 代理程式應用程式提供適合的應用程式和可重複使用的元件
- 示範如何搭配使用 Direct Line Speech 和 Bot Framework 或自訂命令，以用於端對端語音代理程式體驗

範例應用程式所提供的檔會逐步解說語音啟用和代理程式管理的程式碼路徑，從啟動的必要條件到適當的清除。

> [!div class="nextstepaction"]
> [流覽適用于 UWP 範例的 GitHub 存放庫](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>如何? 與 Microsoft 聯繫，以取得有限的存取功能權杖和關鍵字模型檔案之類的資源嗎？

請聯絡 winvoiceassistants@microsoft.com 以要求這些資源。

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>我的應用程式在按語音啟動時，會顯示在小視窗中。 如何從 compact view 轉換到完整的應用程式視窗？

當您的應用程式第一次由語音啟動時，它就會以精簡的方式啟動。 請閱讀[語音啟用預覽的設計指導](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview)方針，以取得有關 Windows 上語音助理的不同視圖和轉換的指引。

若要從 compact view 轉換到完整的應用程式，請使用 appView API `TryEnterViewModeAsync` ：

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>為什麼只有 UWP 應用程式才會啟用 Windows 上的「語音助理」功能？

假設有與語音啟動等功能相關的隱私權風險，則需要 UWP 平臺的功能，才能讓 Windows 上的語音助理功能夠安全。

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>UWP 語音助理範例會使用 Direct Line 語音。 我是否必須在 Windows 上使用語音助理的 Direct Line 語音？

UWP 範例應用程式是使用 Direct Line 語音和語音服務 SDK 來開發，以示範如何使用對話方塊服務搭配 Windows 對話式代理程式功能。 不過，您可以使用任何服務進行本機和雲端關鍵字驗證、語音轉換文字轉換、bot 對話和文字轉換語音。