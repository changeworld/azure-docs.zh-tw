---
title: 適用於貴組織的 Azure Multi-Factor Authentication - Azure Active Directory
description: 根據您的授權模型，瞭解貴組織可用的 Azure Multi-Factor Authentication 功能
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13bb7782f6396466200fbb3e2df77158bb75d8a4
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705396"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>適用於貴組織的 Azure Multi-Factor Authentication

依據貴組織所擁有的授權，有多種方式可以為 Azure Active Directory (AD) 使用者啟用 Azure Multi-Factor Authentication。 

![調查訊號並視需要強制執行 MFA](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

根據我們的研究顯示，如果使用多重要素驗證 (MFA)，則帳戶遭到入侵的機率可降低 99.9%。

那麼，要如何在您的組織乘為受害者之前，先啟用 MFA (甚至可以免費)？

## <a name="free-option"></a>免費選項

享有 Azure AD 免費權益的客戶可以使用[安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)，在環境中啟用多重要素驗證。

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 商務版、E3 或 E5 版

對於具有 Microsoft 365 的客戶，有兩個選項：

* 所有使用者都可以在所有登入事件中，啟用或停用 Azure Multi-Factor Authentication。 無法只啟用某個使用者子集的多重要素驗證，或只在特定情境下啟用。 管理作業是透過 Office 365 入口網站處理。 
* 若想要改善使用者體驗，請升級至 Azure AD Premium P1 或 P2，並使用條件式存取。 如需詳細資訊，請參閱使用多重要素驗證保護 Microsoft 365 資源。

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

對於具有 Azure AD Premium P1 或包含此功能的類似授權 (如 Enterprise Mobility + Security E3、Microsoft 365 F1 或 Microsoft 365 E3 等) 的客戶： 

使用 [Azure AD 條件式存取](../authentication/tutorial-enable-azure-mfa.md)，根據您的業務需求，在特定情境或事件發生時提示使用者進行多重要素驗證。

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

對於具有 Azure AD Premium P2 或包含此功能的類似授權 (如 Enterprise Mobility + Security E5 或 Microsoft 365 E5) 的客戶： 

提供最強的安全性位置和改善使用者體驗。 將[依據風險的條件式存取](../conditional-access/howto-conditional-access-policy-risk.md)新增至會配合使用者模式而調整的 Azure AD Premium P1 功能，並盡可能減少多重要素驗證提示。

## <a name="authentication-methods"></a>驗證方法

| 方法 | 安全性預設值 | 所有其他方法 |
| --- | --- | --- |
| 行動應用程式的通知 | X | X |
| 來自行動應用程式或硬體 Token 的驗證碼 |   | X |
| 電話簡訊 |   | X |
| 電話通話 |   | X |

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱[使用 Azure Multi-Factor Authentication 來保護使用者登入事件](../authentication/tutorial-enable-azure-mfa.md)教學課程。

如需有關授權的詳細資訊，請參閱 [Azure Multi-Factor Authentication 的功能和授權](../authentication/concept-mfa-licensing.md)。
