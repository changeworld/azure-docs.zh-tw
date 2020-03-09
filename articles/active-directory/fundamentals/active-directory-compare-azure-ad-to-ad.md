---
title: 比較 Active Directory 與 Azure Active Directory
description: 本檔會比較 Active Directory Domain Services （新增）至 Azure Active Directory （AD）。 其中概述了身分識別解決方案中的重要概念，並說明其不同或相似之處。
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
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926892"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>比較 Active Directory 與 Azure Active Directory

Azure Active Directory 是雲端身分識別和存取管理解決方案的下一個進化。 Microsoft 引進了 Windows 2000 中的 Active Directory Domain Services，讓組織能夠使用每個使用者的單一身分識別，來管理多個內部部署基礎結構元件和系統。

Azure AD 藉由為組織提供身分識別即服務（IDaaS）解決方案，跨雲端和內部部署的所有應用程式，將此方法帶到下一個層級。

大部分的 IT 系統管理員都熟悉 Active Directory Domain Services 的概念。 下表概述 Active Directory 概念和 Azure Active Directory 之間的差異和相似性。

|概念|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**使用者**|||
|布建：使用者 | 組織會手動建立內部使用者，或使用內建或自動化的布建系統（例如 Microsoft Identity Manager）來與 HR 系統整合。|現有的 AD 組織會使用[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)將身分識別同步至雲端。</br> Azure AD 新增了從[雲端 HR 系統](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)自動建立使用者的支援。 </br>Azure AD 可以在[已啟用 SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)的 SaaS 應用程式中布建身分識別，以自動為應用程式提供必要的詳細資料，以允許使用者存取。 |
|布建：外部身分識別| 組織會以一般使用者的身分，在專用的外部 AD 樹系中手動建立外部使用者，因而導致管理外部身分識別生命週期的系統管理負擔（來賓使用者）| Azure AD 提供一種特殊的身分識別類別，以支援外部身分識別。 [AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)會管理外部使用者身分識別的連結，以確保其有效。 |
| 權利管理和群組| 系統管理員讓使用者成為群組成員。 然後，應用程式和資源擁有者會將應用程式或資源的存取權授與群組。| Azure AD 中也提供[群組](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)，而且系統管理員也可以使用群組來授與資源的許可權。 在 Azure AD 中，系統管理員可以手動將成員資格指派給群組，或使用查詢以動態方式將使用者加入至群組。 </br> 系統管理員可以使用 Azure AD 中的「[權利管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)」，讓使用者能夠使用工作流程來存取應用程式和資源的集合，並視需要以時間為基礎的準則。 |
| 管理員管理|組織將使用 AD 中的網域、組織單位和群組的組合來委派系統管理許可權，以管理其所控制的目錄和資源。| Azure AD 提供具有其角色型存取控制（RBAC）系統的[內建角色](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)，且支援[建立自訂角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)來委派對身分識別系統、應用程式和其所控制資源的特殊許可權存取權。</br>管理角色可以利用[Privileged Identity Management （PIM）](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)來增強，以提供限時、限時或以工作流程為基礎的特殊許可權角色存取。 |
| 認證管理| Active Directory 中的認證是以密碼、憑證驗證和智慧卡驗證為基礎。 密碼是使用以密碼長度、到期日和複雜度為依據的密碼原則來管理。|Azure AD 針對雲端和內部部署使用智慧型[密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)。 保護包括智慧型鎖定，以及封鎖一般和自訂密碼片語和替代。 </br>Azure AD[透過多重要素驗證](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)和[無密碼](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)技術（例如 FIDO2）大幅提升安全性。 </br>Azure AD 為使用者提供[自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)系統，以降低支援成本。 |
| **應用程式**|||
| 基礎結構應用程式|Active Directory 形成許多基礎結構內部部署元件（例如 DNS、DHCP、IPSec、WiFi、NPS 和 VPN 存取）的基礎|在新的雲端環境中，Azure AD 是用來存取應用程式的新控制平面，而不是依賴網路控制。 當使用者進行驗證時，[條件式存取（CA）](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)會控制哪些使用者可以存取所需條件下的哪些應用程式。|
| 傳統和繼承應用程式| 大部分的內部部署應用程式會使用 LDAP、Windows 整合式驗證（NTLM 和 Kerberos），或以標頭為基礎的驗證來控制使用者的存取權。| Azure AD 可以使用內部部署執行[Azure AD 應用程式 proxy 代理程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)，來提供這些內部部署應用程式類型的存取權。 使用此方法 Azure AD 可以在您遷移或需要與繼承應用程式並存時，使用 Kerberos 驗證內部部署的 Active Directory 使用者。 |
| SaaS 應用程式|Active Directory 不會以原生方式支援 SaaS 應用程式，而且需要同盟系統，例如 AD FS。|可整合支援 OAuth2、SAML 和 WS\* authentication 的 SaaS 應用程式，以使用 Azure AD 進行驗證。 |
| 具有新式驗證的企業營運（LOB）應用程式|組織可以搭配 Active Directory 使用 AD FS，以支援需要新式驗證的 LOB 應用程式。| 需要新式驗證的 LOB 應用程式可以設定為使用 Azure AD 進行驗證。 |
| 中層/後臺服務|在內部部署環境中執行的服務通常會使用 AD 服務帳戶或群組受管理的服務帳戶（gMSA）來執行。 然後，這些應用程式會繼承服務帳戶的許可權。| Azure AD 提供[受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)識別，以在雲端中執行其他工作負載。 這些身分識別的生命週期是由 Azure AD 管理，而且系結至資源提供者無法用於其他目的，以取得後門程式存取權。|
| **Devices**|||
| 行動|Active Directory 不會在沒有協力廠商解決方案的情況下，原生支援行動裝置。| Microsoft 的行動裝置管理解決方案（Microsoft Intune）已與 Azure AD 整合。 Microsoft Intune 提供裝置狀態資訊給身分識別系統，以在驗證期間進行評估。 |
| Windows 桌面|Active Directory 提供網域加入 Windows 裝置的功能，以使用群組原則、System Center Configuration Manager 或其他協力廠商解決方案來管理它們。|Windows 裝置可以[聯結到 Azure AD](https://docs.microsoft.com/azure/active-directory/devices/)。 條件式存取可以檢查裝置是否 Azure AD 在驗證過程中聯結。 Windows 裝置也可以使用[Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune)來管理。 在此情況下，條件式存取會在允許存取應用程式之前，考慮裝置是否為投訴（例如，最新的安全性修補程式和病毒碼標記）。|
| Windows 伺服器| Active Directory 為使用群組原則或其他管理解決方案的內部部署 Windows 伺服器提供強大的管理功能。| 您可以使用[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/)來管理 Azure 中的 Windows server 虛擬機器。 當 Vm 需要存取身分識別系統目錄或資源時，可以使用[受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)識別。|
| Linux/Unix 工作負載|Active Directory 不會在不使用協力廠商解決方案的情況下，原生支援非 Windows，不過 Linux 電腦可以設定為以 Active Directory 作為 Kerberos 領域進行驗證。|Linux/Unix Vm 可以使用[受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)識別來存取身分識別系統或資源。 有些組織會將這些工作負載遷移至雲端容器技術，也可以使用受控識別。|

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure Active Directory？](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [比較自我管理的 Active Directory Domain Services、Azure Active Directory 和受控 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)
- [關於 Azure Active Directory 的常見問題](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-faq)
- [Azure Active Directory 的新功能](https://docs.microsoft.com/azure/active-directory/fundamentals/whats-new)
