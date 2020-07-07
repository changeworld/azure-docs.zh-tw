---
title: Azure 安全性控制-身分識別和存取控制
description: Azure 安全性控制身分識別和存取控制
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 473fe838f53df0a874a2c793792533e01b7c069a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408521"
---
# <a name="security-control-identity-and-access-control"></a>安全性控制：身分識別和存取控制

身分識別和存取管理的建議著重于解決與身分識別型存取控制相關的問題、鎖定系統管理存取、對身分識別相關事件發出警示、異常帳戶行為，以及角色型存取控制。

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.1 | 4.1 | 客戶 |

Azure AD 具有必須明確指派且可供查詢的內建角色。 使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

- [如何使用 PowerShell 取得 Azure AD 中的目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

## <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.2 | 4.2 | 客戶 |

Azure AD 沒有預設密碼的概念。 需要密碼的其他 Azure 資源會強制以複雜性需求和最小密碼長度來建立密碼，這會因服務而有所不同。 您必須負責可使用預設密碼的協力廠商應用程式和 marketplace 服務。

## <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.3 | 4.3 | 客戶 |

建立使用專用系統管理帳戶的標準操作程式。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

您也可以使用 Microsoft 服務 Azure AD Privileged Identity Management 特殊許可權角色，並 Azure Resource Manager，啟用僅限時間/僅供存取。 

- [深入瞭解 Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.4 | 4.4 | 客戶 |

可能的話，請使用 Azure Active Directory SSO，而不是針對每個服務設定個別的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。

- [瞭解使用 Azure AD 的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.5 | 4.5、11.5、12.11、16。3 | 客戶 |

啟用 Azure AD MFA，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [如何在 Azure 資訊安全中心監視身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.6 | 4.6、11.6、12.12 | 客戶 |

使用已設定 MFA 的 Paw （特殊許可權存取工作站）來登入和設定 Azure 資源。

- [瞭解特殊權限存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄及警示可疑活動

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.7 | 4.8、4。9 | 客戶 |

當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory 的安全性報告來產生記錄檔和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

- [如何識別已標示為有風險活動的 Azure AD 使用者](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.8 | 11.7 | 客戶 |

使用名為「位置」的條件式存取，只允許來自 IP 位址範圍或國家/地區之特定邏輯群組的存取權。

- [如何在 Azure 中設定具名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

## <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.9 | 16.1、16.2、16.4、16.5、16。6 | 客戶 |

使用 Azure Active Directory （AAD）做為中央驗證和授權系統。 AAD 會對待用和傳輸中資料使用強式加密以保護資料。 AAD 也會 salts、雜湊並安全地儲存使用者認證。

- [如何建立和設定 AAD 執行個體](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.10 | 16.9、16.10 | 客戶 |

Azure AD 提供記錄檔，以協助探索過時的帳戶。 此外，您可以使用 Azure 身分識別存取審查來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。 

- [瞭解 Azure AD 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 身分識別存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.11 | 16.12 | 客戶 |

您可以存取 Azure AD 登入活動、Audit 和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。

若要簡化此流程，您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將稽核記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定所需的警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.12 | 16.13 | 客戶 |

使用 Azure AD 風險和身分識別保護功能，為偵測到與使用者身分識別相關的可疑動作設定自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

- [如何檢視有風險的 Azure AD 登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定和啟用身分識別保護風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 3.13 | 16 | 客戶 |

在 Microsoft 需要存取客戶資料的支援案例中，客戶加密箱會提供介面供您審查，並核准或拒絕客戶資料存取要求。

- [瞭解客戶加密箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制：[資料保護](security-control-data-protection.md)