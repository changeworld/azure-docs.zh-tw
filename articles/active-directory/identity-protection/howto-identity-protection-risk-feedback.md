---
title: 在 Azure Active Directory Identity Protection 中提供風險意見反應
description: 您應如何及為何提供有關 Identity Protection 風險偵測的意見反應。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939ffc9e2a8fb8ce3a25dc212e3df34dc6bb2ec1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88950352"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>如何：在 Azure AD Identity Protection 中提供風險意見反應

Azure AD Identity Protection 可讓您對其風險評量提供意見反應。 下表列出您想要提供意見反應給 Azure AD Identity Protection 的風險評定，以及我們如何併入的案例。

## <a name="what-is-a-detection"></a>什麼是偵測？

身分識別保護偵測是來自身分識別風險觀點的可疑活動指標。 這些可疑活動稱為風險偵測。 這些以身分識別為基礎的偵測可以根據啟發學習法、機器學習服務，也可以來自合作夥伴產品。 這些偵測用來判斷登入風險和使用者風險。

* 使用者風險代表身分識別遭到入侵的可能性。
* 登入風險代表登入遭入侵的機率 (例如，登入未獲身分識別擁有者) 授權。

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>為什麼我應該將風險意見反應提供給 Azure AD 的風險評量？ 

有幾個原因會讓您提出 Azure AD 風險反應：

- **您發現 Azure AD 的使用者或登入風險評定不正確**。 例如，在 [具風險的登入] 報告中顯示的登入是良性的，而且該登入上的所有偵測都是誤報。
- **您已驗證 Azure AD 的使用者或登入風險評估是否正確**。 例如，[具風險的登入] 報告中顯示的登入確實是惡意的，而您想要 Azure AD 知道該登入上的所有偵測都是真肯定的。
- **您已在 Azure AD Identity Protection 之外補救該使用者的風險** ，而您想要更新使用者的風險層級。

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Azure AD 如何使用我的風險意見反應？

Azure AD 會使用您的意見反應來更新基礎使用者及/或登入的風險，以及這些事件的精確度。 這種意見反應有助於保護終端使用者。 例如，一旦您確認登入遭到入侵，Azure AD 會立即增加使用者的風險和登入的匯總風險， (非即時風險) 高。 如果使用者風險原則中包含此使用者，以強制高風險使用者安全地重設其密碼，則使用者會在下次登入時自動修復。

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>如何提供風險的意見反應，以及幕後發生什麼事？

以下是將風險意見反應提供給 Azure AD 的案例和機制。

| 案例 | 如何提供意見反應？ | 幕後會發生什麼事？ | 注意 |
| --- | --- | --- | --- |
| **登入未遭盜用 (誤報) ** <br> [具風險的登入] 報告顯示具有風險的登入 [風險狀態 = 有風險]，但該登入未遭盜用。 | 選取 [登入]，然後按一下 [確認登入安全]。 | Azure AD 會將登入的匯總風險移至無 [風險狀態 = 已確認安全;風險層級 (匯總) =-]，並會反轉其對使用者風險的影響。 | 目前，[確認登入安全] 選項僅適用于「具風險的登入」報告。 |
| **登入遭盜用 (真肯定) ** <br> [具風險的登入] 報表會顯示具有低風險的風險登入 [風險狀態 = 有風險] [風險層級 (匯總) = 低]，且登入確實遭到盜用。 | 選取登入，然後按一下 [確認登入遭盜用]。 | Azure AD 會將登入的匯總風險和使用者風險移至高 [風險狀態 = 已確認遭盜用;風險層級 = 高]。 | 目前，[確認登入遭盜用] 選項僅適用于「具風險的登入」報告。 |
| **使用者遭盜用 (真肯定) ** <br> [具風險的使用者] 報表會顯示具有低風險的風險使用者 [風險狀態 = 有風險] [風險層級 = 低]，而該使用者確實遭到盜用。 | 選取使用者，然後按一下 [確認使用者遭盜用]。 | Azure AD 會將使用者風險移至高 [風險狀態 = 已確認遭盜用;風險層級 = High]，並會新增偵測 ' 系統管理員已確認消費者遭盜用 '。 | 目前，[確認使用者遭盜用] 選項只適用于 [具風險的使用者] 報告。 <br> 偵測 ' 系統管理員已確認消費者遭盜用 ' 會顯示在 [具風險的使用者] 報告中的 [風險偵測未連結至登入] 索引標籤中。 |
| **使用者在 Azure AD Identity Protection 之外補救 (真正的正面 + 補救) ** <br> [具風險的使用者] 報表會顯示具有風險的使用者，並接著在 Azure AD Identity Protection 之外補救使用者。 | 1. 選取使用者，然後按一下 [確認使用者遭盜用]。  (此程式確認 Azure AD 使用者確實遭到入侵。 )  <br> 2. 等待使用者的「風險層級」移至「高」。  (這段時間可讓 Azure AD 將上述意見反應帶到風險引擎所需的時間。 )  <br> 3. 選取使用者，然後按一下 [解除使用者風險]。  (此程式確認 Azure AD 使用者已不再遭到入侵。 )  |  Azure AD 將使用者風險移至 [無] [風險狀態 = 已關閉;風險層級 =-]，並在具有作用中風險的所有現有登入上關閉風險。 | 按一下 [解除使用者風險] 將會關閉使用者與過去登入的所有風險。此動作無法復原。 |
| **使用者未遭盜用 (誤報) ** <br> [具風險的使用者] 報告顯示有風險的使用者，但使用者未遭入侵。 | 選取使用者，然後按一下 [解除使用者風險]。  (此程式確認 Azure AD 使用者未遭入侵。 )  | Azure AD 將使用者風險移至 [無] [風險狀態 = 已關閉;風險層級 =-]。 | 按一下 [解除使用者風險] 將會關閉使用者與過去登入的所有風險。此動作無法復原。 |
| 我想要關閉使用者風險，但不確定使用者是否遭到入侵/安全。 | 選取使用者，然後按一下 [解除使用者風險]。  (此程式確認 Azure AD 使用者已不再遭到入侵。 )  | Azure AD 將使用者風險移至 [無] [風險狀態 = 已關閉;風險層級 =-]。 | 按一下 [解除使用者風險] 將會關閉使用者與過去登入的所有風險。此動作無法復原。 建議您按一下 [重設密碼] 來補救使用者，或要求使用者安全地重設/變更其認證。 |

Identity Protection 中使用者風險偵測的意見反應會離線處理，而且可能需要一些時間才會更新。 [風險處理狀態] 欄會提供目前的意見反應處理狀態。

![具風險使用者報告的風險處理狀態](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>接下來的步驟

- [Azure Active Directory Identity Protection 風險偵測參考](./concept-identity-protection-risks.md)