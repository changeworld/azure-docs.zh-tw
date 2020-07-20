---
title: Windows 語音助理的隱私權指導方針
titleSuffix: Azure Cognitive Services
description: 針對語音代理程式預設啟用語音啟動的指示。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: b9cbb873066131264732d6f46320461bae8c3188
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84981756"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Windows 語音助理的隱私權指導方針

使用者必須清楚瞭解如何收集及使用其聲音資料，並對其提供控制該集合發生的時間與方式的重要資訊。 這些隱私權的核心 facet--*洩漏*和*同意*--對於整合到 Windows 的語音助理而言，這對於使用的一律接聽性質而言特別重要。

在 Windows 上建立語音助理的開發人員必須在其應用程式內包含清楚的使用者介面元素，以反映 assistant 的接聽功能。

> [!NOTE]
> 如果無法為助理應用程式提供適當的洩漏和同意，包括在應用程式更新之後，可能會導致小幫手無法供語音啟用，直到隱私權問題解決為止。

## <a name="minimum-requirements-for-feature-inclusion"></a>功能包含的最低需求

Windows 使用者可以在中查看並控制其助理應用程式的可用性 **`Settings > Privacy > Voice activation`** 。

 > [!div class="mx-imgBorder"]
 > [![隱私權-應用程式清單](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Assistant 應用程式的 Windows 語音啟用隱私權設定專案")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

若要獲得符合此清單的資格，請與 Microsoft 聯絡 winvoiceassistants@microsoft.com 以開始使用。 根據預設，使用者必須明確啟用中新小幫手的語音啟動功能 **`Settings > Privacy > Voice Activation`** ，應用程式可以使用此通訊協定連結至 `ms-settings:privacy-voiceactivation` 。 允許的應用程式一旦執行並使用了 api，就會出現在清單中 `Windows.ApplicationModel.ConversationalAgent` 。 一旦應用程式取得使用者的麥克風同意，它的語音啟用設定就會是可修改的。

由於 Windows 隱私權設定包含語音啟用如何運作的相關資訊，而且具有控制許可權的標準 UI，因此同時也滿足了洩漏和同意。 [助理] 會保留在此允許清單中，但前提是它不會：

* 誤導或 misinform 使用者有關「語音啟用」或「助理」的語音資料處理
* 過度干擾另一個助理
* 中斷任何其他相關的 Microsoft 原則

如果探索到上述任何一項，Microsoft 可能會從允許清單中移除小幫手，直到問題解決為止。

> [!NOTE]
> 在所有情況下，「語音啟用」許可權都需要「麥克風」許可權。 如果助理應用程式沒有麥克風存取權，就不符合語音啟用的資格，且會在 [語音啟用隱私權] 設定中顯示為停用狀態。

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>包含在麥克風同意中的其他需求

想要讓使用者可以更輕鬆且更順暢地選擇語音啟用的小幫手作者，可以藉由滿足額外的需求來完成這項作業，而不需要額外前往 [設定] 頁面。 一經核准，一旦使用者將麥克風許可權授與 assistant 應用程式，語音啟用就會立即可用。 為符合此條件，助理應用程式必須先執行下列動作，**然後**提示您進行麥克風同意（例如，藉由使用 `AppCapability.RequestAccessAsync` API）：

1. 為使用者提供清楚且顯著的指示，讓應用程式想要接聽使用者的關鍵字，*即使應用程式不*在執行中，而且想要使用者同意
1. 包含有關資料使用方式和隱私權原則的相關資訊，例如官方隱私權聲明的連結
1. 避免出現會洩漏音訊捕捉行為的經驗流程中的任何指示詞或開頭用語（例如，在下列提示字元中按一下 [是]）

如果應用程式完成上述所有工作，就有資格啟用語音啟動功能以及麥克風同意。 請聯絡以取得 winvoiceassistants@microsoft.com 詳細資訊，並檢查第一次使用經驗。

> [!NOTE]
> 超過鎖定的語音啟用不適合使用麥克風存取自動啟用，而且仍然需要使用者流覽語音啟用隱私權頁面，才能啟用助理的上方鎖定存取。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解 Windows 上語音助理的最佳作法](windows-voice-assistants-best-practices.md)