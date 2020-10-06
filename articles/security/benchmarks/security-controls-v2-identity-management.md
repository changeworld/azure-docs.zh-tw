---
title: Azure 安全性基準測試 V2-身分識別管理
description: Azure 安全性基準測試 V2 身分識別管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e7447d03fc231d8d96b71c7d944a380c10add0d0
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757931"
---
# <a name="security-control-v2-identity-management"></a>安全性控制 V2：身分識別管理

身分識別管理涵蓋的控制項可使用 Azure Active Directory 建立安全的身分識別和存取控制。 這包括使用單一登入、增強式驗證、受控識別 (和服務原則) 應用程式、條件式存取，以及帳戶異常監視。

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：將 Azure Active Directory 標準化為中央身分識別和驗證系統

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-1 | 16.1、16.2、16.4、16。5 | IA-2、IA-8、AC-2、AC-3 |

Azure Active Directory (Azure AD) 是 Azure 的預設身分識別和存取管理服務。 您應標準化 Azure AD，以管理組織的身分識別和存取管理：
- Microsoft 雲端資源，例如 Azure 入口網站、Azure 儲存體、Azure 虛擬機器 (Linux 和 Windows) 、Azure Key Vault、PaaS 和 SaaS 應用程式。

- 您組織的資源，例如 Azure 上的應用程式或公司網路資源。

保護 Azure AD 在貴組織的雲端安全性實務中應該是高優先順序。 Azure AD 提供身分識別安全分數，以協助您評估與 Microsoft 最佳作法建議相關的身分識別安全性狀態。 您可以使用分數來量測您的設定如何符合最佳做法建議，以及改善安全性狀態。

注意： Azure AD 支援外部識別提供者，讓沒有 Microsoft 帳戶的使用者可以使用其外部身分識別登入其應用程式和資源。

- [Azure AD 中的租使用者](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [如何建立和設定 Azure AD 實例](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [定義 Azure AD 租使用者](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [使用應用程式的外部識別提供者](/azure/active-directory/b2b/identity-providers)

- [Azure AD 中的身分識別安全分數為何](../../active-directory/fundamentals/identity-secure-score.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自動地管理應用程式身分識別

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-2 | N/A | AC-2、AC-3、IA-2、IA-4、IA-9 |

針對非人力帳戶（例如服務或自動化），請使用 Azure 受控識別，而不是建立功能更強大的人力帳戶來存取資源或執行程式碼。 Azure 受控識別可以向支援 Azure AD authentication 的 Azure 服務和資源進行驗證。 透過預先定義的存取授與規則來啟用驗證，以避免在原始程式碼或設定檔中使用硬式編碼的認證。 

針對不支援受控識別的服務，請使用 Azure AD，改為在資源層級建立具有限制許可權的服務主體。  建議您設定具有憑證認證的服務主體，並切換回用戶端密碼。 在這兩種情況下，Azure Key Vault 可以搭配使用 Azure 受控識別，讓執行時間環境 (例如 Azure function) 可以從金鑰保存庫取得認證。

- [Azure 受控識別](../../active-directory/managed-identities-azure-resources/overview.md)

- [支援適用於 Azure 資源的受控識別服務](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure 服務主體](/powershell/azure/create-azure-service-principal-azureps)

- [建立具有憑證的服務主體](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

使用 Azure Key Vault 進行安全性主體註冊：驗證 # 授權-a-安全性-主體到存取金鑰-保存庫

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 單一登入 (SSO) 來存取應用程式

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-3 | 4.4 | IA-2、IA-4 |

Azure AD 可提供 Azure 資源、雲端應用程式和內部部署應用程式的身分識別和存取管理。 身分識別和存取管理適用于企業身分識別，例如員工，以及外部身分識別，例如合作夥伴、廠商和供應商。

使用 Azure AD 單一登入 (SSO) 來管理及保護對內部部署和雲端中組織資料和資源的存取。 將您所有的使用者、應用程式和裝置連接到 Azure AD，以獲得順暢、安全的存取，以及更高的可見度和控制能力。 

- [瞭解 Azure AD 的應用程式 SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：針對所有以 Azure Active Directory 為基礎的存取，使用強式驗證控制項

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-4 | 4.2、4.4 4.5、11.5、12.11、16。3 | AC-2、AC-3、IA-2、IA-4 |

Azure AD 透過多重要素驗證 (MFA) 和強式無密碼方法來支援強式驗證控制項。  
- 多重要素驗證：啟用 Azure AD MFA，並遵循 MFA 設定的 Azure 資訊安全中心身分識別和存取管理建議。 MFA 可以根據登入條件和風險因素，在所有使用者、選取使用者或依使用者層級強制執行。 

- 無密碼 authentication：有三種可用的無密碼 authentication 選項： Windows Hello 企業版、Microsoft Authenticator 應用程式和內部部署驗證方法（例如智慧卡）。 

針對系統管理員和特殊許可權的使用者，請確定已使用強式驗證方法的最高層級，接著向其他使用者推出適當的增強式驗證原則。

如果舊版密碼驗證仍用於 Azure AD authentication，請注意，直接在) Azure 中建立的僅限雲端帳戶 (使用者帳戶，具有預設的基準密碼原則。 和混合式帳戶 (來自內部部署 Active Directory 的使用者帳戶) 遵循內部部署密碼原則。 使用密碼驗證時，Azure AD 提供密碼保護功能，防止使用者設定容易猜測的密碼。 Microsoft 提供以遙測為基礎的禁用密碼全域清單，客戶可以根據自己的需求來增強清單 (例如商標、文化參考等 ) 。 此密碼保護可用於僅限雲端和混合式帳戶。 

注意：以密碼認證為基礎的驗證，很容易受到常見攻擊方法的影響。 為了提高安全性，請使用強式驗證，例如 MFA 和強式密碼原則。 針對可能具有預設密碼的協力廠商應用程式和 marketplace 服務，您應該在初始服務設定期間變更它們。 

- [如何在 Azure 中啟用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Active Directory 的無密碼 authentication 選項簡介](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD 預設密碼原則](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [使用 Azure AD 密碼保護來消除錯誤的密碼](../../active-directory/authentication/concept-password-ban-bad.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：監視和警示帳戶異常

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-5 | 4.8、4.9、16.12、16.13 | AC-2、AC-3、AC-7、AU-6 |

Azure AD 提供下列資料來源： 
-   登入 – 登入報告會提供受控應用程式和使用者登入活動的使用情況相關資訊。

-   Audit logs-透過 Azure AD 中各種功能所進行的所有變更，為記錄提供可追蹤性。 記錄變更審核記錄的範例包括新增或移除使用者、應用程式、群組、角色和原則。

-   有風險的登入 - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。

-   標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

這些資料來源可以與 Azure 監視器、Azure Sentinel 或協力廠商 SIEM 系統整合。

Azure 資訊安全中心也可能會對某些可疑活動發出警示，例如驗證嘗試失敗的次數過多，以及訂用帳戶中已淘汰的帳戶。 

Azure 進階威脅防護 (ATP) 是一種安全性解決方案，可使用內部部署 Active Directory 信號來識別、偵測及調查 Advanced 威脅、遭盜用的身分識別，以及惡意的內部動作。

- [Azure AD 中的審核活動報告](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何檢視有風險的 Azure AD 登入](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何識別已標示為有風險活動的 Azure AD 使用者](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../../security-center/security-center-identity-access.md)

- [Azure 資訊安全中心的威脅情報保護模組中的警示](/azure/security-center/alerts-reference)

- [如何將 Azure 活動記錄整合到 Azure 監視器](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [從 Azure AD Identity Protection 連接資料](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure 進階威脅防護](/azure-advanced-threat-protection/what-is-atp)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：根據條件限制 Azure 資源存取

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-6 | N/A | AC-2、AC-3 |

根據使用者定義的條件，使用 Azure AD 條件式存取來進行更細微的存取控制，例如要求特定 IP 範圍的使用者登入使用 MFA。 您也可以在不同的使用案例中，透過 Azure AD 條件式存取原則來使用細微的驗證會話管理。 

- [Azure 條件式存取總覽](../../active-directory/conditional-access/overview.md)

- [一般條件式存取原則](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [使用條件式存取來設定驗證工作階段管理](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除非預期的認證暴露

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-7 | 18.1、18。7 | IA-5 |

執行 Azure DevOps 認證掃描器來識別程式碼中的認證。 認證掃描器也鼓勵將探索到的認證移至更安全的位置，例如 Azure Key Vault。

在 GitHub 中，您可以使用原生密碼掃描功能來識別程式碼中的認證或其他形式的秘密。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub 秘密掃描](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8：安全地存取繼承應用程式的使用者

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-8 | 14.6 | AC-2、AC-3、SC-11 |

確定您具有適用于繼承應用程式的新式存取控制和會話監視，以及它們所儲存和處理的資料。 雖然 Vpn 通常是用來存取繼承應用程式，但它們通常只會有基本的存取控制和有限的會話監視。

Azure AD 應用程式 Proxy 可讓您將舊版內部部署應用程式發佈至具有單一登入 (SSO) 的遠端使用者，同時以 Azure AD 條件式存取明確驗證遠端使用者和裝置的可信度。 

此外，Microsoft Cloud App Security 是一種雲端存取安全性代理程式 (CASB) 服務，可提供控制項來監視使用者的應用程式會話，以及針對舊版內部部署應用程式和雲端軟體即服務 () SaaS) 應用程式的封鎖 (動作。 

- [Azure AD 應用程式 Proxy](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Microsoft Cloud App Security 最佳作法](/cloud-app-security/best-practices)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
