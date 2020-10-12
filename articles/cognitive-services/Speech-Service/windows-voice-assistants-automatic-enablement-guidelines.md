---
title: Windows 上的語音助理隱私權指導方針
titleSuffix: Azure Cognitive Services
description: 依預設啟用語音代理程式語音啟用的指示。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987399"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Windows 上的語音助理隱私權指導方針

請務必清楚瞭解使用者如何收集及使用其語音資料，以及對這些資料的控制權，以及此收集的運作方式。 這些隱私權的核心 facet-- *洩漏* 和 *同意* --對於使用的永遠接聽本質而言，與 Windows 整合的語音助理而言特別重要。

在 Windows 上建立語音助理的開發人員必須在其應用程式內包含清楚的使用者介面元素，以反映助理的接聽功能。

> [!NOTE]
> 若無法針對小幫手應用程式提供適當的洩漏和同意（包括應用程式更新後），可能會導致助理變成無法使用語音啟用，直到隱私權問題解決為止。

## <a name="minimum-requirements-for-feature-inclusion"></a>功能包含的最低需求

Windows 使用者可以在中查看及控制其助理應用程式的可用性 **`Settings > Privacy > Voice activation`** 。

 > [!div class="mx-imgBorder"]
 > [![螢幕擷取畫面顯示控制 Cortana 可用性的選項。 ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "助理應用程式的 Windows 語音啟用隱私權設定專案")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

若要獲得包含在此清單中的資格，請與 Microsoft 聯絡 winvoiceassistants@microsoft.com 以開始使用。 根據預設，使用者必須在中為新的助理明確啟用語音啟用功能 **`Settings > Privacy > Voice Activation`** ，應用程式可以使用此連結的通訊協定 `ms-settings:privacy-voiceactivation` 。 當允許的應用程式執行並使用 api 之後，就會出現在清單中 `Windows.ApplicationModel.ConversationalAgent` 。 當應用程式取得使用者的麥克風同意之後，就可以修改語音啟用設定。

由於 Windows 隱私權設定包含語音啟用運作方式的相關資訊，以及具有控制許可權的標準 UI，因此可同時滿足洩漏和同意。 小幫手將會保留在此允許清單中，前提是它不會：

* 對使用者誤導或 misinform 助理的語音啟用或語音資料處理
* 過度干擾另一個助理
* 中斷任何其他相關的 Microsoft 原則

如果探索到上述任一項，Microsoft 可能會從允許清單中移除助理，直到問題解決為止。

> [!NOTE]
> 在所有情況下，語音啟用許可權都需要麥克風許可權。 如果小幫手應用程式沒有麥克風存取權，則它將不符合語音啟用的資格，而且會出現在 [語音啟用隱私權] 設定中的 [已停用] 狀態。

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>包含在麥克風同意的其他需求

希望讓使用者能夠更輕鬆且更順暢地選擇語音啟用的助理作者可以達成其他需求，以充分履行洩漏和同意，而不需要額外的設定頁面。 一旦通過核准，當使用者授與對助理應用程式的麥克風許可權之後，就會立即啟用語音啟用。 為了達成此目的，小幫手應用程式必須先執行下列動作， **才能** 提示進行麥克風同意 (例如，使用 `AppCapability.RequestAccessAsync` API) ：

1. 提供清楚且顯著的指示給使用者，應用程式會想要接聽使用者的聲音來取得關鍵字， *即使應用程式不*在執行中，而且想要使用者同意
1. 包含有關資料使用量和隱私權原則的相關資訊，例如官方隱私權聲明的連結
1. 請避免任何指示詞或開頭的用語 (例如，在洩漏音訊捕獲行為的體驗流程中，于下列提示處按一下 [是] ) 

如果應用程式完成上述所有動作，就有資格啟用語音啟用功能以及麥克風同意。 請聯絡 winvoiceassistants@microsoft.com 以取得詳細資訊，並查看首次使用體驗。

> [!NOTE]
> 上述的語音啟用不符合可使用麥克風存取的自動啟用，而且仍需要使用者造訪語音啟用隱私權頁面，以啟用助理的上方鎖定存取。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解 Windows 上語音助理的最佳作法](windows-voice-assistants-best-practices.md)