---
title: Azure AD Multi-Factor Authentication 版本和取用方案
description: 瞭解 Azure AD Multi-Factor Authentication 用戶端和可用的不同方法和版本。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204a656194af9473defc8cf983526dd2b8266021
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839806"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication 的功能和授權

為了保護組織中的使用者帳戶，建議您使用多重要素驗證。 這項功能對於資源具有特殊存取權限的帳戶特別重要。 基本的多重要素驗證功能可 Microsoft 365 和 Azure Active Directory (Azure AD) 系統管理員，無需額外費用。 如果您想要升級系統管理員的功能，或將多重要素驗證擴充到其他使用者，您可以透過數種方式購買 Azure AD Multi-Factor Authentication。

> [!IMPORTANT]
> 本文將詳細說明可授權和使用 Azure AD Multi-Factor Authentication 的不同方式。 如需定價和帳單的特定詳細資料，請參閱 [Azure AD Multi-Factor Authentication 定價頁面](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>可用的 Azure AD 版本 Multi-Factor Authentication

您可以根據組織的需求，使用不同的方式來使用和授權 Azure AD Multi-Factor Authentication。 根據您目前擁有的 Azure AD、EMS 或 Microsoft 365 授權，您可能已經有權使用 Azure AD Multi-Factor Authentication。 下表詳細說明取得 Azure AD Multi-Factor Authentication 以及各項功能和使用案例的不同方式。

| 如果您使用 | 功能和使用案例 |
| --- | --- |
| Microsoft 365 商務版 Premium 和 EMS 或 Microsoft 365 E3 和 E5 | EMS E3、Microsoft 365 E3 和 Microsoft 365 商務版 Premium 包含 Azure AD Premium P1。 EMS E5 或 Microsoft 365 E5 包含 Azure AD Premium P2。 您可以使用下列各節中所述的相同條件式存取功能，為使用者提供多重要素驗證。 |
| Azure AD Premium P1 | 您可以使用 [Azure AD 條件式存取](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)，在特定案例或事件中提示使用者進行多重要素驗證，以符合您的商務需求。 |
| Azure AD Premium P2 | 提供最強的安全性位置和改善使用者體驗。 將[依據風險的條件式存取](../conditional-access/howto-conditional-access-policy-risk.md)新增至會配合使用者模式而調整的 Azure AD Premium P1 功能，並盡可能減少多重要素驗證提示。 |
| 所有 Microsoft 365 方案 | 您可以針對 [每位使用者啟用](howto-mfa-userstates.md)Azure AD Multi-Factor Authentication，或針對所有使用 [安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)的使用者啟用或停用。 Azure AD Multi-Factor Authentication 的管理是透過 Microsoft 365 入口網站來管理。 若想要改善使用者體驗，請升級至 Azure AD Premium P1 或 P2，並使用條件式存取。 如需詳細資訊，請參閱 [使用多重要素驗證保護 Microsoft 365 資源](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication)。 |
| Azure AD Free | 您可以使用 [安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md) ，為所有使用者啟用多重要素驗證。 您無法精確地控制已啟用的使用者或情節，但是它確實提供了額外的安全性步驟。<br /> 即使未使用安全性預設值來為每個人啟用多重要素驗證，獲指派 *Azure AD 全域管理員* 角色的使用者還是可以設定為使用多重要素驗證。 免費層的這項功能可確保重要的管理員帳戶受到多重要素驗證的保護。 |

## <a name="feature-comparison-of-versions"></a>版本的功能比較

下表提供各種版本的 Azure AD Multi-Factor Authentication 所提供的功能清單。 規劃保護使用者驗證的需求，然後判斷哪一種方法符合這些需求。 例如，雖然 Azure AD Free 提供可提供 Azure AD Multi-Factor Authentication 的安全性預設，但只有行動驗證器應用程式可以用於驗證提示，而不是電話或 SMS。 如果您無法確保行動驗證應用程式是安裝在使用者的個人裝置上，這種方法可能會是一項限制。

| 功能 | Azure AD Free - 安全性預設值 | Azure AD Free - Azure AD 全域管理員 | Microsoft 365 應用程式 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| 使用 MFA 保護 Azure AD 租用戶管理帳戶 | ● | ● (僅限 *Azure AD 全域管理員* 帳戶) | ● | ● |
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

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>購買並啟用 Azure AD Multi-Factor Authentication

若要使用 Azure AD Multi-Factor Authentication，請註冊或購買符合資格的 Azure AD 層。 Azure AD 共有四種版本： Free、Microsoft 365 apps、Premium P1 及 Premium P2。

Free 版本隨附於 Azure 訂閱。 請參閱 [下節](#azure-ad-free-tier)，以取得如何使用安全性預設值或透過 *Azure AD 全域管理員* 角色保護帳戶的資訊。

Azure AD Premium 版本可透過您的 Microsoft 代表、[Open Volume 授權方案](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)及[雲端解決方案提供者方案](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)取得。 Azure 和 Microsoft 365 訂閱者也可以於線上購買 Azure Active Directory Premium P1 及 P2。 [登入](https://portal.office.com/Commerce/Catalog.aspx)以購買。

在您購買必要的 Azure AD 層之後，請 [規劃和部署 Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)。

### <a name="azure-ad-free-tier"></a>Azure AD Free 階層

Azure AD Free 租使用者中的所有使用者都可以使用安全性預設值 Azure AD Multi-Factor Authentication。 行動驗證應用程式是使用 Azure AD Free 安全性預設值時，可用於 Azure AD Multi-Factor Authentication 的唯一方法。

* [深入了解 Azure AD 安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)
* [為 Azure AD Free 中的使用者啟用安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

如果您不想要為所有使用者啟用 Azure AD Multi-Factor Authentication，您可以改為選擇只以 *Azure AD 全域管理員* 角色保護使用者帳戶。 這種方法會針對重要的管理員帳戶提供額外的驗證提示。 您可以使用下列其中一種方式來啟用 Azure AD Multi-Factor Authentication，視您使用的帳戶類型而定：

* 如果您使用 Microsoft 帳戶，請[註冊多重要素驗證](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。
* 如果您不是使用 Microsoft 帳戶，請[為 Azure AD 中的使用者或群組開啟多重要素驗證](howto-mfa-userstates.md)。

## <a name="next-steps"></a>後續步驟

* 如需成本的詳細資訊，請參閱 [Azure AD Multi-Factor Authentication 定價](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。
* [何謂條件式存取](../conditional-access/overview.md)

