---
title: 常見問題集 (FAQ) - Azure Active Directory | Microsoft Docs
description: 有關 Azure 及 Azure Active Directory、密碼管理和應用程式存取的常見問題與解答。
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f77304beb598094e8dbbc51ebf624acd71b554b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681736"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>關於 Azure Active Directory 的常見問題集
Azure Active Directory (Azure AD) 是全方位的身分識別即服務 (IDaaS) 解決方案，其涉及範圍橫跨身分識別、存取管理和安全性的所有層面。

如需詳細資訊，請參閱[什麼是 Azure Active Directory？](active-directory-whatis.md)。


## <a name="access-azure-and-azure-active-directory"></a>存取 Azure 和 Azure Active Directory
**問：當我嘗試在 Azure 入口網站中存取 Azure AD 時，為何會收到「找不到訂用帳戶」的訊息？**

**答：** 若要存取 Azure 入口網站，每位使用者都必須擁有 Azure 訂用帳戶的權限。 如果您沒有付費的 Office 365 或 Azure AD 訂用帳戶，您將需要啟用免費 [Azure 帳戶](https://azure.microsoft.com/free/
)或付費訂用帳戶。

如需詳細資訊，請參閱

* [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](active-directory-how-subscriptions-associated-directory.md)

---
**問：Azure AD、Office 365 和 Azure 之間有何關聯性？**

**答：** Azure AD 可提供您所有 Web 服務的常見身分識別和存取功能。 無論您是使用 Office 365、Microsoft Azure、Intune 或其他服務，您都已是使用 Azure AD 協助上述所有服務的登入和存取管理功能。

所有設定要使用 Web 服務的使用者都會定義為一或多個 Azure AD 執行個體中的使用者帳戶。 您可以為這些帳戶設定免費的 Azure AD 功能，如雲端應用程式存取。

Azure AD 付費服務 (例如 Enterprise Mobility + Security) 可透過全方位的企業級管理和安全性解決方案來彌補其他 Web 服務 (例如 Office 365 和 Microsoft Azure) 的不足。

---

**問：擁有者與全域管理員之間有何差異？**

**答：** 依預設，註冊 Azure 訂用帳戶的使用者會獲指派 Azure 資源的擁有者角色。 擁有者可以使用 Azure 訂用帳戶相關聯目錄中的 Microsoft 帳戶，或是公司或學校帳戶。  此角色經過授權，可管理 Azure 入口網站上的服務。

如果其他人必須使用相同的訂用帳戶來登入和存取服務，您可以將適當的[內建角色](../../role-based-access-control/built-in-roles.md)指派給他們。 如需其他資訊，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。

註冊 Azure 訂用帳戶的人員預設會獲指派目錄的全域管理員角色。 全域管理員可以存取所有的 Azure AD 目錄功能。 Azure AD 有一組不同的系統管理員角色，可用來管理目錄和識別相關功能。 這些系統管理員可存取 Azure 入口網站中的各種功能。 系統管理員的角色可決定他們可以執行的作業，例如建立或編輯使用者、將系統管理角色指派給其他人、重設使用者密碼、管理使用者授權或管理網域。  如需有關 Azure AD 目錄管理員及其角色的詳細資訊，請參閱[在 Azure Active Directory 中將使用者指派給系統管理員角色](active-directory-users-assign-role-azure-portal.md)和[在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)。

此外﹐Azure AD 付費服務 (例如 Enterprise Mobility + Security) 可透過全方位的企業級管理和安全性解決方案來彌補其他 Web 服務 (例如 Office 365 和 Microsoft Azure) 的不足。

---
**問：是否有報告可顯示 Azure AD 使用者授權何時即將過期？**

**答：** 否。  目前無法使用此功能。

---

## <a name="get-started-with-hybrid-azure-ad"></a>開始使用混合式 Azure AD


**問：當我新增為共同作業人員時如何離開租用戶？**

**答：** 當您新增為另一個組織租用戶的共同作業人員時，您可以使用右上方的「租用戶切換器」來切換租用戶。  目前沒有任何方法可離開邀請組織，Microsoft 正在努力提供此功能。  在這項功能可用之前，您可以要求邀請組織將您從其租用戶中移除。

---
**問：如何將內部部署目錄連線至 Azure AD？**

**答：** 您可以使用 Azure AD Connect 將內部部署目錄連線至 Azure AD。

如需詳細資訊，請參閱[整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)。

---
**問：如何設定內部部署目錄與雲端應用程式之間的 SSO？**

**答：** 您只需要在內部部署目錄與 Azure AD 之間設定單一登入 (SSO) 即可。 只要您透過 Azure AD 存取雲端應用程式，該服務就會自動讓使用者使用其內部部署認證正確地進行驗證。

透過同盟解決方案 (例如 Active Directory Federation Services (AD FS)) 或藉由設定密碼雜湊同步處理，就能輕鬆實現從內部部署實作 SSO。您可以使用 Azure AD Connect 組態精靈輕鬆部署這兩個選項。

如需詳細資訊，請參閱[整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)。

---
**問：Azure AD 是否為組織中的使用者提供自助入口網站？**

**答：** 是，Azure AD 會為您提供 [Azure AD 存取面板](https://myapps.microsoft.com)，以供使用者進行自助服務和應用程式存取。 如果您是 Office 365 客戶，您可以在 [Office 365 入口網站](https://portal.office.com)中找到許多相同的功能。

如需詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

---
**問：Azure AD 是否會協助我管理內部部署基礎結構？**

**答：** 是。 Azure AD Premium Edition 可為您提供 Azure AD Connect Health。 Azure AD Connect Health 可協助您監視和了解內部部署身分識別基礎結構和同步處理服務。  

如需詳細資訊，請參閱[在雲端中監視內部部署身分識別基礎結構和同步處理服務](../hybrid/whatis-hybrid-identity-health.md)。  

---
## <a name="password-management"></a>密碼管理
**問：是否可以使用 Azure AD 密碼回寫，但不使用密碼同步處理？(在此案例中，可以搭配使用 Azure AD 自助式密碼重設 (SSPR) 和密碼回寫，而不在雲端儲存密碼嗎？)**

**答：** 想要啟用回寫功能，並不需要將 Active Directory 密碼同步處理至 Azure AD。 在同盟環境中，Azure AD 單一登入 (SSO) 會依靠內部部署目錄來驗證使用者。 在這種情況下，並不需要在 Azure AD 中追蹤內部部署密碼。

---
**問：需要多久時間才能將密碼回寫至 Active Directory 內部部署？**

**答：** 密碼回寫會即時運作。

如需詳細資訊，請參閱[開始使用密碼管理](../authentication/quickstart-sspr.md)。

---
**問：是否可以搭配使用密碼回寫和由系統管理員管理的密碼？**

**答：** 是，如果您已啟用密碼回寫，系統管理員所執行的密碼作業便會回寫到內部部署環境。  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>如需密碼相關問題的詳細解答，請參閱[密碼管理常見問題集](../authentication/active-directory-passwords-faq.md)。
---
**問：如果我在嘗試變更密碼時不記得現有的 Office 365/Azure AD 密碼，該如何處理？**

**答：** 在這種情況下有幾種做法。  使用自助式密碼重設 (SSPR) (如果可用的話)。  SSPR 能否運作取決於其設定方式。  如需詳細資訊，請參閱[密碼重設入口網站的運作方式](../authentication/howto-sspr-deployment.md)。

對於 Office 365 使用者，您的系統管理員可以使用[重設使用者密碼](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US)所述的步驟重設密碼。

對於 Azure AD 帳戶，系統管理員可以使用下列其中一種方法重設密碼︰

- [在 Azure 入口網站重設帳戶](active-directory-users-reset-password-azure-portal.md)
- [使用 PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>安全性
**問：帳戶會在特定次數的嘗試失敗之後鎖定，或者是否使用更複雜的策略？**

我們使用更複雜的策略來鎖定帳戶。  此策略是以要求的 IP 和所輸入的密碼為基礎。 鎖定持續期間也會根據它是攻擊的可能性而加長。  

**問：有些 (通用) 密碼遭到拒絕並出現「此密碼已使用許多次」訊息，這是指目前 Active Directory 中使用的密碼嗎？**

這是指全域通用的密碼，例如 “Password” 和 “123456” 的任何變體。

**問：來自可疑來源 (殭屍網路、Tor 端點) 的登入要求是否會在 B2C 租用戶中遭到封鎖，還是基本或進階版本租用戶才有此功能？**

我們沒有適用於所有 B2C 租用戶的閘道，可篩選要求並提供殭屍網路的一些防護功能。

## <a name="application-access"></a>應用程式存取

**問：哪裡可以找到與 Azure AD 預先整合的應用程式及其功能的清單？**

**答：** Azure AD 擁有來自 Microsoft、應用程式服務提供者或合作夥伴超過 2600 個預先整合的應用程式。 所有預先整合的應用程式皆支援單一登入 (SSO)。 SSO 可讓您使用組織認證來存取應用程式。 有些應用程式也支援自動化佈建和取消佈建。

如需完整的預先整合應用程式清單，請參閱 [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)。

---
**問：如果 Azure AD Marketplace 內沒有我需要的應用程式，該怎麼辦？**

**答：** 若您使用 Azure AD Premium，就可以新增及設定您想要的任何應用程式。 視應用程式的功能和您的喜好設定而定，您可以設定 SSO 和自動化佈建。  

如需詳細資訊，請參閱

* [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**問：使用者如何使用 Azure AD 登入應用程式？**

**答：** Azure AD 提供好幾種方式來讓使用者檢視和存取其應用程式，例如︰

* Azure AD 存取面板
* Office 365 應用程式啟動程式
* 直接登入同盟應用程式
* 同盟、密碼或現有應用程式的深層連結

如需詳細資訊，請參閱[應用程式的使用者體驗](../manage-apps/end-user-experiences.md)。

---
**問：Azure AD 可透過哪些不同方式來啟用應用程式的驗證和單一登入？**

**答：** Azure AD 針對驗證和授權支援許多標準化的通訊協定，例如 SAML 2.0、OpenID Connect、OAuth 2.0 和 WS-同盟。 Azure AD 也可針對僅支援表單型驗證的應用程式，支援密碼存放和自動化登入功能。  

如需詳細資訊，請參閱

* [Azure AD 的驗證案例](../develop/authentication-scenarios.md)
* [Active Directory 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Azure AD 中應用程式的單一登入](../manage-apps/what-is-single-sign-on.md)

---
**問：是否可以新增正在內部部署中執行的應用程式？**

**答：** Azure AD 應用程式 Proxy 可讓您簡單又安全地存取您選擇的內部部署 Web 應用程式。 正如同存取 Azure AD 中的軟體即服務 (SaaS) 應用程式一般，相同的方式也可以用來存取這些應用程式。 並不需要 VPN 或變更網路基礎結構。  

如需詳細資訊，請參閱[如何為內部部署應用程式提供安全的遠端存取](../manage-apps/application-proxy.md)。

---
**問：如何對存取特定應用程式的使用者要求多重要素驗證？**

**答：** Azure AD 條件式存取可讓您針對每個應用程式指派唯一的存取原則。 您可以在原則中要求一律要進行 Multi-Factor Authentication，或在使用者未連線到區域網路時才進行。  

如需詳細資訊，請參閱[保護對 Office 365 及其他連接至 Azure Active Directory 之應用程式的存取](../conditional-access/overview.md)。

---
**問：什麼是 SaaS 應用程式的自動化使用者佈建？**

**答：** 使用 Azure AD 可讓您在許多熱門雲端 (SaaS) 應用程式中自動建立、維護和移除使用者身分識別。

如需詳細資訊，請參閱[使用 Azure Active Directory 自動進行 SaaS 應用程式的使用者佈建和解除佈建](../app-provisioning/user-provisioning.md)。

---
**問：是否可以設定與 Azure AD 之間的安全 LDAP 連線？**

**答：** 否。 Azure AD 不直接支援輕量型目錄存取協定 (LDAP) 或安全 LDAP。 不過，您可以在 Azure AD 租用戶上啟用 Azure AD Domain Services (Azure AD DS) 執行個體，並透過 Azure 網路來適當設定網路安全性群組，以達到 LDAP 連線能力。 如需詳細資訊，請參閱[針對 Azure Active Directory Domain Services 受控網域設定安全 LDAP](../../active-directory-domain-services/tutorial-configure-ldaps.md)
