---
title: Azure AD Identity Protection 原則
description: 識別使用 Identity Protection 啟用的三個原則
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7587ed6d414a69cff67aca9446aebf6260c99fcd
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83736495"
---
# <a name="identity-protection-policies"></a>身分識別保護原則

Azure Active Directory Identity Protection 包含三個可由管理員選擇啟用的預設原則。 這些原則的自訂能力有限，但適用於大部分的組織。 所有原則都允許排除使用者，例如您的[緊急存取或急用管理員帳戶](../users-groups-roles/directory-emergency-access.md)。

![身分識別保護原則](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA 註冊原則

Identity Protection 可協助組織使用在登入時需要註冊的條件式存取原則，來推出 Azure Multi-Factor Authentication (MFA)。 啟用此原則，是確保組織中的新使用者在一開始就註冊 MFA 的絕佳方式。 多重要素驗證是 Identity Protection 內的風險事件適用的自我補救方法之一。 自我補救可讓您的使用者自行採取因應措施，以減少服務台的通話量。

如需 Azure Multi-Factor Authentication 的詳細資訊，請參閱[運作方式：Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)。

## <a name="sign-in-risk-policy"></a>登入風險原則

Identity Protection 會分析每一次登入的訊號 (包括即時和離線)，並根據登入並非由使用者執行的機率，來計算風險分數。 管理員可根據此風險分數訊號做出決定，以強制執行組織需求。 管理員可以選擇封鎖存取、允許存取，或允許存取、但需要多重要素驗證。

如果偵測到風險，使用者可執行多重要素驗證以進行自我補救，並關閉有風險的登入事件，以避免對管理員造成不必要的干擾。

> [!NOTE] 
> 使用者必須先註冊 Azure Multi-Factor Authentication，才能觸發登入風險原則。

### <a name="custom-conditional-access-policy"></a>自訂條件式存取原則

管理員也可以選擇建立自訂的條件式存取原則，將登入風險納入為指派條件。 如需如何在條件式存取原則中將風險納入為條件的詳細資訊，請參閱[條件式存取：條件](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)一文

![自訂條件式存取登入風險原則](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>使用者風險原則

Identity Protection 可以計算其認定為正常使用者行為的標準，並將其作為制定風險決策的基礎。 計算身分識別遭入侵的機率，即會得出使用者風險。 管理員可根據此風險分數訊號做出決定，以強制執行組織需求。 管理員可以選擇封鎖存取、允許存取，或允許存取、但需要使用 [Azure AD 自助式密碼重設](../authentication/howto-sspr-deployment.md)來變更密碼。

如果偵測到風險，使用者可以執行自助式密碼重設以進行自我補救，並關閉使用者風險事件，以避免對管理員造成不必要的干擾。

> [!NOTE] 
> 使用者必須先註冊自助式密碼重設，才能觸發使用者風險原則。

## <a name="next-steps"></a>後續步驟

- [啟用 Azure AD 自助式密碼重設](../authentication/howto-sspr-deployment.md)

- [啟用 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [啟用 Azure Multi-Factor Authentication 註冊原則](howto-identity-protection-configure-mfa-policy.md)

- [啟用登入與使用者風險原則](howto-identity-protection-configure-risk-policies.md)
