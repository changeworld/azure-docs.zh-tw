---
title: Azure 多重要素驗證版本和消費計畫
description: 瞭解 Azure 多重要素驗證用戶端以及可用的不同方法和版本。
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
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647997"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Azure 多重要素驗證的功能和許可證

為了保護組織中的使用者帳戶，應使用多重要素驗證。 這項功能對於資源具有特殊存取權限的帳戶特別重要。 Office 365 和 Azure 活動目錄 （Azure AD） 管理員可以使用基本多重要素驗證功能，無需額外費用。 如果要升級管理員的功能或將多重要素驗證擴展到其他使用者，可以通過多種方式購買 Azure 多重要素驗證。

> [!IMPORTANT]
> 本文詳細介紹了 Azure 多重要素驗證可以許可和使用的不同方式。 有關定價和計費的具體詳細資訊，請參閱[Azure 多重要素驗證定價頁](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure Multi-Factor Authentication 版本

Azure 多重要素驗證可以根據組織的需求以幾種不同的方式使用和許可。 根據您當前擁有的 Azure AD、Office 365、EMS 或 Microsoft 365 許可證，您可能已經有權使用 Azure 多重要素驗證。 下表詳細介紹了獲取 Azure 多重要素驗證的不同方法，以及每個功能和用例的某些功能和用例。

| 如果您是 | 功能和用例 |
| --- | --- |
| EMS 或微軟 365 E3 和 E5 | EMS E3 或 Microsoft 365 E3（包括 EMS 和 Office 365）包括 Azure AD 高級 P1。 EMS E5 或 Microsoft 365 E5 包括 Azure AD 高級 P2。 您可以使用以下各節仲介紹的相同條件訪問功能為使用者提供多重要素驗證。 |
| Azure AD Premium P1 | 可以使用[Azure AD 條件訪問](../conditional-access/overview.md)提示使用者在某些方案或事件期間進行多重要素驗證，以滿足您的業務需求。 |
| Azure AD Premium P2 | 提供最強的安全位置和改進的使用者體驗。 添加[基於風險的條件訪問](../conditional-access/howto-conditional-access-policy-risk.md)Azure AD 高級 P1 功能，這些功能可適應使用者的模式並最大限度地減少多重要素驗證提示。 |
| Office 365 業務高級版、E3 或 E5 | 對於所有使用者，對於所有登錄事件，Azure 多重要素驗證已啟用或禁用。 無法僅為使用者子集啟用多重要素驗證，或僅在某些情況下啟用多重要素驗證。 管理通過 Office 365 門戶。 要改進使用者體驗，請使用 Azure AD 高級 P1 或 P2 並使用條件訪問。 有關詳細資訊，請參閱[使用多重要素驗證的安全 Office 365 資源](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)。 |
| Azure AD Free | 每次發出身份驗證請求時，都可以使用[安全預設值](../fundamentals/concept-fundamentals-security-defaults.md)為所有使用者啟用多重要素驗證。 您不能對已啟用的使用者或方案進行精細控制，但它確實提供了該附加的安全步驟。<br /> 即使安全預設值不用於為所有人啟用多重要素驗證，也可以將分配*Azure AD 全域管理員*角色的使用者可以配置為使用多重要素驗證。 免費層的此功能可確保關鍵管理員帳戶受多重要素驗證的保護。 |

## <a name="feature-comparison-of-versions"></a>版本的功能比較

下表提供 Azure Multi-Factor Authentication 各版本中可用的功能清單。 規劃保護使用者身份驗證的需求，然後確定哪種方法滿足這些要求。 例如，儘管 Azure AD Free 提供提供 Azure 多重要素驗證的安全預設值，但只有移動身份驗證器應用可用於身份驗證提示，而不能用於撥打電話或 SMS。 如果無法確保移動身份驗證應用安裝在使用者的個人設備上，則此方法可能是一個限制。

| 功能 | Azure AD 免費 - 安全預設值 | Azure AD 免費 - Azure AD 全域管理員 | Office 365 業務高級版、E3 或 E5 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| 使用 MFA 保護 Azure AD 租戶管理員帳戶 | ● | * （僅限*Azure AD 全域管理員*帳戶） | ● | ● |
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
> 自 2019 年 3 月起，Azure 多重要素驗證和 Azure 自助服務密碼重設使用者在 Azure AD 免費/試用租戶中不再使用撥打電話選項。 短信不受此更改的影響。 撥打電話繼續可供 Azure AD 高級 P1 或 P2 租戶或使用或 Office 365 業務高級版、E3 或 E5 中的使用者使用。

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>購買並啟用 Azure 多重要素驗證

要使用 Azure 多重要素驗證，請註冊或購買符合條件的 Azure AD 層。 Azure AD 有四個版本 - 免費、Office 365 應用版本（適用于 Office 365 業務高級 E3 或 E5 客戶）、高級 P1 和高級 P2。

免費版包含在 Azure 訂閱中。 有關如何使用安全預設值或使用*Azure AD 全域管理員*角色保護帳戶的資訊，請參閱[以下部分](#azure-ad-free-tier)。

Azure AD 高級版可通過 Microsoft 代表、[開放大量授權證計畫和](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)[雲解決方案供應商計畫](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)獲得。 Azure 和 Office 365 訂閱者也可以於線上購買 Azure Active Directory Premium P1 和 P2。 [登錄](https://portal.office.com/Commerce/Catalog.aspx)購買。

> [!IMPORTANT]
> 自 2018 年 9 月 1 日起，新客戶不再使用基於消費的許可。 使用基於消耗的模型的現有客戶可以繼續按啟用的使用者或每個身份驗證計費使用。

購買所需的 Azure AD 層後，[規劃和部署 Azure 多重要素驗證](howto-mfa-getstarted.md)。

### <a name="azure-ad-free-tier"></a>Azure AD 免費套餐

Azure AD 免費租戶中的所有使用者都可以使用安全預設值使用 Azure 多重要素驗證。 這些安全預設值為所有使用者在登錄時啟用 Azure 多重要素驗證。 移動身份驗證應用是使用 Azure AD 免費安全預設值時可用於 Azure 多重要素驗證的唯一方法。

* [瞭解有關 Azure AD 安全預設值的更多](../fundamentals/concept-fundamentals-security-defaults.md)
* [為 Azure AD 免費中的使用者啟用安全預設值](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

如果不想為所有使用者和每個登錄事件啟用 Azure 多重要素驗證，則可以選擇僅使用*Azure AD 全域管理員*角色保護使用者帳戶。 此方法為關鍵管理員帳戶提供其他身份驗證提示。 您可以根據所使用的帳戶類型，以以下方式之一啟用 Azure 多重要素驗證：

* 如果您使用 Microsoft 帳戶，[請註冊進行多重要素驗證](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。
* 如果不使用 Microsoft 帳戶，[請為 Azure AD 中的使用者或組打開多重要素驗證](howto-mfa-userstates.md)。

## <a name="next-steps"></a>後續步驟

有關成本的詳細資訊，請參閱[Azure 多重要素驗證定價](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。
