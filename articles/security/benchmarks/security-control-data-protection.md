---
title: Azure 安全控制 - 資料保護
description: Azure 安全控制資料保護
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 035894c80e619851264aae91daa2d7852d156964
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408551"
---
# <a name="security-control-data-protection"></a>安全控制:資料保護

數據保護建議側重於解決與加密、訪問控制列表、基於身份的訪問控制和數據訪問的審核日誌記錄相關的問題。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 維護敏感資訊的清單

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.1 | 13.1 | 客戶 |

使用標記可幫助追蹤記憶體或處理敏感資訊的 Azure 資源。

- [如何建立與使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 隔離記憶體或處理敏感資訊的系統

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.2 | 13.2, 2.10 | 客戶 |

使用單獨的訂閱和管理組對各個安全域(如環境類型和數據敏感度級別)實現隔離。 您可以限制對應用程式和企業環境需要的 Azure 資源的訪問級別。 您可以通過 Azure 活動目錄基於角色的訪問控制來控制對 Azure 資源的訪問。 

- [如何建立其他 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何建立與使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 監控和阻止未經授權傳輸敏感資訊

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.3 | 13.3 | 共用 |

利用 Azure 應用商店網路週邊的第三方解決方案,該解決方案可監視敏感資訊的未授權傳輸,並阻止此類傳輸,同時提醒資訊安全專業人員。

對於由 Microsoft 管理的基礎平臺,Microsoft 將所有客戶內容視為敏感內容,並防範客戶數據丟失和暴露。 為了確保 Azure 中的客戶數據保持安全,Microsoft 已經實施並維護了一套強大的數據保護控制和功能。

- [瞭解 Azure 的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:加密傳輸中的所有敏感資訊

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.4 | 14.4 | 共用 |

加密傳輸中的所有敏感資訊。 確保連接到 Azure 資源的任何用戶端能夠協商 TLS 1.2 或更高。

請按照 Azure 安全中心的建議,瞭解靜態加密和傳輸中的加密(如果適用)。

- [瞭解使用 Azure 傳輸中的加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 使用活動探索工具辨識敏感資料

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.5 | 14.5 | 共用 |

在 Azure 中的特定服務沒有可用的功能時,請使用第三方活動發現工具來標識組織技術系統(包括位於現場或遠端服務提供者)儲存、處理或傳輸的所有敏感資訊,並更新組織的敏感資訊清單。

使用 Azure 資訊保護標識 Office 365 文檔中的敏感資訊。

使用 Azure SQL 資訊保護可幫助分類和標記儲存在 Azure SQL 資料庫中的資訊。

- [如何實現 Azure SQL 資料發現](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [如何實現 Azure 資訊保護](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [瞭解 Azure 的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 使用基於角色的存取控制來控制對資源的存取

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.6 | 14.6 | 客戶 |

使用 Azure AD RBAC 控制對資料和資源的訪問,否則請使用特定於服務的存取控制方法。

- [如何在 Azure 中設定 RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 使用基於主機的數據丟失防護來實施訪問控制

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.7 | 14.7 | 共用 |

如果需要符合計算資源,則實施第三方工具(如基於主機的自動資料丟失防護解決方案),以便強制訪問數據,即使數據從系統複製也是如此。

對於由 Microsoft 管理的基礎平臺,Microsoft 將所有客戶內容視為敏感內容,並竭盡全力防止客戶數據丟失和暴露。 為了確保 Azure 中的客戶數據保持安全,Microsoft 已經實施並維護了一套強大的數據保護控制和功能。

- [瞭解 Azure 的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 靜態加密敏感資訊

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.8 | 14.8 | 客戶 |

對所有 Azure 資源使用靜態加密。 Microsoft 建議允許 Azure 管理加密密鑰,但在某些情況下,您可以選擇管理自己的密鑰。 

- [瞭解 Azure 中靜態加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [如何設定客戶託管加密金鑰](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 對關鍵 Azure 資源的更改進行紀錄和警報

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 4.9 | 14.9 | 客戶 |

將 Azure 監視器與 Azure 活動日誌一起創建對關鍵 Azure 資源進行更改時的警報。

- [如何為 Azure 活動紀錄事件建立警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>後續步驟

- 請參考下一個安全控制:[漏洞管理](security-control-vulnerability-management.md)