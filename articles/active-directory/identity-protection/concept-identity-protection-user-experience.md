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
ms.openlocfilehash: 7d3315a8d6bca9406461d0082ed85bbefc9459f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88950199"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Azure AD Identity Protection 的使用者體驗

透過 Azure Active Directory Identity Protection，您可以：

* 要求使用者註冊 Azure Multi-Factor Authentication (MFA) 
* 自動補救具風險的登入和遭盜用的使用者

所有身分識別保護原則都會影響使用者的登入體驗。 允許使用者註冊及使用 Azure MFA 和自助式密碼重設等工具可以降低影響。 這些工具以及適當的原則選項，可讓使用者在需要時提供自行修復的選項。

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication 註冊

啟用需要多重要素驗證註冊並將所有使用者設為目標的身分識別保護原則，將可確保他們能夠使用 Azure MFA 在未來進行自我修復。 設定此原則可讓您的使用者有14天的時間，在這段期間內，他們可以選擇註冊，並在結束時強制註冊。 使用者體驗如下所述。 如需詳細資訊，請參閱檔中的終端使用者檔、 [雙因素驗證和您的公司或學校帳戶的總覽](../user-help/multi-factor-authentication-end-user-first-time.md)。

### <a name="registration-interrupt"></a>註冊中斷

1. 登入任何 Azure AD 整合的應用程式時，使用者會收到關於設定帳戶以進行多重要素驗證的需求通知。 這項原則也會在新使用者的全新使用者體驗中，以新的裝置觸發 Windows 10 的體驗。
   
    ![需要更多資訊](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. 完成註冊 Azure Multi-Factor Authentication 的引導式步驟，並完成您的登入。

## <a name="risky-sign-in-remediation"></a>有風險的登入補救

當系統管理員已設定登入風險的原則時，受影響的使用者會在嘗試登入並觸發原則風險層級時收到通知。 

### <a name="risky-sign-in-self-remediation"></a>有風險的登入自我修復

1. 使用者獲知偵測到不尋常的登入，例如從新的位置、裝置或 app 登入。
   
    ![不尋常的提示](./media/concept-identity-protection-user-experience/120.png)

1. 使用者必須透過其先前註冊的其中一個方法來完成 Azure MFA，以證明其身分識別。 

### <a name="risky-sign-in-administrator-unblock"></a>有風險的登入系統管理員解除封鎖

系統管理員可以選擇根據使用者的風險層級，在登入時封鎖使用者。 若要解除封鎖，終端使用者必須聯絡他們的 IT 人員，或嘗試從熟悉的位置或裝置登入。 在此情況下，執行多重要素驗證不是一個選項。

![遭到登入風險原則封鎖](./media/concept-identity-protection-user-experience/200.png)

IT 人員可以依照 [ [解除封鎖使用者](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) ] 一節中的指示，讓使用者重新登入。

## <a name="risky-user-remediation"></a>具風險的使用者補救

設定使用者風險原則之後，符合使用者風險層級入侵的使用者必須經過使用者入侵復原流程才能登入。 

### <a name="risky-user-self-remediation"></a>具風險的使用者自我補救

1. 使用者獲知其帳戶安全性因為可疑活動或認證外洩而有風險。
   
    ![修復](./media/concept-identity-protection-user-experience/101.png)

1. 使用者必須透過其先前註冊的其中一個方法來完成 Azure MFA，以證明其身分識別。 
1. 最後，會強制使用者使用自助式密碼重設來變更其密碼，因為其他人可能會擁有其帳戶的存取權。

## <a name="risky-sign-in-administrator-unblock"></a>有風險的登入系統管理員解除封鎖

系統管理員可以選擇根據使用者的風險層級，在登入時封鎖使用者。 若要解除封鎖，終端使用者必須聯絡他們的 IT 人員。 在此情況下，執行多重要素驗證和自助式密碼重設並不是一個選項，就可以自行修復。

![封鎖使用者風險原則](./media/concept-identity-protection-user-experience/104.png)

IT 人員可以依照 [ [解除封鎖使用者](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) ] 一節中的指示，讓使用者重新登入。

## <a name="see-also"></a>另請參閱

- [補救風險並解除封鎖使用者](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)