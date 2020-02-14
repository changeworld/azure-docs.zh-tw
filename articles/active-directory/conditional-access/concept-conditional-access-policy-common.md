---
title: 一般條件式存取原則-Azure Active Directory
description: 組織常用的條件式存取原則
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfb5fb17abd5a433c177d3efc5a4f0a2cec3d4d7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186130"
---
# <a name="common-conditional-access-policies"></a>一般條件式存取原則

[安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)非常適合某些組織，但許多組織所需的彈性比所提供的還要多。 例如，許多人都必須能夠從需要多重要素驗證的條件式存取原則中排除特定帳戶，例如其緊急存取或中斷玻璃管理帳戶。 針對這些組織，本文中所參考的一般原則可以使用。

![Azure 入口網站中的條件式存取原則](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>緊急存取帳戶

如需緊急存取帳戶和其重要性的詳細資訊，請參閱下列文章： 

* [在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 建立彈性的存取控制管理原則](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>組織部署的一般原則

* 系統[管理員需要 MFA](howto-conditional-access-policy-admin-mfa.md)\*
* [Azure 管理\* 需要 MFA](howto-conditional-access-policy-azure-management.md)
* [所有使用者都需要 MFA](howto-conditional-access-policy-all-users-mfa.md)\*
* [封鎖舊版驗證](howto-conditional-access-policy-block-legacy.md)\*
* [以風險為基礎的條件式存取（需要 Azure AD Premium P2）](howto-conditional-access-policy-risk.md)
* [MFA 註冊需要信任的位置](howto-conditional-access-policy-registration.md)
* [依位置封鎖存取](howto-conditional-access-policy-location.md)
* [需要符合規範的裝置](howto-conditional-access-policy-compliant-device.md)

\* 這四個原則一起設定時，會模擬[安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)所啟用的功能。

## <a name="next-steps"></a>後續步驟

- [使用條件式存取 What If 工具模擬登入行為。](troubleshoot-conditional-access-what-if.md)
- [使用僅限報告模式來進行條件式存取，以判斷新原則決策的影響。](concept-conditional-access-report-only.md)
