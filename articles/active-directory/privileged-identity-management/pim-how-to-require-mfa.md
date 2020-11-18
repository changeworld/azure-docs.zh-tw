---
title: MFA 或2FA 和 Privileged Identity Management Azure AD |Microsoft Docs
description: 了解 Azure AD Privileged Identity Management (PIM) 如何驗證多重要素驗證 (MFA)。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94834994"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>多重要素驗證和 Privileged Identity Management

建議您針對所有系統管理員都要求進行多重要素驗證 (MFA)。 這可降低密碼遭入侵所導致的攻擊風險。

您可以要求使用者在登入時完成多重要素驗證挑戰。 您也可以要求使用者在啟用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 中的角色時，完成多重要素驗證挑戰。 如此一來，如果使用者在登入時未完成多重要素驗證挑戰，Privileged Identity Management 將會提示他們這麼做。

> [!IMPORTANT]
> 現在 Azure AD Multi-Factor Authentication 只適用于公司或學校帳戶，而不是 Microsoft 個人帳戶 (通常是用來登入 Microsoft 服務的個人帳戶，例如 Skype、Xbox 或 Outlook.com) 。 因此，使用個人帳戶的任何人都不能成為合格的系統管理員，因為他們無法使用多重要素驗證來啟用其角色。 如果這些使用者需要繼續使用 Microsoft 帳戶來管理工作負載，請立即將他們提升為永久系統管理員。

## <a name="how-pim-validates-mfa"></a>PIM 如何驗證 MFA

當使用者啟動角色時，有兩個選項可以驗證多重要素驗證。

最簡單的選項是依賴啟用特殊許可權角色之使用者的 Azure AD Multi-Factor Authentication。 若要這樣做，請先檢查這些使用者是否已獲得授權（如有必要），並已註冊 Azure AD Multi-Factor Authentication。 如需如何部署 Azure AD Multi-Factor Authentication 的詳細資訊，請參閱 [部署雲端式 Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)。 建議您設定 Azure AD，以在使用者登入時對這些使用者強制執行多重要素驗證。 這是因為多重要素驗證檢查將會 Privileged Identity Management 本身進行。

或者，如果使用者在內部部署驗證，您可以讓您的身分識別提供者負責進行多重要素驗證。 例如，如果您已設定 AD Federation Services 在存取 Azure AD 之前需要以智慧卡為基礎的驗證，請 [使用 Azure AD Multi-Factor Authentication 來保護雲端資源，並 AD FS](../authentication/howto-mfa-adfs.md) 包含設定 AD FS 將宣告傳送至 Azure AD 的指示。 當使用者嘗試啟動角色時，Privileged Identity Management 將會在收到適當的宣告時，接受已針對使用者驗證過的多重要素驗證。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定 Azure AD 角色設定](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
