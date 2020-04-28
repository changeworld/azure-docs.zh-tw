---
title: 適用于您組織的 Azure 多重要素驗證-Azure Active Directory
description: 根據您的授權模型，瞭解組織的 Azure 多因素驗證可用功能
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
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530388"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>瞭解您組織的 Azure 多因素驗證

有多種方式可以根據貴組織所擁有的授權，為您的 Azure Active Directory （AD）使用者啟用 Azure 多重要素驗證。 

![調查信號並視需要強制執行 MFA](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

根據我們的研究，如果您使用多重要素驗證（MFA），則您的帳戶會比99.9% 更不可能遭到入侵。

那麼，您的組織如何在成為統計資料之前，甚至免費開啟 MFA？

## <a name="free-option"></a>免費選項

利用 Azure AD 免費權益的客戶可以使用[安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)，在其環境中啟用多重要素驗證。

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Business Premium、E3 或 E5

針對具有 Office 365 的客戶，有兩個選項：

* 所有使用者都可以在所有登入事件中，啟用或停用 Azure 多重要素驗證。 只有在某些情況下，才能夠啟用多重要素驗證，或僅限於特定案例。 管理是透過 Office 365 入口網站。 
* 如需改善的使用者體驗，請升級至 Azure AD Premium P1 或 P2，並使用條件式存取。 如需詳細資訊，請參閱使用多重要素驗證保護 Office 365 資源。

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

適用于具有 Azure AD Premium P1 或類似授權的客戶，其中包括 Enterprise Mobility + Security E3、Microsoft 365 F1 或 Microsoft 365 E3 等這類功能： 

使用[Azure AD 條件式存取](../conditional-access/overview.md)，在特定案例或事件中提示使用者進行多重要素驗證，以符合您的商務需求。

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

適用于具有 Azure AD Premium P2 或類似授權的客戶，其中包含此功能，例如 Enterprise Mobility + Security E5 或 Microsoft 365 E5： 

提供最強的安全性位置和更好的使用者體驗。 新增以[風險為基礎的條件式存取](../conditional-access/howto-conditional-access-policy-risk.md)Azure AD Premium P1 功能，以適應使用者的模式，並將多重要素驗證提示降至最低。

## <a name="authentication-methods"></a>驗證方法

|   | 安全性預設值 | 所有其他方法 |
| --- | --- | --- |
| 行動應用程式的通知 | X | X |
| 來自行動應用程式或硬體 Token 的驗證碼 |   | X |
| 電話簡訊 |   | X |
| 電話通話 |   | X |

## <a name="next-steps"></a>後續步驟

若要開始，請參閱[使用 Azure 多重要素驗證來保護使用者登入事件](../authentication/tutorial-enable-azure-mfa.md)的教學課程。

如需授權的詳細資訊，請參閱[Azure 多重要素驗證的功能與](../authentication/concept-mfa-licensing.md)授權。
