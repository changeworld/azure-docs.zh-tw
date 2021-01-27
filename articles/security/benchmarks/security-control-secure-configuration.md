---
title: Azure 安全性控制-安全設定
description: Azure 安全性控制安全設定
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f945699256b4de9bd87ee4ab7500268302e93089
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880164"
---
# <a name="security-control-secure-configuration"></a>安全性控制：安全設定

建立、執行及主動管理 (的追蹤、報告、正確) Azure 資源的安全性設定，以防止攻擊者利用易受攻擊的服務和設定。

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.1 | 5.1 | 客戶 |

使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的設定進行審核或強制執行。 您也可以使用內建的 Azure 原則定義。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，您應該檢查這些設定，以確保設定符合/超過組織的安全性需求。

您也可以使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。

- [如何查看可用 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教學課程：建立和管理原則來強制執行相容性](../../governance/policy/tutorials/create-and-manage.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../../azure-resource-manager/templates/export-template-portal.md)

- [安全性建議 - 參考指南](../../security-center/recommendations-reference.md)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.2 | 5.1 | 客戶 |

使用 Azure 資訊安全中心建議來維護所有計算資源上的安全性設定。  此外，您也可以使用自訂的作業系統映射或 Azure 自動化狀態設定，來建立您組織所需作業系統的安全性設定。

- [如何監視 Azure 資訊安全中心建議](../../security-center/security-center-recommendations.md)

- [安全性建議 - 參考指南](../../security-center/recommendations-reference.md)

- [Azure 自動化狀態設定總覽](../../automation/automation-dsc-overview.md)

- [上傳 VHD 並使用它在 Azure 中建立新的 Windows Vm](../../virtual-machines/windows/upload-generalized-managed.md)

- [使用 Azure CLI 從自訂磁碟建立 Linux VM](../../virtual-machines/linux/upload-vhd.md)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.3 | 5.2 | 客戶 |

使用 Azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 資源上強制執行安全設定。  此外，您可以使用 Azure Resource Manager 範本，維護您的組織所需的 Azure 資源安全性設定。 

- [瞭解 Azure 原則效果](../../governance/policy/concepts/effects.md)

- [建立和管理原則以強制執行合規性](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 範本總覽](../../azure-resource-manager/templates/overview.md)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.4 | 5.2 | 共用 |

遵循 Azure 資訊安全中心在您的 Azure 計算資源上執行弱點評定的建議。  此外，您也可以使用 Azure Resource Manager 範本、自訂作業系統映射或 Azure 自動化狀態設定來維護組織所需作業系統的安全性設定。   與 Azure 自動化 Desired State Configuration 結合的 Microsoft 虛擬機器範本，可能有助於符合和維護安全性需求。 

另外也請注意，microsoft 發佈的 Azure Marketplace 虛擬機器映射是由 Microsoft 所管理和維護。 

- [如何實行 Azure 資訊安全中心弱點評定建議](../../security-center/deploy-vulnerability-assessment-vm.md)

- [如何從 Azure Resource Manager 範本建立 Azure 虛擬機器](../../virtual-machines/windows/ps-template.md)

- [Azure 自動化狀態設定總覽](../../automation/automation-dsc-overview.md)

- [在 Azure 入口網站中建立 Windows 虛擬機器](../../virtual-machines/windows/quick-create-portal.md)

- [如何下載 VM 範本的資訊](/previous-versions/azure/virtual-machines/windows/download-template)

- [將 VHD 上傳至 Azure 並新建 VM 的範例指令碼](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.5 | 5.3 | 客戶 |

使用 Azure DevOps 來安全地儲存和管理您的程式碼，例如自訂 Azure 原則、Azure Resource Manager 範本和 Desired State Configuration 腳本。 若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 TFS 整合的 Active Directory。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.6 | 5.3 | 客戶 |

如果使用自訂映射，請使用 Azure 角色型存取控制 (Azure RBAC) ，以確保只有獲得授權的使用者可以存取影像。 使用共用映像庫，即可在組織內對於不同的使用者、服務主體或 AD 群組共用您的映像。  針對容器映射，請將它們儲存在 Azure Container Registry 中，並利用 Azure RBAC 來確保只有授權的使用者可以存取影像。  

- [了解 Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [瞭解適用于 Container Registry 的 Azure RBAC](../../container-registry/container-registry-roles.md)

- [如何設定 Azure RBAC](../../role-based-access-control/quickstart-assign-role-user-portal.md)

- [共用映射庫總覽](../../virtual-machines/shared-image-galleries.md)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.7 | 5.4 | 客戶 |

使用 Azure 原則定義和實行 Azure 資源的標準安全性設定。 使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的網路設定進行審核或強制執行。 您也可以使用與特定資源相關的內建原則定義。  此外，您可以使用 Azure 自動化來部署設定變更。

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用別名](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.8 | 5.4 | 客戶 |

Azure 自動化狀態設定是在任何雲端或內部部署資料中心內 Desired State Configuration (DSC) 節點的設定管理服務。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。 

- [將機器上架交由 Azure Automation State Configuration 管理](../../automation/automation-dsc-onboarding.md)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.9 | 5.5 | 客戶 |

使用 Azure 資訊安全中心來執行 Azure 資源的基準掃描。  此外，請使用 Azure 原則來警示和審核 Azure 資源設定。

- [如何修復 Azure 資訊安全中心中的建議](../../security-center/security-center-remediate-recommendations.md)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.10 | 5.5 | 客戶 |

使用 Azure 資訊安全中心對容器的 OS 和 Docker 設定執行基準掃描。

- [了解 Azure 資訊安全中心容器建議](../../security-center/container-security.md)

## <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.11 | 13.1 | 客戶 |

使用受控服務識別搭配 Azure Key Vault，以簡化和保護雲端應用程式的秘密管理。

- [如何與 Azure 受控識別整合](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [如何建立 Key Vault](../../key-vault/secrets/quick-create-portal.md)

- [如何驗證 Key Vault](../../key-vault/general/authentication.md)

- [如何指派 Key Vault 存取原則](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.12 | 4.1 | 客戶 |

使用受控識別，在 Azure AD 中為 Azure 服務提供自動管理的身分識別。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

- [如何設定受控識別](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.13 | 18.1、18。7 | 客戶 |

執行認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制：  [惡意程式碼防禦](security-control-malware-defense.md)