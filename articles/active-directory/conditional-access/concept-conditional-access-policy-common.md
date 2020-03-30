---
title: 常見條件訪問策略 - Azure 活動目錄
description: 組織常用的條件訪問策略
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
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295296"
---
# <a name="common-conditional-access-policies"></a>一般條件式存取原則

[安全預設值](../fundamentals/concept-fundamentals-security-defaults.md)對一些人來說很棒，但許多組織需要比它們提供的靈活性。 例如，許多人需要能夠將特定帳戶（如其緊急訪問或破玻璃管理帳戶）從需要多重要素驗證的條件訪問策略中排除。 對於這些組織，本文中引用的通用策略可能使用。

![Azure 門戶中的條件訪問策略](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>緊急存取帳戶

有關緊急存取帳戶及其重要性的詳細資訊，請參閱以下文章： 

* [在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 來建立具彈性的存取控制管理策略](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>組織部署的典型策略

* [阻止舊版身份驗證](howto-conditional-access-policy-block-legacy.md)\*
* [管理員需要 MFA](howto-conditional-access-policy-admin-mfa.md)\*
* [需要 MFA 進行 Azure 管理](howto-conditional-access-policy-azure-management.md)\*
* [要求所有使用者使用 MFA](howto-conditional-access-policy-all-users-mfa.md)\*

\*這四個策略在一起配置時，將模仿[安全預設值啟用的功能](../fundamentals/concept-fundamentals-security-defaults.md)。

## <a name="additional-policies"></a>其他策略

* [以風險為依據的條件式存取 (需要 Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [需要受信任的位置進行 MFA 註冊](howto-conditional-access-policy-registration.md)
* [依位置封鎖存取](howto-conditional-access-policy-location.md)
* [需要符合規範裝置](howto-conditional-access-policy-compliant-device.md)
* [阻止訪問，但特定應用除外](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>後續步驟

- [使用"條件訪問"工具類比登錄行為。](troubleshoot-conditional-access-what-if.md)

- [對條件訪問使用僅報告模式來確定新策略決策的影響。](concept-conditional-access-report-only.md)
