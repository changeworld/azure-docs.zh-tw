---
title: Azure 安全性基準測試 V2-資產管理
description: Azure 安全性基準測試 V2 資產管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 845c4f3194f35c68306e74734e5ecc6109d9c103
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318019"
---
# <a name="security-control-v2-asset-management"></a>安全性控制 V2：資產管理

資產管理涵蓋的控制項可確保 Azure 資源的安全性可見度和治理。 這包括安全性人員的許可權建議、資產清查的安全性存取，以及管理服務和資源的核准 (清查、追蹤和正確的) 。

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>上午-1：確保安全性小組能看到資產的風險

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-1 | 1.1、1。2 | CM-8，下午5點 |

請確定安全性小組已獲得 Azure 租使用者和訂用帳戶中的安全性讀取者許可權，讓他們可以使用 Azure 資訊安全中心監視安全性風險。 

根據安全性小組責任的結構，監視安全性風險可能是中央安全性小組或本地小組的責任。 話雖如此，安全性見解和風險一律必須在組織內集中匯總。 

安全性讀取者許可權可以廣泛地套用到整個租使用者， (根管理群組) 或限定于管理群組或特定訂用帳戶。 

注意：若要瞭解工作負載和服務，可能需要其他許可權。 

- [安全性讀取者角色總覽](../../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理群組總覽](../../governance/management-groups/overview.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>上午-2：確保安全性小組可以存取資產清查和中繼資料

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-2 | 1.1、1.2、1.4、1.5、9.1、12。1 | CM-8，下午5點 |

請確定安全性小組可以存取 Azure 上不斷更新的資產清查。 安全性小組通常需要此清查來評估其組織對新興風險的潛在風險，並做為持續安全性改進的輸入。 

Azure 資訊安全中心清查功能和 Azure Resource Graph 可以查詢及探索訂用帳戶中的所有資源，包括 Azure 服務、應用程式和網路資源。  

使用標記以及 Azure 中的其他中繼資料，以邏輯方式組織資產， (名稱、描述和類別) 。  

- [如何使用 Azure Resource Graph Explorer 建立查詢](../../governance/resource-graph/first-query-portal.md)

- [Azure 資訊安全中心資產庫存管理](../../security-center/asset-inventory.md)

- [如需標記資產的詳細資訊，請參閱資源命名和標記決策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>上午-3：僅使用已核准的 Azure 服務

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-3 | 2.3、2.4 | CM-7、CM-8 |

使用 Azure 原則來審核和限制使用者可以在您的環境中布建的服務。 使用 Azure Resource Graph 來查詢及探索其訂用帳戶內的資源。  您也可以使用 Azure 監視器來建立規則，以在偵測到未核准的服務時觸發警示。

- [設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](/azure/governance/policy/samples/not-allowed-resource-types)

- [如何使用 Azure Resource Graph Explorer 建立查詢](../../governance/resource-graph/first-query-portal.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>上午-4：確保資產生命週期管理的安全性

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-4 | 2.3、2.4、2。5 | CM-7、CM-8、CM-10、CM-11 |

建立或更新安全性原則，以處理可能有高影響的修改資產生命週期管理程式。 這些修改包括以下的變更：身分識別提供者和存取、資料敏感度、網路設定和系統管理許可權指派。

當不再需要 Azure 資源時，請將其移除。

- [刪除 Azure 資源群組和資源](../../azure-resource-manager/management/delete-resource-group.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>上午-5：限制使用者與 Azure Resource Manager 互動的能力

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-5 | 2.9 | AC-3 |

使用 Azure AD 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../../role-based-access-control/conditional-access-azure-management.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>上午-6：僅在計算資源中使用已核准的應用程式

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-6 | 2.6、2。7 | AC-3，CM-7，CM-8，CM-10，CM-11 |

確定只有授權的軟體會執行，而且所有未經授權的軟體都會在 Azure 虛擬機器上遭到封鎖而無法執行。

使用 Azure 資訊安全中心 (ASC) 適應性應用程式控制，以探索並產生應用程式允許清單。 您也可以使用 ASC 調適型應用程式控制，以確保只有授權的軟體會執行，而且所有未經授權的軟體都會在 Azure 虛擬機器上遭到封鎖而無法執行。

使用 Azure 自動化變更追蹤和清查，從您的 Windows 和 Linux Vm 將清查資訊的收集自動化。 您可以從 Azure 入口網站取得軟體名稱、版本、發行者和重新整理時間。 若要取得軟體安裝日期和其他資訊，請啟用來賓層級的診斷，並將 Windows 事件記錄檔導向至 Log Analytics 工作區。

根據腳本的類型而定，您可以使用作業系統專屬的設定或協力廠商資源，以限制使用者在 Azure 計算資源中執行腳本的能力。 

您也可以使用協力廠商解決方案來探索及識別未核准的軟體。

- [如何使用 Azure 資訊安全中心適應性應用程式控制](../../security-center/security-center-adaptive-application.md)

- [瞭解 Azure 自動化變更追蹤和清查](../../automation/change-tracking.md)

- [如何在 Windows 環境中控制 PowerShell 腳本執行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

