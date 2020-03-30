---
title: 在 Azure 活動目錄標識保護中提供風險回饋
description: 如何以及為什麼提供有關身份保護風險檢測的回饋。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be8290f12d64f0c556100c63ec159bd414c6fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382084"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>如何：在 Azure AD 標識保護中提供風險回饋

Azure AD 標識保護允許您提供有關其風險評估的回饋。 以下文檔列出了您希望就 Azure AD 標識保護的風險評估以及我們如何合併提供回饋的方案。

## <a name="what-is-a-detection"></a>什麼是檢測？

從身份風險的角度來看，身份保護檢測是可疑活動的指示器。 這些可疑活動稱為風險檢測。 這些基於身份的檢測可以基於啟發式、機器學習，也可以來自合作夥伴產品。 這些檢測用於確定登錄風險和使用者風險，

* 使用者風險表示標識洩露的可能性。
* 登錄風險表示登錄被洩露的可能性（例如，登錄未由標識擁有者授權）。

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>為什麼我應該向 Azure AD 的風險評估提供風險回饋？ 

提供 Azure AD 風險回饋的原因有多種：

- **您發現 Azure AD 的使用者或登錄風險評估不正確**。 例如，"Risky 登錄"報表中顯示的登錄是良性的，該登錄上的所有檢測都是誤報。
- **您驗證 Azure AD 的使用者或登錄風險評估是正確的**。 例如，"Risky 登錄"報表中顯示的登錄確實是惡意的，您希望 Azure AD 知道該登錄中的所有檢測都是真正的陽性。
- **修復了 Azure AD 標識保護之外該使用者的風險**，並希望更新該使用者的風險級別。

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Azure AD 如何使用我的風險回饋？

Azure AD 使用您的回饋來更新基礎使用者和/或登錄的風險以及這些事件的準確性。 此回饋有助於保護最終使用者的安全。 例如，一旦確認登錄已受到威脅，Azure AD 會立即將使用者的風險和登錄的總風險（非即時風險）增加到"高"。 如果此使用者包含在使用者風險策略中，以強制高風險使用者安全地重置其密碼，則使用者將在下次登錄時自動修復自身。

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>我應該如何提供風險回饋，以及引擎蓋下會發生什麼？

以下是向 Azure AD 提供風險回饋的方案和機制。

| 狀況 | 如何提供回饋？ | 引擎蓋下會發生什麼？ | 注意 |
| --- | --- | --- | --- |
| **登錄未洩露（誤報）** <br> "Risky 登錄"報告顯示了風險登錄[風險狀態 = 風險狀態]，但該登錄未受到危害。 | 選擇登錄，然後按一下"確認登錄安全"。 | Azure AD 會將登錄的聚合風險移動到無 [風險狀態 = 已確認的安全;風險級別（聚合） = -] 並將扭轉其對使用者風險的影響。 | 目前，"確認登錄安全"選項僅在"Risky 登錄"報告中可用。 |
| **登錄已洩露（正真）** <br> "Risky 登錄"報告顯示風險登錄 [風險狀態 = 風險]低風險 [風險級別（聚合） = 低]，並且該登錄確實受到了危害。 | 選擇登錄，然後按一下"確認登錄已洩露"。 | Azure AD 會將登錄的聚合風險和使用者風險移動到高 [風險狀態 ] 已確認已洩露;風險級別 = 高]。 | 目前，"確認登錄已洩露"選項僅在"Risky 登錄"報告中可用。 |
| **使用者受損（正）** <br> "Risky 使用者"報告顯示風險使用者 [風險狀態 = 風險]風險低 [風險級別 = 低]且該使用者確實受到危害。 | 選擇使用者並按一下"確認使用者受損"。 | Azure AD 會將使用者風險移動到高 [風險狀態 ] 已確認已洩露;風險級別 = 高*，並將添加新檢測"管理員確認的使用者已洩露"。 | 目前，"確認使用者受損"選項僅在"Risky 使用者"報告中可用。 <br> 檢測"管理員確認的使用者已洩露"顯示在"風險使用者"報告中的"風險檢測未連結到登錄"選項卡中。 |
| **Azure AD 標識保護之外修復的使用者（正正= 修復）** <br> "Risky 使用者"報表顯示風險使用者，我隨後在 Azure AD 標識保護之外對使用者進行修正。 | 1. 選擇使用者並按一下"確認使用者受損"。 （此過程向 Azure AD 確認使用者確實受到了危害。 <br> 2. 等待使用者的"風險級別"轉到"高"。 （這一次為 Azure AD 提供了將上述回饋帶到風險引擎所需的時間。 <br> 3. 選擇使用者並按一下"消除使用者風險"。 （此過程向 Azure AD 確認使用者不再受到威脅。 |  Azure AD 將使用者風險移動到無 [風險狀態 ] 已消除;風險級別 = -] 並關閉所有具有活動風險的現有登錄的風險。 | 按一下"消除使用者風險"將關閉使用者和過去登錄的所有風險。無法撤銷此操作。 |
| **使用者未洩露（誤報）** <br> "Risky 使用者"報表在風險使用者中顯示，但使用者不會受到威脅。 | 選擇使用者並按一下"消除使用者風險"。 （此過程向 Azure AD 確認使用者未受危害。 | Azure AD 將使用者風險移動到無 [風險狀態 ] 已消除;風險級別 = -。 | 按一下"消除使用者風險"將關閉使用者和過去登錄的所有風險。無法撤銷此操作。 |
| 我想關閉使用者風險，但我不確定使用者是否受到危害/安全。 | 選擇使用者並按一下"消除使用者風險"。 （此過程向 Azure AD 確認使用者不再受到威脅。 | Azure AD 將使用者風險移動到無 [風險狀態 ] 已消除;風險級別 = -。 | 按一下"消除使用者風險"將關閉使用者和過去登錄的所有風險。無法撤銷此操作。 我們建議您通過按一下"重置密碼"或請求使用者安全地重置/更改其憑據來修復使用者。 |

有關身份保護中使用者風險檢測的回饋將離線處理，可能需要一些時間進行更新。 風險處理狀態列將提供回饋處理的目前狀態。

![風險使用者報告的風險處理狀態](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>後續步驟

- [Azure 活動目錄標識保護風險檢測引用](risk-events-reference.md)
