---
title: 組織的 Azure 多重要素驗證 - Azure 活動目錄
description: 根據許可證模型瞭解組織 Azure 多重要素驗證的可用功能
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530388"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>組織 Azure 多重要素驗證概述

有多種方法可以根據組織擁有的許可證為 Azure 活動目錄 （AD） 使用者啟用 Azure 多重要素驗證。 

![調查信號並在必要時強制實施 MFA](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

根據我們的研究，如果您使用多重要素驗證 （MFA），您的帳戶被入侵的可能性要降低 99.9%。

那麼，在成為統計資料之前，您的組織如何免費打開 MFA？

## <a name="free-option"></a>免費選項

利用 Azure AD 免費優勢的客戶可以使用[安全預設值](../fundamentals/concept-fundamentals-security-defaults.md)在其環境中啟用多重要素驗證。

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 業務高級版、E3 或 E5

對於具有 Office 365 的客戶，有兩個選項：

* 對於所有使用者，對於所有登錄事件，Azure 多重要素驗證已啟用或禁用。 無法僅為使用者子集啟用多重要素驗證，或僅在某些情況下啟用多重要素驗證。 管理通過 Office 365 門戶。 
* 要改進使用者體驗，請使用 Azure AD 高級 P1 或 P2 並使用條件訪問。 有關詳細資訊，請參閱使用多重要素驗證的安全 Office 365 資源。

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

適用于具有 Azure AD 高級 P1 或類似許可證的客戶，包括此功能，如企業移動性 + 安全 E3、Microsoft 365 F1 或 Microsoft 365 E3： 

使用[Azure AD 條件訪問](../conditional-access/overview.md)提示使用者在某些方案或事件期間進行多重要素驗證，以滿足您的業務需求。

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

適用于具有 Azure AD 高級 P2 或類似許可證的客戶，這些許可證包含此功能，如企業移動性 + 安全 E5 或 Microsoft 365 E5： 

提供最強的安全位置和改進的使用者體驗。 添加[基於風險的條件訪問](../conditional-access/howto-conditional-access-policy-risk.md)Azure AD 高級 P1 功能，這些功能可適應使用者的模式並最大限度地減少多重要素驗證提示。

## <a name="authentication-methods"></a>驗證方法

|   | 安全性預設值 | 所有其他方法 |
| --- | --- | --- |
| 行動應用程式的通知 | X | X |
| 來自行動應用程式或硬體 Token 的驗證碼 |   | X |
| 電話簡訊 |   | X |
| 電話通話 |   | X |

## <a name="next-steps"></a>後續步驟

要開始，請參閱使用 Azure[多重要素驗證保護使用者登錄事件的](../authentication/tutorial-enable-azure-mfa.md)教程。

有關許可的詳細資訊，請參閱[Azure 多重要素驗證的功能和許可證](../authentication/concept-mfa-licensing.md)。
