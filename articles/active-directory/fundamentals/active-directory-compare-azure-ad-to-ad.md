---
title: 比較 Active Directory 與 Azure Active Directory (機器翻譯)
description: 本檔會比較 Active Directory Domain Services (將) 新增至 Azure Active Directory (AD) 。 其中概述兩種身分識別解決方案的主要概念，並說明其不同或相似之處。
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
ms.openlocfilehash: 3c8cf84aa697512b6d1147bf853d30761792370b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207303"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>比較 Active Directory 與 Azure Active Directory (機器翻譯)

Azure Active Directory 是雲端身分識別和存取管理解決方案的下一次演進。 Microsoft 在 Windows 2000 中引進 Active Directory Domain Services，讓組織能夠使用每位使用者的單一身分識別來管理多個內部部署基礎結構元件和系統。

Azure AD 可為組織提供身分識別即服務， (IDaaS 跨雲端和內部部署的所有應用程式) 解決方案，以將這種方法帶到下一個層級。

大部分的 IT 系統管理員都熟悉 Active Directory Domain Services 概念。 下表概述 Active Directory 概念和 Azure Active Directory 之間的差異和相似性。

|概念|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**使用者**|||
|布建：使用者 | 組織會手動建立內部使用者，或使用內部或自動化的布建系統（例如 Microsoft Identity Manager）來與 HR 系統整合。|現有的 AD 組織會使用 [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) 將身分識別同步至雲端。</br> Azure AD 新增了自動從 [雲端 HR 系統](../saas-apps/workday-tutorial.md)建立使用者的支援。 </br>Azure AD 可以在 [已啟用 SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 的 SaaS 應用程式中布建身分識別，以自動為應用程式提供必要的詳細資料，以允許使用者進行存取。 |
|布建：外部身分識別| 組織會以一般使用者的身分在專用的外部 AD 樹系中手動建立外部使用者，因而導致管理額外負荷，以管理 (來賓使用者的外部身分識別生命週期) | Azure AD 提供特殊的身分識別類別來支援外部身分識別。 [AZURE AD B2B](/azure/active-directory/b2b/) 將會管理外部使用者身分識別的連結，以確保其有效。 |
| 權利管理和群組| 系統管理員讓使用者成為群組的成員。 然後，應用程式和資源擁有者會將應用程式或資源的存取權授與群組。| [群組](./active-directory-groups-create-azure-portal.md) 也可在 Azure AD 中使用，而系統管理員也可以使用群組來授與資源的許可權。 在 Azure AD 中，系統管理員可以手動指派成員資格給群組，或使用查詢將使用者動態加入群組。 </br> 系統管理員可以在 Azure AD 中使用 [權利管理](../governance/entitlement-management-overview.md) ，讓使用者能夠使用工作流程和以時間為基礎的準則來存取應用程式和資源集合。 |
| 管理管理|組織會在 AD 中使用網域、組織單位和群組的組合，以委派管理許可權來管理其所控制的目錄和資源。| Azure AD 使用 Azure AD 的角色型存取控制 (Azure AD RBAC) 系統提供 [內建角色](./active-directory-users-assign-role-azure-portal.md) ，有限的支援可讓您 [建立自訂角色](../users-groups-roles/roles-custom-overview.md) ，以將特殊許可權存取委派給身分識別系統、應用程式和其所控制的資源。</br>您可以使用 [Privileged Identity Management (PIM) ](../privileged-identity-management/pim-configure.md) 來增強管理角色，以提供即時、時間限制或以工作流程為基礎的特殊許可權角色存取權。 |
| 認證管理| Active Directory 中的認證是以密碼、憑證驗證和智慧卡驗證為基礎。 密碼是使用根據密碼長度、到期日和複雜度的密碼原則來管理。|Azure AD 使用適用于雲端和內部部署的智慧型 [密碼保護](../authentication/concept-password-ban-bad.md) 。 保護包括智慧型鎖定，以及封鎖一般和自訂的密碼片語和替代。 </br>Azure AD [透過多重要素驗證](../authentication/concept-mfa-howitworks.md) 和 [無密碼](../authentication/concept-authentication-passwordless.md) 技術（例如 FIDO2）大幅提高安全性。 </br>Azure AD 藉由提供使用者 [自助密碼重設](../authentication/concept-sspr-howitworks.md) 系統來降低支援成本。 |
| **應用程式**|||
| 基礎結構應用程式|Active Directory 形成許多基礎結構內部部署元件的基礎，例如 DNS、DHCP、IPSec、WiFi、NPS 和 VPN 存取|在新的雲端世界中，Azure AD 是用來存取應用程式，而不依賴網路控制的新控制平面。 當使用者進行驗證時[， (CA) 的條件式存取 ](../conditional-access/overview.md)，將會控制哪些使用者可以存取所需條件下的哪些應用程式。|
| 傳統和繼承應用程式| 大部分的內部部署應用程式會使用 LDAP、Windows-Integrated 驗證 (NTLM 和 Kerberos) ，或以標頭為基礎的驗證來控制使用者的存取權。| Azure AD 可以使用在內部部署環境中執行 [Azure AD 應用程式 proxy 代理程式](../manage-apps/application-proxy.md) ，來提供這些內部部署應用程式類型的存取權。 使用這個方法 Azure AD 可以在您遷移或需要與繼承應用程式並存時，使用 Kerberos 驗證內部部署 Active Directory 使用者。 |
| SaaS 應用程式|Active Directory 不會以原生方式支援 SaaS 應用程式，而且需要同盟系統，例如 AD FS。|支援 OAuth2、SAML 和 WS 驗證的 SaaS 應用程式 \* 可以整合以使用 Azure AD 進行驗證。 |
| 使用新式驗證的 LOB) 應用程式企業營運 (|組織可以搭配使用 AD FS 與 Active Directory 來支援需要新式驗證的 LOB 應用程式。| 需要新式驗證的 LOB 應用程式可以設定為使用 Azure AD 進行驗證。 |
| 中層/Daemon 服務|在內部部署環境中執行的服務通常會使用 AD 服務帳戶或群組受管理的服務帳戶 (gMSA) 執行。 然後，這些應用程式會繼承服務帳戶的許可權。| Azure AD 提供 [受控](../managed-identities-azure-resources/index.yml) 識別來執行雲端中的其他工作負載。 這些身分識別的生命週期是由 Azure AD 管理，並系結至資源提供者，因此無法用於其他用途以取得後門程式存取。|
| **裝置**|||
| 行動|Active Directory 原本就不支援沒有協力廠商解決方案的行動裝置。| Microsoft 的行動裝置管理解決方案（Microsoft Intune）已與 Azure AD 整合。 Microsoft Intune 會將裝置狀態資訊提供給識別系統，以便在驗證期間進行評估。 |
| Windows 桌上型電腦|Active Directory 可以讓 Windows 裝置加入網域，以使用群組原則、System Center Configuration Manager 或其他協力廠商解決方案來管理它們。|Windows 裝置可 [聯結至 Azure AD](../devices/index.yml)。 條件式存取可以檢查裝置是否在驗證過程中 Azure AD 加入。 您也可以使用 [Microsoft Intune](/intune/what-is-intune)來管理 Windows 裝置。 在此案例中，條件式存取會考慮裝置是否符合規範 (例如，最新的安全性修補程式和病毒碼) ，然後才允許存取應用程式。|
| Windows 伺服器| Active Directory 針對使用群組原則或其他管理解決方案的內部部署 Windows 伺服器，提供強大的管理功能。| 您可以使用 [Azure AD Domain Services](../../active-directory-domain-services/index.yml)管理 Azure 中的 Windows server 虛擬機器。 當 Vm 需要存取身分識別系統目錄或資源時，可以使用[受控](../managed-identities-azure-resources/index.yml)識別。|
| Linux/Unix 工作負載|雖然 Linux 機器可以設定為以 Kerberos 領域的 Active Directory 進行驗證，但 Active Directory 原本不支援非 Windows 的非 Windows 解決方案。|Linux/Unix Vm 可以使用 [受控](../managed-identities-azure-resources/index.yml) 識別來存取身分識別系統或資源。 某些組織會將這些工作負載遷移至雲端容器技術，也可以使用受控識別。|

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure Active Directory？](./active-directory-whatis.md)
- [比較自我管理 Active Directory Domain Services、Azure Active Directory 和受控 Azure Active Directory Domain Services](../../active-directory-domain-services/compare-identity-solutions.md)
- [關於 Azure Active Directory 的常見問題集](./active-directory-faq.md)
- [Azure Active Directory 的新功能](./whats-new.md)
