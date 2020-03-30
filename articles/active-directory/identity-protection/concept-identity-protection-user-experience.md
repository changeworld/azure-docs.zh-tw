---
title: 使用 Azure AD 標識保護的使用者體驗
description: Azure AD 標識保護的使用者體驗
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886996"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>使用 Azure AD 標識保護的使用者體驗

透過 Azure Active Directory Identity Protection，您可以：

* 要求使用者註冊 Azure 多重要素驗證 （MFA）
* 自動補救有風險登錄和受攻擊的使用者

所有身份保護原則都對使用者的登錄體驗有影響。 允許使用者註冊和使用 Azure MFA 和自助服務密碼重設等工具可以減輕影響。 這些工具以及適當的策略選擇為使用者提供了在需要時的自我修復選項。

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication 註冊

啟用需要多重要素驗證註冊並面向所有使用者的標識保護原則將確保他們將來能夠使用 Azure MFA 進行自我修復。 配置此策略可為使用者提供 14 天的時間，使用者可以選擇註冊，並在結束時強制註冊。 下面概述了使用者的體驗。 更多資訊可在文章"[雙因素驗證概述"和"工作或學校帳戶"](../user-help/user-help-two-step-verification-overview.md)中的最終使用者文檔中找到。

### <a name="registration-interrupt"></a>註冊中斷

1. 登錄任何 Azure AD 集成應用程式時，使用者會收到有關設置帳戶以進行多重要素驗證的通知。 對於具有新設備的新使用者的 Windows 10 開箱即用體驗，也會觸發此策略。
   
    ![需要更多資訊](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. 完成註冊 Azure 多重要素驗證的引導步驟並完成登錄。

## <a name="risky-sign-in-remediation"></a>風險登錄補救

當管理員為登錄風險配置策略時，受影響的使用者在嘗試登錄並觸發策略風險級別時將收到通知。 

### <a name="risky-sign-in-self-remediation"></a>風險登錄自我修復

1. 使用者獲知偵測到不尋常的登入，例如從新的位置、裝置或 app 登入。
   
    ![一些不尋常的提示](./media/concept-identity-protection-user-experience/120.png)

1. 使用者需要使用以前註冊的方法之一完成 Azure MFA 來證明其身份。 

### <a name="risky-sign-in-administrator-unblock"></a>風險登錄管理員取消阻止

管理員可以選擇在登錄時阻止使用者，具體取決於其風險級別。 要解除阻止，最終使用者必須聯繫其 IT 人員，或者他們可以嘗試從熟悉的位置或設備登錄。 在這種情況下，通過執行多重要素驗證進行自我修復不是一個選項。

![受登錄風險策略阻止](./media/concept-identity-protection-user-experience/200.png)

IT 人員可以按照["取消阻止使用者"](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk)部分中的說明操作，以允許使用者重新登錄。

## <a name="risky-user-remediation"></a>風險使用者補救

配置使用者風險策略後，滿足使用者風險級別危害概率的使用者必須經過使用者危害恢復流，然後才能登錄。 

### <a name="risky-user-self-remediation"></a>風險使用者自我修復

1. 使用者獲知其帳戶安全性因為可疑活動或認證外洩而有風險。
   
    ![補救](./media/concept-identity-protection-user-experience/101.png)

1. 使用者需要使用以前註冊的方法之一完成 Azure MFA 來證明其身份。 
1. 最後，使用者被迫使用自助服務密碼重設更改其密碼，因為其他人可能有權訪問其帳戶。

## <a name="risky-sign-in-administrator-unblock"></a>風險登錄管理員取消阻止

管理員可以選擇在登錄時阻止使用者，具體取決於其風險級別。 要解除阻止，最終使用者必須聯繫其 IT 人員。 在這種情況下，通過執行多重要素驗證和自助服務密碼重設進行自我修復不是一個選項。

![被使用者風險策略阻止](./media/concept-identity-protection-user-experience/104.png)

IT 人員可以按照["取消阻止使用者"](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk)部分中的說明操作，以允許使用者重新登錄。

## <a name="see-also"></a>另請參閱

- [修復風險並取消阻止使用者](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 
