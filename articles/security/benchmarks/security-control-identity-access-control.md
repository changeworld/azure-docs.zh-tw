---
title: Azure 安全性控制 - 識別和存取控制
description: Azure 安全控制識別和存取控制
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 473fe838f53df0a874a2c793792533e01b7c069a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408521"
---
# <a name="security-control-identity-and-access-control"></a>安全控制:身份及存取控制

身份和訪問管理建議側重於解決與基於身份的訪問控制相關的問題、鎖定管理訪問、對與身份相關的事件發出警報、異常帳戶行為和基於角色的訪問控制。

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 維持管理帳戶的清單

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.1 | 4.1 | 客戶 |

Azure AD 具有必須顯式分配且可查詢的內建角色。 使用 Azure AD PowerShell 模組執行臨時查詢以發現屬於管理組成員的帳戶。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 取得 Azure AD 中目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

## <a name="32-change-default-passwords-where-applicable"></a>3.2: 在適用的情況下變更預設密碼

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.2 | 4.2 | 客戶 |

Azure AD 沒有預設密碼的概念。 需要密碼的其他 Azure 資源強制創建具有複雜性要求和最小密碼長度的密碼,具體取決於服務。 您負責可能使用預設密碼的第三方應用程式和市場服務。

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: 使用專用管理帳戶

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.3 | 4.3 | 客戶 |

圍繞使用專用管理帳戶創建標準操作程式。 使用 Azure 安全中心識別和訪問管理監視管理帳戶數。

還可以通過使用 Microsoft 服務和 Azure 資源管理員的 Azure AD 特權標識管理特權角色啟用" 及時 / 剛剛足夠 " 訪問。 

- [瞭解有關權識別管理的更多資訊](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: 使用 Azure 的目錄的單一登入 (SSO)

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.4 | 4.4 | 客戶 |

在可能的情況下,使用 Azure 活動目錄 SSO 而不是配置每個服務的各個獨立認證。 使用 Azure 安全中心標識和存取管理建議。

- [使用 Azure AD 瞭解 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5:對所有基於 Azure 活動目錄的存取使用多重身份驗證

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.5 | 4.5, 11.5, 12.11, 16.3 | 客戶 |

啟用 Azure AD MFA 並遵循 Azure 安全中心識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [如何在 Azure 安全中心內監視標識和訪問](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 對所有管理工作使用專用電腦 (特權存取工作站)

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | 客戶 |

使用配置為登錄和配置 Azure 資源的 MFA 的 PAW(特權存取工作站)。

- [瞭解特權存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 從管理帳戶記錄和警報可疑活動

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.7 | 4.8, 4.9 | 客戶 |

當環境中發生可疑或不安全活動時,請使用 Azure Active Directory 安全報告生成日誌和警報。 使用 Azure 安全中心監視標識和訪問活動。

- [如何辨識標記為有風險活動的 Azure AD 使用者](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何監控 Azure 安全中心中的使用者身份和存取活動](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:僅從已批准的位置管理 Azure 資源

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.8 | 11.7 | 客戶 |

使用條件訪問命名位置僅允許從IP位址範圍或國家/地區的特定邏輯分組進行訪問。

- [如何在 Azure 中設定命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

## <a name="39-use-azure-active-directory"></a>3.9: 使用 Azure 的項目

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | 客戶 |

使用 Azure 活動目錄 (AAD) 作為中央身份驗證和授權系統。 AAD 對靜態和傳輸中的數據使用強加密來保護數據。 AAD 還對使用者憑據進行鹽漬、哈希和安全地存儲。

- [如何建立與設定 AAD 實體](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 定期審查和協調使用者存取

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.10 | 16.9, 16.10 | 客戶 |

Azure AD 提供日誌以幫助發現陳舊的帳戶。 此外,使用 Azure 標識存取審核可高效地管理組成員身份、對企業應用程式的訪問和角色分配。 可以定期查看使用者訪問許可權,以確保只有正確的使用者才能繼續訪問。 

- [瞭解 Azure AD 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 識別存取稽核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 監視訪問已停用認證的嘗試

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.11 | 16.12 | 客戶 |

您可以造訪 Azure AD 登入活動、審核和風險事件日誌源,該源允許您與任何 SIEM/監視工具集成。

您可以通過為 Azure 活動目錄使用者帳戶創建診斷設定並將審核日誌和登錄日誌發送到日誌分析工作區來簡化此過程。 您可以在日誌分析工作區中配置所需的警報。

- [如何將 Azure 活動紀錄整合到 Azure 監視器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 帳戶登錄行為偏差警報

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.12 | 16.13 | 客戶 |

使用 Azure AD 風險和標識保護功能配置自動回應,以檢測與使用者身份相關的可疑操作。 您還可以將數據引入 Azure 哨兵以進行進一步調查。

- [如何檢視 Azure AD 風險登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定與啟用身份保護風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [如何登上 Azure 哨兵](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 在支援方案期間向 Microsoft 提供對相關客戶資料的存取

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.13 | 16 | 客戶 |

在 Microsoft 需要存取客戶資料的支援方案中,客戶密碼箱提供了一個介面,供您查看、批准或拒絕客戶數據訪問請求。

- [瞭解客戶密碼箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)


## <a name="next-steps"></a>後續步驟

- 請參考下一個安全控制:[資料保護](security-control-data-protection.md)