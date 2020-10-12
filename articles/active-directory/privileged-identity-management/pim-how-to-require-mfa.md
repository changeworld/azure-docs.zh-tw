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
ms.openlocfilehash: bf1113f7b2f396deed849fa46108537f290b53a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84742091"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>多重要素驗證和 Privileged Identity Management

建議您針對所有系統管理員都要求進行多重要素驗證 (MFA)。 這可降低密碼遭入侵所導致的攻擊風險。

您可以要求使用者在登入時完成多重要素驗證挑戰。 您也可以要求使用者在啟用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 中的角色時，完成多重要素驗證挑戰。 如此一來，如果使用者在登入時未完成多重要素驗證挑戰，Privileged Identity Management 將會提示他們這麼做。

> [!IMPORTANT]
> 現在，Azure Multi-Factor Authentication 僅適用于公司或學校帳戶，而不是 Microsoft 個人帳戶， (通常是用來登入 Microsoft 服務的個人帳戶，例如 Skype、Xbox 或 Outlook.com) 。 因此，使用個人帳戶的任何人都不能成為合格的系統管理員，因為他們無法使用多重要素驗證來啟用其角色。 如果這些使用者需要繼續使用 Microsoft 帳戶來管理工作負載，請立即將他們提升為永久系統管理員。

## <a name="how-pim-validates-mfa"></a>PIM 如何驗證 MFA

當使用者啟動角色時，有兩個選項可以驗證多重要素驗證。

最簡單的選項是依賴 Azure Multi-Factor Authentication，讓啟用特殊許可權角色的使用者使用。 若要這樣做，請先檢查這些使用者是否已獲得授權（如有必要），並已向 Azure Multi-Factor Authentication 註冊。 如需如何部署 Azure Multi-Factor Authentication 的詳細資訊，請參閱 [部署雲端式 azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)。 建議您設定 Azure AD，以在使用者登入時對這些使用者強制執行多重要素驗證。 這是因為多重要素驗證檢查將會 Privileged Identity Management 本身進行。

或者，如果使用者在內部部署驗證，您可以讓您的身分識別提供者負責進行多重要素驗證。 例如，如果您已在存取 Azure AD 之前將 Active Directory 同盟服務設定為需要以智慧卡為基礎的驗證， [使用 Azure Multi-Factor Authentication 與 AD FS 保護雲端資源](../authentication/howto-mfa-adfs.md) 包含用來設定 AD FS 來將宣告傳送至 Azure AD 的相關指示。 當使用者嘗試啟動角色時，Privileged Identity Management 將會在收到適當的宣告時，接受已針對使用者驗證過的多重要素驗證。

## <a name="next-steps"></a>接下來的步驟

- [在 Privileged Identity Management 中設定 Azure AD 角色設定](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
