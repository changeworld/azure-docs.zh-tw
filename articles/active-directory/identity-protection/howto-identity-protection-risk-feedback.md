---
title: 提供 Azure Active Directory Identity Protection 中的風險意見反應
description: 您應如何和為何提供有關身分識別保護風險偵測的意見反應。
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74382084"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>如何：在 Azure AD Identity Protection 中提供風險回饋

Azure AD Identity Protection 可讓您提供有關其風險評估的意見反應。 下列檔列出您想要對 Azure AD Identity Protection 的風險評估提供意見反應，以及如何將其納入的案例。

## <a name="what-is-a-detection"></a>什麼是偵測？

身分識別保護偵測是來自身分識別風險觀點的可疑活動指示器。 這些可疑的活動稱為風險偵測。 這些以身分識別為基礎的偵測可以根據啟發學習法、機器學習服務或來自合作夥伴產品。 這些偵測是用來判斷登入風險和使用者風險，

* 使用者風險代表身分識別遭到入侵的可能性。
* 登入風險代表登入遭到入侵的機率（例如，登入未由身分識別擁有者授權）。

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>為什麼我應該將風險的意見反應提供給 Azure AD 的風險評估？ 

有幾個原因會讓您提供 Azure AD 風險的意見反應：

- **您發現 Azure AD 的使用者或登入風險評估不正確**。 例如，[具風險的登入] 報告中顯示的登入是良性的，而且該登入的所有偵測都是誤報。
- **您已驗證 Azure AD 的使用者或登入風險評估正確**。 例如，[具風險的登入] 報告中顯示的登入確實是惡意的，您希望 Azure AD 知道該登入的所有偵測都是真肯定的。
- **您已在 Azure AD Identity Protection 外部補救該使用者的風險**，而您想要更新使用者的風險層級。

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Azure AD 如何使用我的風險意見反應？

Azure AD 會使用您的意見反應來更新基礎使用者和/或登入的風險，以及這些事件的精確度。 此意見反應可協助保護使用者的安全。 例如，確認登入遭入侵後，Azure AD 立即將使用者的風險和登入匯總風險（非即時風險）增加到高。 如果使用者風險原則中包含此使用者，以強制高風險使用者安全地重設其密碼，則使用者會在下一次登入時自動進行補救。

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>我應該如何提供風險的意見反應，以及幕後會發生什麼事？

以下是將風險意見反應提供給 Azure AD 的案例和機制。

| 狀況 | 如何提供意見反應？ | 幕後會發生什麼事？ | 注意 |
| --- | --- | --- | --- |
| **登入未洩露（誤報）** <br> [有風險的登入] 報告會顯示有風險的登入 [風險狀態 = 有風險]，但該登入並未受到危害。 | 選取 [登入]，然後按一下 [確認登入安全]。 | Azure AD 會將登入的匯總風險移至 [無] [風險狀態 = 已確認安全];風險層級（匯總） =-]，將會反轉其對使用者風險的影響。 | 目前，[確認登入安全] 選項僅適用于「有風險的登入」報表。 |
| **登入遭盜用（真肯定）** <br> [有風險的登入] 報告會顯示有風險的登入 [風險狀態 = 發生風險]，但有低風險 [風險層級（匯總） = 低]，而且登入確實遭到入侵。 | 選取 [登入]，然後按一下 [確認登入遭盜用]。 | Azure AD 會將登入的匯總風險和使用者風險移至高 [風險狀態 = 已確認遭到入侵;風險層級 = 高]。 | 目前，「確認登入遭盜用」選項僅適用于「有風險的登入」報表。 |
| **使用者遭到入侵（真肯定）** <br> [具風險的使用者] 報表會顯示有風險的使用者 [風險狀態 = 有風險] 的風險低 [風險層級 = 低]，而且該使用者已確實遭到入侵。 | 選取使用者，然後按一下 [確認使用者遭盜用]。 | Azure AD 會將使用者風險移至 [高] [風險狀態 = 已確認遭到盜用];風險層級 = 高]，並會新增偵測「系統管理員已確認使用者遭盜用」。 | 目前，只有「有風險的使用者」報表可以使用 [確認使用者已遭入侵] 選項。 <br> 系統會在 [具風險的使用者] 報告中，于 [未連結至登入的風險偵測] 索引標籤中，顯示「系統管理員已確認使用者已遭入侵」。 |
| **在 Azure AD Identity Protection 外部補救的使用者（真肯定 + 已補救）** <br> 「具風險的使用者」報表會顯示有風險的使用者，而且我接著會在 Azure AD Identity Protection 外部補救使用者。 | 1. 選取使用者，然後按一下 [確認使用者遭盜用]。 （此程式會確認 Azure AD 使用者確實遭到入侵）。 <br> 2. 等待使用者的 [風險層級] 移至 [高]。 （這段時間提供了 Azure AD 所需的時間，讓您將上述意見反應授與風險引擎）。 <br> 3. 選取使用者，然後按一下 [解除使用者風險]。 （此程式會確認 Azure AD 使用者不再遭到入侵）。 |  Azure AD 會將使用者風險移至 none [風險狀態 = 已關閉;風險層級 =-]，並關閉所有具有有效風險的現有登入風險。 | 按一下 [解除使用者風險] 將會關閉使用者和過去登入的所有風險。此動作無法復原。 |
| **使用者未遭入侵（誤報）** <br> 「具風險的使用者」報表會顯示「具有風險的使用者」，但使用者不會遭到入侵。 | 選取使用者，然後按一下 [解除使用者風險]。 （此程式會確認 Azure AD 使用者不會遭到入侵）。 | Azure AD 會將使用者風險移至 none [風險狀態 = 已關閉;風險層級 =-]。 | 按一下 [解除使用者風險] 將會關閉使用者和過去登入的所有風險。此動作無法復原。 |
| 我想要關閉使用者風險，但不確定使用者是否遭到入侵/安全。 | 選取使用者，然後按一下 [解除使用者風險]。 （此程式會確認 Azure AD 使用者不再遭到入侵）。 | Azure AD 會將使用者風險移至 none [風險狀態 = 已關閉;風險層級 =-]。 | 按一下 [解除使用者風險] 將會關閉使用者和過去登入的所有風險。此動作無法復原。 建議您按一下 [重設密碼] 來補救使用者，或要求使用者安全地重設/變更其認證。 |

有關 Identity Protection 中使用者風險偵測的意見反應會離線處理，而且可能需要一些時間來更新。 [風險處理狀態] 欄會提供意見處理的目前狀態。

![有風險的使用者報告的風險處理狀態](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>後續步驟

- [Azure Active Directory Identity Protection 風險偵測參考](risk-events-reference.md)
