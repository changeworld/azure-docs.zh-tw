---
title: 以使用者風險為基礎的條件式存取-Azure Active Directory
description: 使用 Identity Protection 使用者風險建立條件式存取原則
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
ms.openlocfilehash: a015f7dd58bae328a8c18e3de3202c0ca615c9cf
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86034804"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>條件式存取：以使用者風險為基礎的條件式存取

Microsoft 與研究人員、執法機關、Microsoft 的各種安全性小組及其他受信任的來源合作，以找出流失的使用者名稱和密碼配對。 具有 Azure AD Premium P2 授權的組織可以建立包含[Azure AD Identity Protection 使用者風險](../identity-protection/concept-identity-protection-risks.md#user-risk)偵測的條件式存取原則。 

有兩個位置可以指派此原則。 組織應選擇下列其中一個選項，以啟用以使用者風險為基礎的條件式存取原則，需要安全的密碼變更。

## <a name="enable-with-conditional-access-policy"></a>使用條件式存取原則啟用

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下選取 [使用者和群組]。
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [排除] 底下選取 [使用者和群組]，然後選擇組織的緊急存取或急用帳戶。 
   1. 選取 [完成] 。
1. 在 [**雲端應用程式] 或 [動作**] 底下  >  ** **，選取 [**所有雲端應用程式**]。
1. 在 [**條件**  >  **使用者風險**] **Configure**底下，將設定為 **[是]**。 在 [**設定強制執行原則所需的使用者風險層級**] 底下，選取 [**高**]，然後選取 [**完成**]。
1. 在 [**存取控制**  >  **授**與] 底下，選取 **[授與存取權**，**需要密碼變更**]，然後選取 [**選取**]
1. 確認您的設定，並將 [**啟用原則**] 設定為 [**開啟**]。
1. 選取 [建立] 以建立以啟用您的原則。

## <a name="enable-through-identity-protection"></a>透過身分識別保護啟用

1. 登入 **Azure 入口網站**。
1. 選取 [**所有服務**]，然後流覽至**Azure AD Identity Protection**。
1. 選取 [**使用者風險原則**]。
1. 在 [**指派**] 底下，選取 [**使用者**]。
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [**排除**] 底下，選取 [**選取排除的使用者**]，選擇您組織的緊急存取或中斷透明帳戶，然後選取 [**選取**]。
   1. 選取 [完成] 。
1. 在 [**條件**] 底下，選取 [**使用者風險**]，然後選擇 [**高**]。
   1. 依序選取 [**選取**] 和 [**完成**]。
1. 在 [**控制項**  >  **存取**] 底下，選擇 [**允許存取**]，然後選取 [**需要變更密碼**]。
   1. 選取 [選取] 。
1. 將 [**強制執行原則**] 設定為 [**開啟**]。
1. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

[條件式存取一般原則](concept-conditional-access-policy-common.md)

[以風險為基礎的條件式存取](howto-conditional-access-policy-risk.md)

[使用條件式存取報告專用模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)

[Azure Active Directory Identity Protection 是什麼？](../identity-protection/overview.md)
