---
title: Azure 安全控制 - 標識和存取控制
description: 安全控制標識和存取控制
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 543573610c2ea3ab0bcd89e1b8f4ee5f5a34dbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934438"
---
# <a name="security-control-identity-and-access-control"></a>安全控制：身份和存取控制

身份和訪問管理建議側重于解決與基於身份的存取控制相關的問題、鎖定管理訪問、對與身份相關的事件發出警報、異常帳戶行為和基於角色的存取控制。

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 維持管理帳戶的清單

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.1 | 4.1 | 客戶 |

Azure AD 具有必須顯式分配且可查詢的內置角色。 使用 Azure AD PowerShell 模組執行臨時查詢以發現屬於管理組成員的帳戶。

如何使用 PowerShell 在 Azure AD 中獲取目錄角色：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 獲取 Azure AD 中的目錄角色成員：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3.2： 在適用的情況下更改預設密碼

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.2 | 4.2 | 客戶 |

Azure AD 沒有預設密碼的概念。 需要密碼的其他 Azure 資源強制創建具有複雜性要求和最小密碼長度的密碼，具體取決於服務。 您負責可能使用預設密碼的協力廠商應用程式和市場服務。

## <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用專用管理帳戶

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.3 | 4.3 | 客戶 |

圍繞使用專用管理帳戶創建標準操作程式。 使用 Azure 安全中心標識和訪問管理監視管理帳戶數。

還可以通過使用 Microsoft 服務和 Azure 資源管理器的 Azure AD 特權標識管理特權角色啟用"及時/剛剛足夠"訪問。 

瞭解更多資訊：https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活動目錄的單一登入 （SSO）

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.4 | 4.4 | 客戶 |

在可能的情況下，使用 Azure 活動目錄 SSO 而不是配置每個服務的各個獨立憑據。 使用 Azure 安全中心標識和訪問管理建議。

使用 Azure AD 瞭解 SSO：

https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：對所有基於 Azure 活動目錄的訪問使用多重要素驗證

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.5 | 4.5, 11.5, 12.11, 16.3 | 客戶 |

啟用 Azure AD MFA 並遵循 Azure 安全中心標識和訪問管理建議。

如何在 Azure 中啟用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心內監視標識和訪問：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 對所有管理工作使用專用電腦（特權訪問工作站）

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | 客戶 |

使用配置為登錄和配置 Azure 資源的 MFA 的 PAW（特權訪問工作站）。

瞭解特權訪問工作站：

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 從管理客戶紀錄和警報可疑活動

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.7 | 4.8, 4.9 | 客戶 |

當環境中發生可疑或不安全活動時，請使用 Azure Active Directory 安全報告生成日誌和警報。 使用 Azure 安全中心監視標識和訪問活動。

如何識別標記為有風險活動的 Azure AD 使用者：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何監視 Azure 安全中心中的使用者身份和訪問活動：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從已批准的位置管理 Azure 資源

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.8 | 11.7 | 客戶 |

使用條件訪問命名位置僅允許從 IP 位址範圍或國家/地區的特定邏輯分組進行訪問。

如何在 Azure 中配置命名位置：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活動目錄

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | 客戶 |

使用 Azure 活動目錄 （AAD） 作為中央身份驗證和授權系統。 AAD 對靜態和傳輸中的資料使用增強式加密來保護資料。 AAD 還對使用者憑據進行鹽漬、雜湊和安全地存儲。

如何創建和配置 AAD 實例：

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期審查和協調使用者訪問

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.10 | 16.9, 16.10 | 客戶 |

Azure AD 提供日誌以説明發現陳舊的帳戶。 此外，使用 Azure 標識訪問審核可高效地管理組成員身份、對企業應用程式的訪問和角色指派。 可以定期查看使用者存取權限，以確保只有正確的使用者才能繼續訪問。 

Azure 廣告報告：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 標識訪問審核：

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 監視訪問已停用帳戶的嘗試

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.11 | 16.12 | 客戶 |

您可以訪問 Azure AD 登錄活動、審核和風險事件日誌源，該源允許您與任何 SIEM/監視工具集成。

您可以通過為 Azure 活動目錄使用者帳戶創建診斷設置並將稽核記錄和登錄日誌發送到日誌分析工作區來簡化此過程。 您可以在日誌分析工作區中配置所需的警報。

如何將 Azure 活動日誌集成到 Azure 監視器中：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帳戶登錄行為偏差警報

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.12 | 16.13 | 客戶 |

使用 Azure AD 風險和標識保護功能配置自動回應，以檢測與使用者身份相關的可疑操作。 您還可以將資料引入 Azure 哨兵以進行進一步調查。

如何查看 Azure AD 風險登錄：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和啟用身份保護風險策略：

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何登上 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支援方案期間向 Microsoft 提供對相關客戶資料的訪問

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 3.13 | 16 | 客戶 |

在 Microsoft 需要訪問客戶資料的支援方案中，客戶密碼箱提供了一個介面，供您查看、批准或拒絕客戶資料訪問請求。

瞭解客戶密碼箱：

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>後續步驟

請參閱下一個安全控制：[資料保護](security-control-data-protection.md)
