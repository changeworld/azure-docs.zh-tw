---
title: 一般條件式存取原則-Azure Active Directory
description: 組織常使用的條件式存取原則
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdaaed72b85bc3c0196a8897be311efa5fb714e2
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366338"
---
# <a name="common-conditional-access-policies"></a>一般條件式存取原則

[安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md) 適用于某些組織，但許多組織所需的彈性比它們所提供的更大。 例如，許多組織都需要能夠從需要多重要素驗證的條件式存取原則中排除特定帳戶，例如其緊急存取或半透明的系統管理帳戶。 針對這些組織，本文中參考的一般原則可以使用。

![Azure 入口網站中的條件式存取原則](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>緊急存取帳戶

如需緊急存取帳戶的詳細資訊，以及其重要性，請參閱下列文章： 

* [在 Azure AD 中管理緊急存取帳戶](../roles/security-emergency-access.md)
* [使用 Azure Active Directory 來建立具彈性的存取控制管理策略](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>組織部署的一般原則

* [封鎖舊版驗證](howto-conditional-access-policy-block-legacy.md)\*
* [需要系統管理員的 MFA](howto-conditional-access-policy-admin-mfa.md)\*
* [Azure 管理需要 MFA](howto-conditional-access-policy-azure-management.md)\*
* [所有使用者都需要 MFA](howto-conditional-access-policy-all-users-mfa.md)\*

\* 這四個原則會在一起設定時模擬 [安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)所啟用的功能。

## <a name="additional-policies"></a>其他原則

* [以風險為基礎的條件式存取 (需要 Azure AD Premium P2) ](howto-conditional-access-policy-risk.md)
* [以使用者風險為基礎的條件式存取 (需要 Azure AD Premium P2) ](howto-conditional-access-policy-risk-user.md)
* [需要受信任的位置進行 MFA 註冊](howto-conditional-access-policy-registration.md)
* [依位置封鎖存取](howto-conditional-access-policy-location.md)
* [需要符合規範裝置](howto-conditional-access-policy-compliant-device.md)
* [封鎖特定應用程式以外的存取](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>後續步驟

- [使用條件式存取 What If 工具模擬登入行為。](troubleshoot-conditional-access-what-if.md)

- [使用僅限報表模式來進行條件式存取，以判斷新原則決策的影響。](concept-conditional-access-report-only.md)
