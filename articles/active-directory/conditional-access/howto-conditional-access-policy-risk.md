---
title: 以風險為基礎的條件式存取-Azure Active Directory
description: 使用 Identity Protection 登入風險建立條件式存取原則
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2564e3f246d23028d6c73832a25f37110e7670c8
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948091"
---
# <a name="conditional-access-sign-in-risk-based-conditional-access"></a>條件式存取：以登入風險為基礎的條件式存取

大部分使用者都有可追蹤的正常行為，而當他們超出此標準範圍時，光是允許他們登入就可能會有風險。 您可能會想要封鎖該使用者，或只要求他們執行多重要素驗證，以證明他們確實是他們所聲稱的人。 

登入風險表示指定的驗證要求未獲身分識別擁有者授權的機率。 具有 Azure AD Premium P2 授權的組織可以建立條件式存取原則，其中包含 [Azure AD Identity Protection 登入風險](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)偵測。

有兩個位置可以指派此原則。 組織應該選擇下列其中一個選項，以啟用以登入風險為基礎的條件式存取原則，而此原則需要安全的密碼變更。

## <a name="enable-with-conditional-access-policy"></a>使用條件式存取原則啟用

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下選取 [使用者和群組]。
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [排除] 底下選取 [使用者和群組]，然後選擇組織的緊急存取或急用帳戶。 
   1. 選取 [完成] 。
1. 在 [**雲端應用程式] 或 [動作**] 下  >  ** **，選取 [**所有雲端應用程式**]。
1. 在 [**條件**  >  **使用者風險**] **Configure**下，將 [設定為**是]**。 在 **[選取此原則將套用的登入風險層級**] 下 
   1. 選取 [ **高** ] 和 [ **中**]。
   1. 選取 [完成]。
1. 在 [存取控制] > [授與] 底下選取 [授與存取權] 和 [需要多重要素驗證]，然後選取 [選取]。
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

## <a name="enable-through-identity-protection"></a>透過 Identity Protection 啟用

1. 登入 **Azure 入口網站**。
1. 選取 [ **所有服務**]，然後流覽至 **Azure AD Identity Protection**。
1. 選取 [登 **入風險原則**]。
1. 在 [ **指派**] 底下，選取 [ **使用者**]。
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [ **排除**] 下，選取 [ **選取排除的使用者**]，選擇您組織的緊急存取或玻璃帳戶，然後選取 [ **選取**]。
   1. 選取 [完成]。
1. 在 [ **條件**] 底下，選取 [登 **入風險**]，然後選擇 [ **中] 和 [上方**]。
   1. 選取 [ **選取**]，然後選取 [ **完成**]。
1. 在 [**控制項**  >  **存取**] 底下，選擇 [**允許存取**]，然後選取 [**需要多重要素驗證**]。
   1. 選取 [選取]  。
1. 將 [ **強制執行原則** ] 設定為 [ **開啟**]。
1. 選取 [儲存]  。

## <a name="next-steps"></a>後續步驟

[條件式存取一般原則](concept-conditional-access-policy-common.md)

[使用者風險型條件式存取](howto-conditional-access-policy-risk-user.md)

[使用條件式存取報告專用模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)

[Azure Active Directory Identity Protection 是什麼？](../identity-protection/overview-identity-protection.md)