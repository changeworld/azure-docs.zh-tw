---
title: Azure 安全控制 - 資料保護
description: 安全控制資料保護
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934508"
---
# <a name="security-control-data-protection"></a>安全控制：資料保護

資料保護建議側重于解決與加密、存取控制清單、基於身份的存取控制和資料訪問的稽核記錄記錄相關的問題。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 維護敏感資訊的清單

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.1 | 13.1 | 客戶 |

使用標記可説明跟蹤存儲或處理敏感資訊的 Azure 資源。

如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔離存儲或處理敏感資訊的系統

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.2 | 13.2 | 客戶 |

為開發、測試和生產實施單獨的訂閱和/或管理組。 資源應由 VNet/子網分隔，標記得當，並由 NSG 或 Azure 防火牆保護。 存儲或處理敏感性資料的資源應足夠隔離。 對於存儲或處理敏感性資料的虛擬機器，請實施策略和程式，在不使用時將其關閉。

如何創建其他 Azure 訂閱：

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何創建管理組：

https://docs.microsoft.com/azure/governance/management-groups/create

如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何創建虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何創建具有安全配置的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何部署 Azure 防火牆：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火牆配置警報或警報和拒絕：

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 監控和阻止未經授權傳輸敏感資訊

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.3 | 13.3 | 客戶 |

在網路週邊部署自動工具，用於監視敏感資訊的未授權傳輸，並阻止此類傳輸，同時提醒資訊安全專業人員。

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感資訊

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.4 | 14.4 | 共用 |

加密傳輸中的所有敏感資訊。 確保連接到 Azure 資源的任何用戶端能夠協商 TLS 1.2 或更高。

請按照 Azure 安全中心的建議，瞭解靜態加密和傳輸中的加密（如果適用）。

瞭解使用 Azure 傳輸中的加密：

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活動發現工具識別敏感性資料

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.5 | 14.5 | 客戶 |

在 Azure 中沒有可用於特定服務的功能時，請使用協力廠商活動發現工具來標識組織技術系統存儲、處理或傳輸的所有敏感資訊，包括位於現場或遠端服務提供者，並更新組織的敏感資訊清單。

使用 Azure 資訊保護標識 Office 365 文檔中的敏感資訊。

使用 Azure SQL 資訊保護可説明分類和標記存儲在 Azure SQL 資料庫中的資訊。

如何實現 Azure SQL 資料發現：

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

如何實現 Azure 資訊保護：

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制對資源的訪問

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.6 | 14.6 | 客戶 |

使用 Azure AD RBAC 控制對資料和資源的訪問，否則請使用特定于服務的存取控制方法。

瞭解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

如何在 Azure 中配置 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基於主機的資料丟失防護來實施存取控制

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.7 | 14.7 | 客戶 |

實施協力廠商工具（如基於主機的自動資料丟失防護解決方案），即使將資料從系統複製，也能強制實施對資料的存取控制。

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 靜態加密敏感資訊

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.8 | 14.8 | 客戶 |

對所有 Azure 資源使用靜態加密。 Microsoft 建議允許 Azure 管理加密金鑰，但在某些情況下，您可以選擇管理自己的金鑰。 

瞭解 Azure 中靜態加密：

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

如何配置客戶託管加密金鑰：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 對關鍵 Azure 資源的更改進行日誌和警報

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.9 | 14.9 | 客戶 |

將 Azure 監視器與 Azure 活動日誌一起創建對關鍵 Azure 資源進行更改時的警報。

如何為 Azure 活動日誌事件創建警報：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>後續步驟

請參閱下一個安全控制：[漏洞管理](security-control-vulnerability-management.md)
