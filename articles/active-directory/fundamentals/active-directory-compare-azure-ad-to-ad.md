---
title: 將活動目錄與 Azure 活動目錄進行比較
description: 本文檔將活動目錄域服務 （ADDS） 與 Azure 活動目錄 （AD） 進行比較。 它概述了兩個標識解決方案中的關鍵概念，並解釋了它的不同或相似的方式。
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 5075ae57df6a7306f0c860690931c846e52c2a89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926892"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>將活動目錄與 Azure 活動目錄進行比較

Azure 活動目錄是雲標識和訪問管理解決方案的下一個演變。 Microsoft 在 Windows 2000 中引入了 Active Directory 域服務，使組織能夠使用每個使用者使用單個標識管理多個本地基礎結構元件和系統。

Azure AD 通過為跨雲和本地的所有應用為組織提供身份即服務 （IDaaS） 解決方案，將此方法提升到一個新的水準。

大多數 IT 管理員都熟悉活動目錄域服務概念。 下表概述了活動目錄概念和 Azure 活動目錄之間的差異和相似性。

|概念|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**使用者**|||
|預配：使用者 | 組織手動創建內部使用者或使用內部或自動預配系統（如 Microsoft 標識管理器）與 HR 系統集成。|現有 AD 組織使用[Azure AD 連接](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)將標識同步到雲。</br> Azure AD 添加了支援，以便從[雲 HR 系統](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)自動創建使用者。 </br>Azure AD 可以在啟用[SCIM 的](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)SaaS 應用中預配標識，以自動向應用提供必要的詳細資訊，以便使用者訪問。 |
|預配：外部標識| 組織在專用外部 AD 林中手動創建外部使用者，從而導致管理管理外部標識的生命週期（來賓使用者）| Azure AD 提供一個特殊的標識類來支援外部標識。 [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)將管理指向外部使用者標識的連結，以確保它們有效。 |
| 權利管理和組| 管理員使使用者成為組的成員。 然後，應用和資源擁有者向組授予對應用或資源的存取權限。| [組](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)在 Azure AD 中也可用，管理員也可以使用組向資源授予許可權。 在 Azure AD 中，管理員可以手動將成員身份分配給組，或使用查詢動態地將使用者包括到組。 </br> 管理員可以使用 Azure AD 中[的權利管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)，使使用者使用工作流（如有必要）基於時間的條件，訪問應用和資源的集合。 |
| 管理管理|組織將使用 AD 中的域、組織單位和組的組合來委派管理許可權來管理它控制的目錄和資源。| Azure AD 通過其基於角色的存取控制 （RBAC） 系統提供[內置角色](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)，對[創建自訂角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)以委派對標識系統、應用及其控制的資源的存取權限的支援有限。</br>通過[特權標識管理 （PIM）](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)可以增強管理角色，以便提供對特權角色的及時、有時間限制或基於工作流的訪問。 |
| 認證管理| 活動目錄中的憑據基於密碼、證書身份驗證和智慧卡身份驗證。 密碼使用基於密碼長度、過期和複雜性的密碼原則進行管理。|Azure AD 對雲和本地使用智慧[密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)。 保護包括智慧鎖定加上阻止常用和自訂密碼短語和替換。 </br>Azure AD[通過多重要素驗證](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)和無[密碼](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)技術（如 FIDO2）顯著提高安全性。 </br>Azure AD 為使用者提供[自助服務密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)系統，從而降低了支援成本。 |
| **應用程式**|||
| 基礎設施應用|活動目錄構成了許多基礎結構本機群組件的基礎，例如 DNS、DHCP、IPSec、WiFi、NPS 和 VPN 訪問|在新的雲世界中，Azure AD 是訪問應用與依賴網路控制的新控制平面。 當使用者對[進行身份驗證時，條件訪問 （CA）](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)將控制哪些使用者，將有權訪問哪些應用在要求條件下。|
| 傳統應用和傳統應用| 大多數本地應用使用 LDAP、Windows 集成身份驗證（NTLM 和 Kerberos）或基於標頭的身份驗證來控制對使用者的訪問。| Azure AD 可以使用在本地運行的 Azure AD[應用程式代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)提供對這些類型的本地應用的訪問。 使用此方法，Azure AD 可以在遷移或需要與舊應用共存時使用 Kerberos 對本地活動目錄使用者進行身份驗證。 |
| SaaS 應用程式|活動目錄不支援本地的 SaaS 應用，並且需要聯合系統（如 AD FS）。|支援 OAuth2、SAML 和 WS 身份驗證的\*SaaS 應用可以集成以使用 Azure AD 進行身份驗證。 |
| 具有現代身份驗證的業務線 （LOB） 應用|組織可以使用帶有活動目錄的 AD FS 來支援需要現代身份驗證的 LOB 應用。| 需要現代身份驗證的 LOB 應用可以配置為使用 Azure AD 進行身份驗證。 |
| 中端/守護族服務|在本地環境中運行的服務通常使用 AD 服務帳戶或組託管服務帳戶 （gMSA） 運行。 然後，這些應用將繼承服務帳戶的許可權。| Azure AD 提供[託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)，用於在雲中運行其他工作負荷。 這些標識的生命週期由 Azure AD 管理，並且綁定到資來源提供者，不能用於其他目的來獲得後門訪問。|
| **裝置**|||
| 行動訊息|沒有協力廠商解決方案，Active Directory 不支援本機支援行動裝置。| 微軟的行動裝置管理解決方案微軟Intune與 Azure AD 集成。 Microsoft Intune 向標識系統提供設備狀態資訊，供身份驗證期間進行評估。 |
| 視窗桌面|Active Directory 提供使用群組原則、系統中心組態管理員或其他協力廠商解決方案對加入 Windows 設備進行域連接以對其進行管理的功能。|Windows 設備可以[連接到 Azure AD](https://docs.microsoft.com/azure/active-directory/devices/)。 條件訪問可以檢查設備是否作為身份驗證過程的一部分加入 Azure AD。 視窗設備也可以管理與[微軟Intune。](https://docs.microsoft.com/intune/what-is-intune) 在這種情況下，條件訪問將考慮設備是否是投訴（例如，最新的安全修補程式和病毒簽名），然後再允許訪問應用。|
| Windows 伺服器| Active Directory 使用群組原則或其他管理解決方案為本地 Windows 伺服器提供強大的管理功能。| Azure 中的 Windows 伺服器虛擬機器可以使用[Azure AD 域服務](https://docs.microsoft.com/azure/active-directory-domain-services/)進行管理。 當 VM 需要訪問標識系統目錄或資源時，可以使用[託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)。|
| Linux/Unix 工作負載|Active Directory 不支援沒有協力廠商解決方案的非 Windows，儘管可以將 Linux 電腦配置為將 Active Directory 配置為 Kerberos 領域進行身份驗證。|Linux/Unix VM 可以使用[託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)來訪問標識系統或資源。 某些組織將這些工作負載遷移到雲容器技術，這些技術也可以使用託管標識。|

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 活動目錄？](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [比較自我管理 Active Directory Domain Services、Azure Active Directory 和受控 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)
- [關於 Azure Active Directory 的常見問題集](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-faq)
- [Azure 活動目錄中有哪些新增功能？](https://docs.microsoft.com/azure/active-directory/fundamentals/whats-new)
