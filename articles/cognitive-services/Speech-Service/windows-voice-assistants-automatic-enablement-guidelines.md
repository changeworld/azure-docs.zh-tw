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
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997776"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Windows 語音助理的隱私權指導方針

使用者必須清楚瞭解如何收集及使用其聲音資料，並對其提供控制該集合發生的時間與方式的重要資訊。 這些隱私權的核心 facet--*洩漏*和*同意*--對於整合到 Windows 的語音助理而言，這對於使用的一律接聽性質而言特別重要。

在 Windows 上建立語音助理的開發人員必須在其應用程式內包含清楚的使用者介面元素，以反映 assistant 的接聽功能。

> [!NOTE]
> 如果無法為助理應用程式提供適當的洩漏和同意，包括在應用程式更新之後，可能會導致小幫手無法供語音啟用，直到隱私權問題解決為止。 

## <a name="minimum-requirements-for-feature-inclusion"></a>功能包含的最低需求

Windows 使用者可以在中**`Settings > Privacy > Voice activation`** 查看並控制其助理應用程式的可用性。

 > [!div class="mx-imgBorder"]
 > [![隱私權-應用程式清單](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Assistant 應用程式的 Windows 語音啟用隱私權設定專案")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

若要成為符合此清單的資格，應用程式必須：

1. 以醒目方式告知其使用者將接聽關鍵字，即使應用程式不在執行中，以及該關鍵字為何
1. 包括如何使用使用者的語音資料的描述，包括相關隱私權原則的連結或參考
1. 通知使用者除了任何應用程式內設定外，使用者還可以在中**`Settings > Privacy > Voice activation`** 查看及修改其隱私權選擇，並選擇性地包含`ms-settings:privacy-voiceactivation`適用于直接存取的通訊協定連結

在符合這些需求並取得 Microsoft 的核准後，當「語音啟用應用程式」清單中已向`Windows.ApplicationModel.ConversationalAgent` api 註冊後，「助理應用程式」將會出現在「語音啟動應用程式」清單中，而且使用者將能夠授與同意應用程式的關鍵字啟用。 根據預設，這兩個設定都`Off`是，而且需要使用者手動流覽 [設定] 頁面以啟用。

> [!NOTE]
> 在所有情況下，「語音啟用」許可權都需要「麥克風」許可權。 如果助理應用程式沒有麥克風存取權，就不符合語音啟用的資格，且會在 [語音啟用隱私權] 設定中顯示為停用狀態。

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>包含在麥克風同意中的其他需求

想要讓使用者更輕鬆且更順暢地選擇語音啟用的小幫手作者，可以藉由滿足上述各項額外的需求來完成這項作業。 在符合這些要求之後，助理應用程式的標準、裝置解除鎖定的語音啟用設定`On`會預設為一次（僅限一次），將麥克風存取權授與應用程式。 如此一來，就不需要額外的設定行程，就能讓語音啟用助理。

額外的需求是 assistant 應用程式必須：

1. 提示進行麥克風同意**之前**（例如使用`AppCapability.RequestAccessAsync` API），請向使用者提供明顯的指示，讓助理應用程式想要接聽使用者的關鍵字，即使應用程式不在執行中，而且想要使用者同意
2. 在要求麥克風存取或使用`Windows.ApplicationModel.ConversationalAgent` api 之前，請**先**包含資料使用方式和隱私權原則的所有相關資訊
3. 避免在體驗流程中公開音訊捕捉行為和要求許可權的任何指示詞或開頭用語（例如，「在下列提示中按一下 [是]）

滿足這些需求之後，一旦授與麥克風存取權，合格的助理應用程式就會出現在符合語音`enabled`啟用功能的應用程式清單中。

> [!NOTE]
> 超過鎖定的語音啟用不適合使用麥克風存取自動啟用，而且仍然需要使用者流覽語音啟用隱私權頁面，才能啟用助理的上方鎖定存取。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解 Windows 上語音助理的最佳作法](windows-voice-assistants-best-practices.md)