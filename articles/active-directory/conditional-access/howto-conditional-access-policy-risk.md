---
title: 條件訪問 - 基於風險的條件訪問 - Azure 活動目錄
description: 創建條件訪問策略，以啟用對策略的身份驗證增強功能
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295145"
---
# <a name="conditional-access-risk-based-conditional-access"></a>條件訪問：基於風險的條件訪問

具有 Azure AD 高級 P2 許可證的組織可以創建條件訪問策略，以包含 Azure AD 標識保護風險檢測。 有三個預設策略可以開箱即用地啟用。 

* 要求所有使用者註冊 Azure 多重要素驗證。
* 要求對高風險使用者更改密碼。
* 對於具有中等或高登錄風險的使用者，需要多重要素驗證。

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>要求所有使用者註冊 Azure 多重要素驗證

啟用此策略將要求所有使用者在 14 天內註冊 Azure 多重要素驗證。 

1. 登錄到 Azure**門戶**。
1. 按一下 [所有服務]****，然後瀏覽至 [Azure AD Identity Protection]****。
1. 按一下 [MFA 註冊]****。
1. 在 **"分配"** 下，選擇 **"使用者**"。
   1. 在 **"包括**"下，選擇**所有使用者**。
   1. 在 **"排除"** 下，**選擇"選擇排除使用者**"，選擇組織的緊急訪問或碎玻璃帳戶，然後選擇 **"選擇**"。 
   1. 選擇 **"完成**"。
1. 將 **"強制策略**"設置為 **"打開**"。
1. 按一下 [儲存]****。

## <a name="require-a-password-change-high-risk-users"></a>要求更改密碼高風險使用者

Microsoft 與研究人員、執法機關，Microsoft 的各個安全性小組和其他受信任的來源合作，以找出使用者名稱和密碼的配對。 當其中一個配對符合您環境中的帳戶時，就可以使用下列原則觸發風險型密碼變更。

1. 登錄到 Azure**門戶**。
1. 按一下 [所有服務]****，然後瀏覽至 [Azure AD Identity Protection]****。
1. 按一下**使用者風險策略**。
1. 在 **"分配"** 下，選擇 **"使用者"**
   1. 在 **"包括**"下，選擇**所有使用者**。
   1. 在 **"排除"** 下，**選擇"選擇排除使用者**"，選擇組織的緊急訪問或碎玻璃帳戶，然後選擇 **"選擇**"。
   1. 選擇 **"完成**"。
1. 在 **"條件"下**，選擇 **"使用者風險**"，然後選擇 **"高**"。
   1. 按一下 **"選擇**"然後**完成**。
1. 在 **"控制** > **訪問**"下，選擇 **"允許訪問**"，然後選擇 **"需要密碼更改**"。
   1. 按一下 **"選擇**"。
1. 將 **"強制策略**"設置為 **"打開**"。
1. 按一下 [儲存]****。

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>需要 MFA 中等或高登錄風險使用者

大部分使用者都有可追蹤的正常行為，而當他們超出此標準範圍時，光是允許他們登入就可能會有風險。 您可能希望阻止該使用者，或者只是要求他們執行多重要素驗證，以證明他們實際上就是他們所說的人。 若要在偵測到有風險的登入時啟用需要 MFA 的原則，請啟用下列原則。

1. 登錄到 Azure**門戶**。
1. 按一下 [所有服務]****，然後瀏覽至 [Azure AD Identity Protection]****。
1. 按一下**登錄風險策略**
1. 在 **"分配"** 下，選擇 **"使用者"**
   1. 在 **"包括**"下，選擇**所有使用者**。
   1. 在 **"排除"** 下，**選擇"選擇排除使用者**"，選擇組織的緊急訪問或碎玻璃帳戶，然後選擇 **"選擇**"。
   1. 選擇 **"完成**"。
1. 在 **"條件"下**，選擇**登錄風險**，然後選擇 **"中"和"以上**"。
   1. 按一下 **"選擇**"然後**完成**。
1. 在 **"控制** > **訪問**"下，選擇 **"允許訪問**"，然後選擇 **"需要多重要素驗證**"。
   1. 按一下 **"選擇**"。
1. 將 **"強制策略**"設置為 **"打開**"。
1. 按一下 [儲存]****。

## <a name="next-steps"></a>後續步驟

[條件訪問通用策略](concept-conditional-access-policy-common.md)

[使用僅條件訪問報告模式確定影響](howto-conditional-access-report-only.md)

[使用條件訪問"如果"工具類比登錄行為](troubleshoot-conditional-access-what-if.md)

[運作方式：Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Azure Active Directory Identity Protection 是什麼？](../identity-protection/overview.md)
