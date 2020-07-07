---
title: Azure 安全性控制-安全設定
description: Azure 安全性控制安全設定
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e3d43239dabe75bc2b25319945c2c6b08d726d2a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82193100"
---
# <a name="security-control-secure-configuration"></a>安全性控制：安全設定

建立、執行及主動管理（追蹤、報告、更正） Azure 資源的安全性設定，以防止攻擊者利用易受攻擊的服務和設定。

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.1 | 5.1 | 客戶 |

使用 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure 資源的設定。 您也可以使用內建的 Azure 原則定義。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法（JSON）中匯出範本，這應該進行檢查以確保設定符合/超過組織的安全性需求。

您也可以使用來自 Azure 資訊安全中心的建議，做為 Azure 資源的安全設定基準。

- [如何查看可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教學課程：建立和管理原則來強制執行相容性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [單一和多重資源匯出至 Azure 入口網站中的範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [安全性建議 - 參考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.2 | 5.1 | 客戶 |

使用 Azure 資訊安全中心建議來維護所有計算資源的安全性設定。  此外，您可以使用自訂作業系統映射或 Azure 自動化狀態設定，來建立組織所需作業系統的安全性設定。

- [如何監視 Azure 資訊安全中心建議](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [安全性建議 - 參考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Azure 自動化狀態設定總覽](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [上傳 VHD 並使用它在 Azure 中建立新的 Windows Vm](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [使用 Azure CLI 從自訂磁碟建立 Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.3 | 5.2 | 客戶 |

使用 Azure 原則 [拒絕] 和 [部署（如果不存在）]，在您的 Azure 資源上強制執行安全設定。  此外，您可以使用 Azure Resource Manager 範本來維護貴組織所需之 Azure 資源的安全性設定。 

- [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [建立和管理原則以強制執行合規性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Resource Manager 範本總覽](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.4 | 5.2 | 共用 |

請遵循在 Azure 計算資源上執行弱點評定 Azure 資訊安全中心中的建議。  此外，您可以使用 Azure Resource Manager 範本、自訂作業系統映射或 Azure 自動化狀態設定，以維護貴組織所需作業系統的安全性設定。   與 Azure 自動化 Desired State Configuration 結合的 Microsoft 虛擬機器範本可能有助於滿足和維護安全性需求。 

另請注意，Microsoft 所發佈的 Azure Marketplace 虛擬機器映射是由 Microsoft 所管理和維護。 

- [如何實行 Azure 資訊安全中心弱點評估建議](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [如何從 Azure Resource Manager 範本建立 Azure 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Azure 自動化狀態設定總覽](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [在 Azure 入口網站中建立 Windows 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [如何下載 VM 範本的資訊](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [將 VHD 上傳至 Azure 並新建 VM 的範例指令碼](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.5 | 5.3 | 客戶 |

使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂的 Azure 原則、Azure Resource Manager 範本和 Desired State Configuration 腳本。 若要存取您在 Azure DevOps 中管理的資源，您可以將許可權授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory （Azure AD）中定義的群組（如果與 Azure DevOps 整合），或 Active Directory （如果與 TFS 整合）。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.6 | 5.3 | 客戶 |

如果使用自訂映射，請使用角色型存取控制（RBAC），以確保只有授權的使用者可以存取映射。 使用共用映像庫，即可在組織內對於不同的使用者、服務主體或 AD 群組共用您的映像。  針對容器映射，請將其儲存在 Azure Container Registry 中，並利用 RBAC 來確保只有授權的使用者可以存取影像。  

- [瞭解 Azure 中的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [瞭解 Container Registry 的 RBAC](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [如何在 Azure 中設定 RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [共用映像資源庫概觀](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.7 | 5.4 | 客戶 |

使用 Azure 原則定義和執行 Azure 資源的標準安全性設定。 使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的網路設定進行審核或強制執行。 您也可以使用與特定資源相關的內建原則定義。  此外，您可以使用 Azure 自動化來部署設定變更。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用別名](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.8 | 5.4 | 客戶 |

Azure 自動化狀態設定是適用于任何雲端或內部部署資料中心內 Desired State Configuration （DSC）節點的設定管理服務。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。 

- [將機器上架交由 Azure Automation State Configuration 管理](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.9 | 5.5 | 客戶 |

使用 Azure 資訊安全中心來執行 Azure 資源的基準掃描。  此外，請使用 Azure 原則來警示和審核 Azure 資源設定。

- [如何修復 Azure 資訊安全中心中的建議](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.10 | 5.5 | 客戶 |

使用 Azure 資訊安全中心來執行適用于容器的 OS 和 Docker 設定的基準掃描。

- [了解 Azure 資訊安全中心容器建議](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.11 | 13.1 | 客戶 |

搭配 Azure Key Vault 使用受控服務識別，以簡化及保護雲端應用程式的秘密管理。

- [如何與 Azure 受控識別整合](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [如何建立 Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [如何使用受控識別提供 Key Vault 驗證](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.12 | 4.1 | 客戶 |

在 Azure AD 中使用受控識別，以自動管理的身分識別提供 Azure 服務。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

- [如何設定受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.13 | 18.1、18。7 | 客戶 |

執行認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制：[惡意程式碼防護](security-control-malware-defense.md)