---
title: Azure 安全性控制 ─ 安全性設定
description: Azure 安全控制安全設定
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: bfa16f59155e420209ab6370056a7c612b5327e4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759051"
---
# <a name="security-control-secure-configuration"></a>安全性控制:安全設定

建立、實施並積極管理 Azure 資源的安全配置(跟蹤、報告、更正),以防止攻擊者利用易受攻擊的服務和設置。

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:為所有 Azure 資源建立安全配置

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.1 | 5.1 | 客戶 |

使用 Azure 策略別名創建自訂策略以審核或強制執行 Azure 資源的配置。 您還可以使用內置的 Azure 策略定義。

此外,Azure 資源管理員能夠匯出 JavaScript 物件符號 (JSON) 中的範本,應對其進行審查,以確保配置滿足/超過組織的安全要求。

您還可以使用 Azure 安全中心的建議作為 Azure 資源的安全配置基線。

- [如何檢視可用的 Azure 政策別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教學:建立和管理原則以強制實施合規性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [單資源和多資源匯出到 Azure 門戶中的範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [安全性建議 - 參考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: 建立安全的作業系統設定

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.2 | 5.1 | 客戶 |

使用 Azure 安全中心建議維護所有計算資源的安全配置。  此外,您可以使用自訂作業系統映射或 Azure 自動化狀態配置來建立組織所需的作業系統的安全配置。

- [如何監視 Azure 安全中心建議](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [安全性建議 - 參考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Azure Automation State Configuration 概觀](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [上傳 VHD 並使用它在 Azure 中建立新的 Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [使用 Azure CLI 從自訂磁碟建立 Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:維護安全的 Azure 資源配置

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.3 | 5.2 | 客戶 |

使用 Azure 策略 [拒絕] 和 [部署(如果不存在)在 Azure 資源中強制實施安全設置。  此外,可以使用 Azure 資源管理器範本來維護組織所需的 Azure 資源的安全配置。 

- [了解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [建立和管理原則以強制實施合規性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 資源管理員樣本概述](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 維護安全的作業系統設定

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.4 | 5.2 | 共用 |

按照 Azure 安全中心的建議對 Azure 計算資源執行漏洞評估。  此外,您可以使用 Azure 資源管理器範本、自訂作業系統映射或 Azure 自動化狀態配置來維護組織所需的作業系統的安全配置。   Microsoft 虛擬機器樣本與 Azure 自動化所需狀態配置相結合,可能有助於滿足和維護安全要求。 

此外,請注意,由 Microsoft 發佈的 Azure 應用商店虛擬機映射由 Microsoft 管理和維護。 

- [如何實施 Azure 安全中心漏洞評估建議](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [如何從 Azure 資源管理員樣本建立 Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Azure Automation State Configuration 概觀](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [在 Azure 入口網站中建立 Windows 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [有關如何下載 VM 樣本的資訊](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [將 VHD 上傳至 Azure 並新建 VM 的範例指令碼](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: 安全儲存 Azure 資源的設定

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.5 | 5.3 | 客戶 |

使用 Azure DevOps 安全地儲存和管理代碼,如自訂 Azure 策略、Azure 資源管理員範本和所需的狀態配置腳稿。 要造訪在 Azure DevOps 中管理的資源,可以授予或拒絕特定使用者、內置安全組或與 Azure DevOps 整合或在 Azure 活動目錄(如果與 TFS 整合)中定義的組的許可權。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 的權限和群組](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: 安全地儲存自訂作業系統映像

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.6 | 5.3 | 客戶 |

如果使用自定義映射,請使用基於角色的存取控制 (RBAC) 來確保只有經過授權的使用者才能存取映射。 使用共用映像庫，即可在組織內對於不同的使用者、服務主體或 AD 群組共用您的映像。  對於容器映射,將它們存儲在 Azure 容器註冊表中,並利用 RBAC 確保只有經過授權的使用者才能訪問映射。  

- [瞭解 Azure 中的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [瞭解容器註冊表的 RBAC](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [如何在 Azure 中設定 RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [共用映像資源庫概觀](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:為 Azure 資源部署設定管理工具

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.7 | 5.4 | 客戶 |

使用 Azure 策略定義和實現 Azure 資源的標準安全配置。 使用 Azure 策略別名創建自訂策略以審核或強制執行 Azure 資源的網路配置。 您還可以使用與您的特定資源相關的內置策略定義。  此外,您可以使用 Azure 自動化來部署配置更改。

- [如何設定與管理 Azure 政策](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用別名](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 為作業系統部署設定管理工具

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.8 | 5.4 | 客戶 |

Azure 自動化狀態配置是任何雲端或本地資料中心中所需狀態配置 (DSC) 節點的配置管理服務。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。 

- [將機器上架交由 Azure Automation State Configuration 管理](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:對 Azure 資源實施自動配置監視

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.9 | 5.5 | 客戶 |

使用 Azure 安全中心對 Azure 資源執行基線掃描。  此外,使用 Azure 策略來警報和審核 Azure 資源配置。

- [如何修復 Azure 安全中心中的建議](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 對作業系統執行自動設定監控

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.10 | 5.5 | 客戶 |

使用 Azure 安全中心對容器的作業系統和 Docker 設置執行基線掃描。

- [了解 Azure 資訊安全中心容器建議](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: 安全管理 Azure 機密

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.11 | 13.1 | 客戶 |

將託管服務標識與 Azure 密鑰保管庫結合使用,以簡化和保護雲應用程式的秘密管理。

- [如何與 Azure 託管識別整合](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [如何建立金鑰保存庫](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [如何使用託管識別提供金鑰保管庫身份驗證](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: 安全、自動地管理身份

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.12 | 4.1 | 客戶 |

使用託管識別在 Azure AD 中為 Azure 服務提供自動託管標識。 託管標識允許您對支援 Azure AD 身份驗證的任何服務(包括密鑰保管庫)進行身份驗證,而無需在代碼中進行任何認證。

- [如何設定託管識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 消除意外的認證

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.13 | 18.1, 18.7 | 客戶 |

實現憑據掃描程式以標識代碼中的憑據。 憑據掃描程式還將鼓勵將發現的憑據移動到更安全的位置,如 Azure 密鑰保管庫。 

- [如何設定認證程式](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全控制:[惡意軟體防禦](security-control-malware-defense.md)