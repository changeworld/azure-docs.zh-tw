---
title: 條件式存取-以風險為基礎的條件式存取-Azure Active Directory
description: 建立條件式存取原則，以啟用原則的身分識別保護增強功能
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8054d8985596095db32d9262322d7fb0f4aab8c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295145"
---
# <a name="conditional-access-risk-based-conditional-access"></a>條件式存取：以風險為基礎的條件式存取

具有 Azure AD Premium P2 授權的組織可以建立包含 Azure AD Identity Protection 風險偵測的條件式存取原則。 有三個預設原則可供現成啟用。 

* 要求所有使用者註冊 Azure 多重要素驗證。
* 需要對具有高風險的使用者進行密碼變更。
* 對於具有中等或高登入風險的使用者，需要多重要素驗證。

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>要求所有使用者註冊 Azure 多重要素驗證

啟用此原則將會要求所有使用者在14天內註冊 Azure 多重要素驗證。 

1. 登入 **Azure 入口網站**。
1. 按一下 [所有服務]****，然後瀏覽至 [Azure AD Identity Protection]****。
1. 按一下 [MFA 註冊]****。
1. 在 [**指派**] 底下，選取 [**使用者**]。
   1. 在 [**包含**] 底下，選取 [**所有使用者**]。
   1. 在 [**排除**] 底下，選取 [**選取排除的使用者**]，選擇您組織的緊急存取或中斷透明帳戶，然後選取 [**選取**]。 
   1. 選取 [完成]  。
1. 將 [**強制執行原則**] 設定為 [**開啟**]。
1. 按一下 **[儲存]** 。

## <a name="require-a-password-change-high-risk-users"></a>需要密碼變更高風險使用者

Microsoft 與研究人員、執法機關，Microsoft 的各個安全性小組和其他受信任的來源合作，以找出使用者名稱和密碼的配對。 當其中一個配對符合您環境中的帳戶時，就可以使用下列原則觸發風險型密碼變更。

1. 登入 **Azure 入口網站**。
1. 按一下 [所有服務]****，然後瀏覽至 [Azure AD Identity Protection]****。
1. 按一下 [**使用者風險原則**]。
1. 在 [**指派**] 底下，選取 [**使用者**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**]。
   1. 在 [**排除**] 底下，選取 [**選取排除的使用者**]，選擇您組織的緊急存取或中斷透明帳戶，然後選取 [**選取**]。
   1. 選取 [完成]  。
1. 在 [**條件**] 底下，選取 [**使用者風險**]，然後選擇 [**高**]。
   1. 依序按一下 [**選取**] 和 [**完成**]。
1. 在 [**控制項** > **存取**] 底下，選擇 [**允許存取**]，然後選取 [**需要變更密碼**]。
   1. 按一下 [選取]  。
1. 將 [**強制執行原則**] 設定為 [**開啟**]。
1. 按一下 **[儲存]** 。

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>需要 MFA medium 或高的登入風險使用者

大部分使用者都有可追蹤的正常行為，而當他們超出此標準範圍時，光是允許他們登入就可能會有風險。 您可能想要封鎖該使用者，或只是要求他們執行多重要素驗證，證明他們真的是他們所說的。 若要在偵測到有風險的登入時啟用需要 MFA 的原則，請啟用下列原則。

1. 登入 **Azure 入口網站**。
1. 按一下 [所有服務]****，然後瀏覽至 [Azure AD Identity Protection]****。
1. 按一下 [登**入風險原則**]
1. 在 [**指派**] 底下，選取 [**使用者**]
   1. 在 [**包含**] 底下，選取 [**所有使用者**]。
   1. 在 [**排除**] 底下，選取 [**選取排除的使用者**]，選擇您組織的緊急存取或中斷透明帳戶，然後選取 [**選取**]。
   1. 選取 [完成]  。
1. 在 [**條件**] 底下，選取 [登**入風險**]，然後選擇 [**中] 和 [以上**]。
   1. 依序按一下 [**選取**] 和 [**完成**]。
1. 在 [**控制項** > **存取**] 底下，選擇 [**允許存取**]，然後選取 [**需要多重要素驗證**]。
   1. 按一下 [選取]  。
1. 將 [**強制執行原則**] 設定為 [**開啟**]。
1. 按一下 **[儲存]** 。

## <a name="next-steps"></a>後續步驟

[條件式存取的一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取僅限報告模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)

[運作方式：Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Azure Active Directory Identity Protection 是什麼？](../identity-protection/overview.md)
