---
title: Azure 多因素驗證版本和取用方案
description: 瞭解 Azure 多因素驗證用戶端，以及可用的不同方法和版本。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e49a07f591731a1deb2838751852c0134548966d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77521841"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Azure 多重要素驗證的功能與授權

為了保護您組織中的使用者帳戶，應該使用多重要素驗證。 這項功能對於資源具有特殊存取權限的帳戶特別重要。 Office 365 和 Azure Active Directory （Azure AD）系統管理員可以使用基本的多重要素驗證功能，無需額外費用。 如果您想要升級系統管理員的功能，或將多重要素驗證擴充到其他使用者，您可以透過數種方式購買 Azure 多重要素驗證。

> [!IMPORTANT]
> 本文詳細說明可授權和使用 Azure 多重要素驗證的不同方式。 如需定價和計費的特定詳細資料，請參閱[Azure 多因素驗證定價頁面](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure Multi-Factor Authentication 版本

根據您組織的需求，您可以使用幾種不同的方式，並授權 Azure 多重要素驗證。 您可能已經有權使用 Azure 多重要素驗證，視您目前擁有的 Azure AD、Office 365、EMS 或 Microsoft 365 授權而定。 下表詳細說明取得 Azure 多重要素驗證的不同方式，以及各項功能和使用案例。

| 如果您是的使用者 | 功能和使用案例 |
| --- | --- |
| EMS 或 Microsoft 365 E3 和 E5 | EMS E3 或 Microsoft 365 E3 （包括 EMS 和 Office 365）包含 Azure AD Premium P1。 EMS E5 或 Microsoft 365 E5 包含 Azure AD Premium P2。 您可以使用下列各節中所述的相同條件式存取功能，為使用者提供多重要素驗證。 |
| Azure AD Premium P1 | 您可以使用[Azure AD 條件式存取](../conditional-access/overview.md)，在特定案例或事件期間提示使用者進行多重要素驗證，以符合您的商務需求。 |
| Azure AD Premium P2 | 提供最強的安全性位置和更好的使用者體驗。 新增以[風險為基礎的條件式存取](../conditional-access/howto-conditional-access-policy-risk.md)Azure AD Premium P1 功能，以適應使用者的模式，並將多重要素驗證提示降至最低。 |
| Office 365 Business Premium、E3 或 E5 | 所有使用者都可以在所有登入事件中，啟用或停用 Azure 多重要素驗證。 只有在某些情況下，才能夠啟用多重要素驗證，或僅限於特定案例。 管理是透過 Office 365 入口網站。 如需改善的使用者體驗，請升級至 Azure AD Premium P1 或 P2，並使用條件式存取。 如需詳細資訊，請參閱[使用多重要素驗證保護 Office 365 資源](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)。 |
| Azure AD Free | 您可以使用[安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)，在每次進行驗證要求時，為所有使用者啟用多重要素驗證。 您不會對已啟用的使用者或案例有細微的控制，但是它確實提供了額外的安全性步驟。<br /> 即使未使用安全性預設值來為每個人啟用多重要素驗證，獲指派*Azure AD 全域管理員*角色的使用者，都可以設定為使用多重要素驗證。 免費層的這項功能可確保重要的系統管理員帳戶受到多重要素驗證的保護。 |

## <a name="feature-comparison-of-versions"></a>版本的功能比較

下表提供 Azure Multi-Factor Authentication 各版本中可用的功能清單。 規劃保護使用者驗證的需求，然後判斷哪一種方法符合這些需求。 例如，雖然 Azure AD Free 提供提供 Azure 多重要素驗證的安全性預設值，但只有行動驗證器應用程式可用於驗證提示，而不是電話或 SMS。 如果您無法確定行動驗證應用程式是安裝在使用者的個人裝置上，這種方法可能會是一項限制。

| 功能 | Azure AD Free-安全性預設值 | Azure AD Free Azure AD 全域管理員 | Office 365 Business Premium、E3 或 E5 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| 透過 MFA 保護 Azure AD 系統管理員帳戶 | ● | ●（僅*Azure AD 全域管理員*帳戶） | ● | ● |
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
> 自2019年3月起，在 Azure AD Free/試用版租使用者中，Azure 多重要素驗證和 Azure 自助式密碼重設使用者已不再提供通話選項。 SMS 訊息不會受到這項變更的影響。 Azure AD Premium P1 或 P2 租使用者中的使用者，或使用或 Office 365 Business Premium、E3 或 E5，仍可繼續使用通話。

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>購買並啟用 Azure 多重要素驗證

若要使用 Azure 多重要素驗證，請註冊或購買合格的 Azure AD 層。 Azure AD 有四種版本：免費版、Office 365 應用程式版本（適用于 Office 365 Business Premium E3 或 E5 客戶）、Premium P1 和 Premium P2。

免費版隨附于 Azure 訂用帳戶。 如需如何使用安全性預設值或以*Azure AD 全域管理員*角色保護帳戶的相關資訊，請參閱[下一節](#azure-ad-free-tier)。

Azure AD Premium 版本可透過您的 Microsoft 代表、 [Open Volume 授權方案](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)及[雲端解決方案提供者方案](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)取得。 Azure 和 Office 365 訂閱者也可以在線上購買 Azure Active Directory Premium P1 和 P2。 登[入](https://portal.office.com/Commerce/Catalog.aspx)以購買。

> [!IMPORTANT]
> 自2018年9月1日起，新客戶將無法再使用以耗用量為基礎的授權。 使用以耗用量為基礎的模型的現有客戶可以繼續使用每個已啟用的使用者或每一驗證計費。

在您購買必要的 Azure AD 層之後，請[規劃及部署 Azure 多因素驗證](howto-mfa-getstarted.md)。

### <a name="azure-ad-free-tier"></a>Azure AD Free 層

Azure AD Free 租使用者中的所有使用者都可以透過使用安全性預設值來使用 Azure 多重要素驗證。 這些安全性預設值會在每次使用者登入時，為所有使用者啟用 Azure 多重要素驗證。 在使用 Azure AD Free 安全性預設值時，行動驗證應用程式是唯一可用於 Azure 多重要素驗證的方法。

* [深入瞭解 Azure AD 安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)
* [為 Azure AD Free 中的使用者啟用安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

如果您不想要為所有使用者和每個登入事件啟用 Azure 多重要素驗證，您可以改為選擇僅使用*Azure AD 全域管理員*角色來保護使用者帳戶。 這種方法會針對重要的系統管理員帳戶提供額外的驗證提示。 您可以透過下列其中一種方式來啟用 Azure 多重要素驗證，視您使用的帳戶類型而定：

* 如果您使用 Microsoft 帳戶，請[註冊多重要素驗證](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。
* 如果您不是使用 Microsoft 帳戶，請[在 Azure AD 中開啟使用者或群組的多重要素驗證](howto-mfa-userstates.md)。

## <a name="next-steps"></a>後續步驟

如需成本的詳細資訊，請參閱[Azure 多因素驗證定價](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。
