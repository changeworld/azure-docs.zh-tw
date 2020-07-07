---
title: Azure AD Identity Protection 的使用者體驗
description: Azure AD Identity Protection 的使用者體驗
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
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "72886996"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Azure AD Identity Protection 的使用者體驗

透過 Azure Active Directory Identity Protection，您可以：

* 要求使用者註冊 Azure 多重要素驗證（MFA）
* 自動補救有風險的登入和遭到入侵的使用者

所有的身分識別保護原則都會影響使用者的登入體驗。 允許使用者註冊並使用 Azure MFA 和自助式密碼重設之類的工具，可以減少影響。 這些工具以及適當的原則選擇，會在使用者需要時提供自我補救選項。

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication 註冊

啟用需要多重要素驗證註冊並以所有使用者為目標的身分識別保護原則，將會確保他們能夠在未來使用 Azure MFA 進行自我補救。 設定此原則可讓您的使用者在14天的期間內選擇註冊，並在結束時強制註冊。 使用者的體驗如下所述。 如需詳細資訊，請參閱這篇文章中的使用者檔集：[雙因素驗證和您的公司或學校帳戶的總覽](../user-help/user-help-two-step-verification-overview.md)。

### <a name="registration-interrupt"></a>註冊中斷

1. 在登入任何 Azure AD 整合的應用程式時，使用者會收到有關設定帳戶以進行多重要素驗證之需求的通知。 這項原則也會在具有新裝置的新使用者的 Windows 10 全新體驗中觸發。
   
    ![需要更多資訊](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. 完成引導式步驟來註冊 Azure 多重要素驗證，並完成您的登入。

## <a name="risky-sign-in-remediation"></a>有風險的登入修復

當系統管理員設定登入風險的原則時，受影響的使用者會在嘗試登入並觸發原則風險層級時收到通知。 

### <a name="risky-sign-in-self-remediation"></a>有風險的登入自我補救

1. 使用者獲知偵測到不尋常的登入，例如從新的位置、裝置或 app 登入。
   
    ![出現異常的提示](./media/concept-identity-protection-user-experience/120.png)

1. 使用者必須使用其先前註冊的其中一個方法來完成 Azure MFA，以證明其身分識別。 

### <a name="risky-sign-in-administrator-unblock"></a>有風險的登入系統管理員解除封鎖

系統管理員可以選擇在登入時封鎖使用者，視其風險層級而定。 若要解除封鎖，終端使用者必須聯絡其 IT 人員，或嘗試從熟悉的位置或裝置登入。 在此情況下，執行多重要素驗證並不是一個選項。

![由登入風險原則封鎖](./media/concept-identity-protection-user-experience/200.png)

IT 人員可以遵循[解除封鎖使用者](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk)，讓使用者重新登入一節中的指示。

## <a name="risky-user-remediation"></a>有風險的使用者補救

設定使用者風險原則之後，符合使用者風險層級的使用者必須先經過使用者入侵復原流程，才可以登入。 

### <a name="risky-user-self-remediation"></a>有風險的使用者自我修復

1. 使用者獲知其帳戶安全性因為可疑活動或認證外洩而有風險。
   
    ![修復](./media/concept-identity-protection-user-experience/101.png)

1. 使用者必須使用其先前註冊的其中一個方法來完成 Azure MFA，以證明其身分識別。 
1. 最後，使用者會被迫使用自助式密碼重設來變更其密碼，因為其他人可能有其帳戶的存取權。

## <a name="risky-sign-in-administrator-unblock"></a>有風險的登入系統管理員解除封鎖

系統管理員可以選擇在登入時封鎖使用者，視其風險層級而定。 若要解除封鎖，終端使用者必須聯絡其 IT 人員。 在這種情況下，執行多重要素驗證和自助式密碼重設不是一個選項，因此可自行補救。

![由使用者風險原則封鎖](./media/concept-identity-protection-user-experience/104.png)

IT 人員可以遵循[解除封鎖使用者](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk)，讓使用者重新登入一節中的指示。

## <a name="see-also"></a>另請參閱

- [補救風險並解除封鎖使用者](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 
