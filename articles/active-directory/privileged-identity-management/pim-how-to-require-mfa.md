---
title: MFA 或 2FA 和特權標識管理 - Azure AD |微軟文檔
description: 了解 Azure AD Privileged Identity Management (PIM) 如何驗證多重要素驗證 (MFA)。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6df593909c3ae5962e413eb84e64196fade0326b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022144"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>多重要素驗證和特權標識管理

建議您針對所有系統管理員都要求進行多重要素驗證 (MFA)。 這可降低密碼遭入侵所導致的攻擊風險。

您可以要求使用者在登錄時完成多重要素驗證質詢。 您還可以要求使用者在 Azure 活動目錄 （Azure AD） 特權標識管理 （PIM） 中啟動角色時完成多重要素驗證質詢。 這樣，如果使用者在登錄時未完成多重要素驗證質詢，則特權標識管理將提示使用者這樣做。

> [!IMPORTANT]
> 目前，Azure 多重要素驗證僅適用于工作帳戶或學校帳戶，而不適用於 Microsoft 個人帳戶（通常是用於登錄到 Microsoft 服務（如 Skype、Xbox 或 Outlook.com）的個人帳戶）。 因此，使用個人帳戶的任何人都不能成為合格的管理員，因為他們無法使用多重要素驗證來啟動其角色。 如果這些使用者需要繼續使用 Microsoft 帳戶來管理工作負載，請立即將他們提升為永久系統管理員。

## <a name="how-pim-validates-mfa"></a>PIM 如何驗證 MFA

當使用者啟動角色時，有兩個選項可用於驗證多重要素驗證。

最簡單的選項是為啟動特權角色的使用者依賴于 Azure 多重要素驗證。 為此，請首先檢查這些使用者是否獲得許可（如有必要）是否註冊了 Azure 多重要素驗證。 有關如何部署 Azure 多重要素驗證的詳細資訊，請參閱[部署基於雲的 Azure 多重要素驗證](../authentication/howto-mfa-getstarted.md)。 建議（但不是必需的）將 Azure AD 配置為在這些使用者登錄時強制實施多重要素驗證。 這是因為多重要素驗證檢查將由特權標識管理本身進行。

或者，如果使用者在本地進行身份驗證，則可以讓標識提供程式負責多重要素驗證。 例如，如果您已在存取 Azure AD 之前將 Active Directory 同盟服務設定為需要以智慧卡為基礎的驗證， [使用 Azure Multi-Factor Authentication 與 AD FS 保護雲端資源](../authentication/howto-mfa-adfs.md) 包含用來設定 AD FS 來將宣告傳送至 Azure AD 的相關指示。 當使用者嘗試啟動角色時，特權標識管理將接受在使用者收到適當的聲明後已針對該使用者驗證多重要素驗證。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中配置 Azure AD 角色設置](pim-how-to-change-default-settings.md)
- [在特權標識管理中配置 Azure 資源角色設置](pim-resource-roles-configure-role-settings.md)
