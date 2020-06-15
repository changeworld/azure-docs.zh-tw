---
title: Azure Multi-Factor Authentication 版本和取用方案
description: 了解 Azure Multi-Factor Authentication 用戶端、不同的方法及可用的版本。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 165b84ee6b124d3f6a04c8db177ef17e32784ff9
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83757410"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 的功能與授權

為了保護組織中的使用者帳戶，建議您使用多重要素驗證。 這項功能對於資源具有特殊存取權限的帳戶特別重要。 因此，我們為 Office 365 和 Azure Active Directory (Azure AD) 管理員免費提供基本多重要素驗證功能。 如果您想要升級管理員功能，或擴充多重要素驗證到其他的使用者，您可以透過數種方式購買 Azure Multi-Factor Authentication。

> [!IMPORTANT]
> 本文詳細說明可授權和使用 Azure Multi-Factor Authentication 的不同方式。 如需定價和計費的特定詳細資訊，請參閱 [Azure Multi-Factor Authentication 定價頁面](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure Multi-Factor Authentication 版本

根據您組織的需求，可以有不同的方式來使用和授權 Azure Multi-Factor Authentication。 您可能已經有權使用 Azure Multi-Factor Authentication，視您目前擁有的 Azure AD、Office 365、EMS 或 Microsoft 365 授權而定。 下表詳細說明取得 Azure Multi-Factor Authentication 的不同方式，以及各項功能和使用案例。

| 如果您使用 | 功能和使用案例 |
| --- | --- |
| EMS 或 Microsoft 365 E3 和 E5 | EMS E3 或 Microsoft 365 E3 (包含 EMS 和 Office 365)，包括 Azure AD Premium P1。 EMS E5 或 Microsoft 365 E5 包含 Azure AD Premium P2。 您可以使用下列各節中所述的相同條件式存取功能，為使用者提供多重要素驗證。 |
| Azure AD Premium P1 | 您可以使用 [Azure AD 條件式存取](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)，在特定案例或事件中提示使用者進行多重要素驗證，以符合您的商務需求。 |
| Azure AD Premium P2 | 提供最強的安全性位置和改善使用者體驗。 將[依據風險的條件式存取](../conditional-access/howto-conditional-access-policy-risk.md)新增至會配合使用者模式而調整的 Azure AD Premium P1 功能，並盡可能減少多重要素驗證提示。 |
| Office 365 商務進階版、E3 或 E5 | 您可以為[每個使用者啟用](howto-mfa-userstates.md) Azure Multi-Factor Authentication，或使用安全性預設值為所有使用者針對所有登入事件啟用或停用 Azure Multi-Factor Authentication。 Azure Multi-Factor Authentication 的管理是透過 Office 365 入口網站加以管理。 若想要改善使用者體驗，請升級至 Azure AD Premium P1 或 P2，並使用條件式存取。 如需詳細資訊，請參閱[使用多重因素驗證保護 Office 365 資源](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)。 |
| Azure AD Free | 每次提出驗證要求時，您可以使用[安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)為所有使用者啟用多重要素驗證。 您無法精確地控制已啟用的使用者或情節，但是它確實提供了額外的安全性步驟。<br /> 即使未使用安全性預設值來為每個人啟用多重要素驗證，獲指派 *Azure AD 全域管理員*角色的使用者還是可以設定為使用多重要素驗證。 免費層的這項功能可確保重要的管理員帳戶受到多重要素驗證的保護。 |

## <a name="feature-comparison-of-versions"></a>版本的功能比較

下表提供 Azure Multi-Factor Authentication 各版本中可用的功能清單。 規劃保護使用者驗證的需求，然後判斷哪一種方法符合這些需求。 例如，雖然 Azure AD Free 提供的安全性預設值可提供 Azure Multi-Factor Authentication，但只有行動驗證器應用程式可用於驗證提示，而非電話或文字簡訊。 如果您無法確保行動驗證應用程式是安裝在使用者的個人裝置上，這種方法可能會是一項限制。

| 功能 | Azure AD Free - 安全性預設值 | Azure AD Free - Azure AD 全域管理員 | Office 365 商務進階版、E3 或 E5 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| 使用 MFA 保護 Azure AD 租用戶管理帳戶 | ● | ● (僅限 *Azure AD 全域管理員*帳戶) | ● | ● |
| 以行動應用程式做為第二個因素 | ● | ● | ● | ● |
| 以撥打電話做為第二個因素 | | ● | ● | ● |
| 以 SMS 做為第二個因素 | | ● | ● | ● |
| 系統管理員控制驗證方法 | | ● | ● | ● |
| 詐騙警示 | | | | ● |
| MFA 報告 | | | | ● |
| 通話的自訂問候語 | | | | ● |
| 自訂的通話來電者 ID | | | | ● |
| 信任的 IP | | | | ● |
| 記住受信任裝置的 MFA | | ● | ● | ● |
| 內部部署應用程式的 MFA | | | | ● |

> [!IMPORTANT]
> 自 2019 年 3 月起，通話選項不再提供給 Azure AD Free/試用版租用者中的 Azure Multi-Factor Authentication 和 Azure Self-Service Password Reset 使用者使用。 手機簡訊不會受到這項變更的影響。 通話功能可繼續供 Azure AD Premium P1 或 P2 租用戶的使用者，或 Office 365 Business Premium、E3 或 E5 的使用者使用。

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>購買與啟用 Azure Multi-Factor Authentication

若要使用 Azure Multi-Factor Authentication，請註冊或購買合格的 Azure AD 階層。 Azure AD 有四種版本 — Free、Office 365 應用程式版本 (適用於 Office 365 商務進階版 E3 或 E5 客戶)、Premium P1 和 Premium P2。

Free 版本隨附於 Azure 訂閱。 請參閱[下節](#azure-ad-free-tier)，以取得如何使用安全性預設值或透過 *Azure AD 全域管理員*角色保護帳戶的資訊。

Azure AD Premium 版本可透過您的 Microsoft 代表、[Open Volume 授權方案](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)及[雲端解決方案提供者方案](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)取得。 Azure 和 Office 365 訂閱者也可以線上購買 Azure Active Directory Premium P1 和 P2。 [登入](https://portal.office.com/Commerce/Catalog.aspx)以購買。

> [!IMPORTANT]
> 自 2018 年 9 月 1 日起，以耗用量為基礎的授權不再供新客戶使用。 使用以耗用量為基礎模型的現有客戶可以繼續使用每個已啟用的使用者或每個驗證帳單。

購買必要的 Azure AD 階層之後，請[規劃及部署 Azure Multi-Factor Authentication](howto-mfa-getstarted.md)。

### <a name="azure-ad-free-tier"></a>Azure AD Free 階層

Azure AD Free 租用戶中的所有使用者都可以使用安全性預設值來使用 Azure Multi-Factor Authentication。 每次使用者登入時，這些安全性預設值會為所有使用者啟用 Azure Multi-Factor Authentication。 使用 Azure AD Free 安全性預設值時，行動驗證應用程式是可用於 Azure Multi-Factor Authentication 的唯一方法。

* [深入了解 Azure AD 安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)
* [為 Azure AD Free 中的使用者啟用安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

如果您不想要為所有使用者和每個登入事件啟用 Azure Multi-Factor Authentication，您可以改為選擇僅保護具有 *Azure AD 全域管理員*角色的使用者帳戶。 這種方法會針對重要的管理員帳戶提供額外的驗證提示。 視您使用的帳戶類型而定，您可以透過下列其中一種方式來啟用 Azure Multi-Factor Authentication：

* 如果您使用 Microsoft 帳戶，請[註冊多重要素驗證](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。
* 如果您不是使用 Microsoft 帳戶，請[為 Azure AD 中的使用者或群組開啟多重要素驗證](howto-mfa-userstates.md)。

## <a name="next-steps"></a>後續步驟

* 如需詳細資訊，請參閱 [Azure Multi-Factor Authentication 價格](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。
* [何謂條件式存取](../conditional-access/overview.md)

