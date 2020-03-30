---
title: Azure AD 標識保護原則
description: 標識使用身份保護啟用的三個策略
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887412"
---
# <a name="identity-protection-policies"></a>身分識別保護原則

Azure 活動目錄標識保護包括管理員可以選擇啟用的三個預設策略。 這些策略包括有限的自訂，但適用于大多陣列織。 所有策略都允許排除使用者，如[緊急訪問或碎玻璃管理員帳戶](../users-groups-roles/directory-emergency-access.md)。

![身分識別保護原則](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA 註冊策略

身份保護可説明組織使用需要在登錄時進行註冊的條件訪問策略來推出 Azure 多重要素驗證 （MFA）。 啟用此策略是確保組織中的新使用者在第一天註冊 MFA 的好方法。 多重要素驗證是身份保護中風險事件的自修複方法之一。 自我修復允許使用者自行採取行動，以減少説明台呼叫量。

有關 Azure 多重要素驗證的詳細資訊，請參閱文章"[如何工作：Azure 多重要素驗證](../authentication/concept-mfa-howitworks.md)"。

## <a name="sign-in-risk-policy"></a>登入風險原則

身份保護分析來自每個登錄（即時和離線）的信號，並根據使用者未執行登錄的概率計算風險評分。 管理員可以基於此風險評分信號做出決策，以強制實施組織要求。 管理員可以選擇阻止訪問、允許訪問或允許訪問，但需要多重要素驗證。

如果檢測到風險，使用者可以執行多重要素驗證以自我修復和關閉危險的登錄事件，以防止管理員出現不必要的噪音。

> [!NOTE] 
> 在觸發登錄風險策略之前，使用者必須以前已註冊 Azure 多重要素驗證。

### <a name="custom-conditional-access-policy"></a>自訂條件訪問策略

管理員還可以選擇創建自訂條件訪問策略，包括登錄風險作為分配條件。 有關條件訪問的詳細資訊，請參閱文章"[什麼是條件訪問？](../conditional-access/overview.md)

![自訂條件訪問登錄風險策略](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>使用者風險原則

身份保護可以計算它認為使用者行為的正常內容，並用它來根據使用者的風險做出決策。 使用者風險是計算標識被洩露的可能性。 管理員可以基於此風險評分信號做出決策，以強制實施組織要求。 管理員可以選擇阻止訪問、允許訪問或允許訪問，但需要使用[Azure AD 自助服務密碼重設更改密碼](../authentication/howto-sspr-deployment.md)。

如果檢測到風險，使用者可以執行自助服務密碼重設以自我修復並關閉使用者風險事件，以防止管理員出現不必要的噪音。

> [!NOTE] 
> 在觸發使用者風險策略之前，使用者必須以前已註冊進行自助服務密碼重設。

## <a name="next-steps"></a>後續步驟

- [啟用 Azure AD 自助服務密碼重設](../authentication/howto-sspr-deployment.md)

- [啟用 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [啟用 Azure 多重要素驗證註冊策略](howto-identity-protection-configure-mfa-policy.md)

- [啟用登錄和使用者風險策略](howto-identity-protection-configure-risk-policies.md)
