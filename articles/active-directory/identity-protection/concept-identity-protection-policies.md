---
title: Azure AD Identity Protection 原則
description: 識別使用 Identity Protection 啟用的三個原則
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "72887412"
---
# <a name="identity-protection-policies"></a>身分識別保護原則

Azure Active Directory Identity Protection 包含三個系統管理員可選擇啟用的預設原則。 這些原則包括有限的自訂，但適用于大部分的組織。 所有的原則都允許排除使用者，例如您的[緊急存取或半透明的系統管理員帳戶](../users-groups-roles/directory-emergency-access.md)。

![身分識別保護原則](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA 註冊原則

Identity Protection 可協助組織使用需要在登入時註冊的條件式存取原則，來推出 Azure 多重要素驗證（MFA）。 啟用此原則是確保組織中的新使用者在第一天註冊 MFA 的絕佳方式。 多重要素驗證是身分識別保護內風險事件的其中一個自我補救方法。 自我修復可讓您的使用者自行採取行動，以減少技術服務人員電話量。

如需有關 Azure 多因素驗證的詳細資訊，請參閱[其運作方式： Azure 多重要素驗證](../authentication/concept-mfa-howitworks.md)。

## <a name="sign-in-risk-policy"></a>登入風險原則

Identity Protection 會分析每一次登入的信號，包括即時和離線，並根據使用者未執行登入的機率來計算風險分數。 系統管理員可以根據此風險分數指示來做出決定，以強制執行組織需求。 系統管理員可以選擇封鎖存取、允許存取，或允許存取，但需要多重要素驗證。

如果偵測到風險，使用者可以執行多重要素驗證來進行自我補救，並關閉有風險的登入事件，以避免系統管理員不必要的干擾。

> [!NOTE] 
> 使用者必須先註冊 Azure 多重要素驗證，才能觸發登入風險原則。

### <a name="custom-conditional-access-policy"></a>自訂條件式存取原則

系統管理員也可以選擇建立自訂的條件式存取原則，包括登入風險做為指派條件。 如需條件式存取的詳細資訊，請參閱[什麼是條件式存取？](../conditional-access/overview.md)

![自訂條件式存取登入風險原則](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>使用者風險原則

「身分識別保護」可以計算使用者行為正常的標準，並使用它來根據其風險來決定決策。 使用者風險是指身分識別已遭入侵的機率計算。 系統管理員可以根據此風險分數指示來做出決定，以強制執行組織需求。 系統管理員可以選擇封鎖存取、允許存取，或允許存取，但需要使用[Azure AD 自助式密碼重設](../authentication/howto-sspr-deployment.md)來變更密碼。

如果偵測到風險，使用者可以執行自助式密碼重設以自我補救並關閉使用者風險事件，以避免系統管理員不必要的干擾。

> [!NOTE] 
> 使用者必須先註冊自助式密碼重設，才能觸發使用者風險原則。

## <a name="next-steps"></a>後續步驟

- [啟用 Azure AD 自助式密碼重設](../authentication/howto-sspr-deployment.md)

- [啟用 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [啟用 Azure 多重要素驗證註冊原則](howto-identity-protection-configure-mfa-policy.md)

- [啟用登入和使用者風險原則](howto-identity-protection-configure-risk-policies.md)
