---
title: Azure 安全控制 - 安全配置
description: 安全控制安全配置
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934303"
---
# <a name="security-control-secure-configuration"></a>安全控制：安全配置

建立、實施並積極管理 Azure 資源的安全配置（跟蹤、報告、更正），以防止攻擊者利用易受攻擊的服務和設置。

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全配置

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.1 | 5.1 | 客戶 |

使用 Azure 策略或 Azure 安全中心維護所有 Azure 資源的安全配置。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的作業系統配置

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.2 | 5.1 | 客戶 |

使用 Azure 安全&quot;中心建議修復虛擬機器&quot;上安全配置中的漏洞，以維護所有計算資源的安全配置。

如何監視 Azure 安全中心建議：

https://docs.microsoft.com/azure/security-center/security-center-recommendations

如何修復 Azure 安全中心建議：

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源配置

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.3 | 5.2 | 客戶 |

使用 Azure 策略 [拒絕] 和 [部署（如果不存在）在 Azure 資源中強制實施安全設置。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

瞭解 Azure 策略效果：

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 維護安全的作業系統配置

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.4 | 5.2 | 共用 |

基本作業系統映射由 Microsoft 管理和維護。

但是，您可以使用 Azure 資源管理器範本和/或所需的狀態配置應用組織所需的安全設置。

如何從 Azure 資源管理器範本創建 Azure 虛擬機器：

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

瞭解 Azure 虛擬機器所需的狀態配置：

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存儲 Azure 資源的配置

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.5 | 5.3 | 客戶 |

如果使用自訂 Azure 策略定義，請使用 Azure DevOps 或 Azure 存儲庫安全地存儲和管理代碼。

如何在 Azure DevOps 中存儲代碼：

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 存儲庫文檔：

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存儲自訂作業系統映射

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.6 | 5.3 | 客戶 |

如果使用自訂映射，請使用 RBAC 確保只有經過授權的使用者才能訪問映射。 對於容器映射，將它們存儲在 Azure 容器註冊表中，並利用 RBAC 確保只有經過授權的使用者才能訪問映射。

瞭解 Azure 中的 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

瞭解容器註冊表的 RBAC：

https://docs.microsoft.com/azure/container-registry/container-registry-roles

如何在 Azure 中配置 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系統建構管理工具

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.7 | 5.4 | 客戶 |

使用 Azure 策略警報、審核和強制實施系統組態。 此外，開發用於管理原則異常的流程和管道。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：為作業系統部署系統建構管理工具

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.8 | 5.4 | 客戶 |

使用 Azure 計算擴展，如適用于 Windows 計算的 PowerShell 所需狀態配置或 Linux 的 Linux Chef 擴展。

如何在 Azure 中安裝虛擬機器擴展：

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實現自動化配置監視

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.9 | 5.5 | 客戶 |

使用 Azure 安全中心對 Azure 資源執行基線掃描

如何修復 Azure 安全中心中的建議：

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 對作業系統實施自動設定監控

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.1 | 5.5 | 客戶 |

使用 Azure 安全中心對容器的作業系統和 Docker 設置執行基線掃描。

瞭解 Azure 安全中心容器建議：

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 機密 

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.11 | 13.1 | 客戶 |

將託管服務標識與 Azure 金鑰保存庫結合使用，以簡化和保護雲應用程式的秘密管理。

如何與 Azure 託管標識集成：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何創建金鑰保存庫：

https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用託管標識提供金鑰保存庫身份驗證：

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自動地管理身份

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.12 | 4.1 | 客戶 |

使用託管標識在 Azure AD 中為 Azure 服務提供自動託管標識。 託管標識允許您對支援 Azure AD 身份驗證的任何服務（包括金鑰保存庫）進行身份驗證，而無需在代碼中進行任何憑據。

如何配置託管標識：

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的憑據暴露

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 7.13 | 13.3 | 客戶 |

實現憑據掃描程式以標識代碼中的憑據。 憑據掃描程式還將鼓勵將發現的憑據移動到更安全的位置，如 Azure 金鑰保存庫。 

如何設置憑據掃描程式：

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>後續步驟

請參閱下一個安全控制：[惡意軟體防禦](security-control-malware-defense.md)
