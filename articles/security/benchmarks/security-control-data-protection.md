---
title: Azure 安全性控制-資料保護
description: Azure 安全性控制資料保護
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d89320807c6322120490db85100453edf593aded
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045530"
---
# <a name="security-control-data-protection"></a>安全性控制：資料保護

資料保護建議著重于解決與加密相關的問題、存取控制清單、身分識別型存取控制，以及資料存取的審核記錄。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.1 | 13.1 | 客戶 |

使用標記來協助追蹤儲存或處理敏感資訊的 Azure 資源。

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.2 | 13.2、2.10 | 客戶 |

針對個別的安全性網域（例如環境類型和資料敏感度層級），使用不同的訂用帳戶和管理群組來執行隔離。 您可以限制您的應用程式和企業環境所需的 Azure 資源存取層級。 您可以透過 Azure 角色型存取控制（Azure RBAC）來控制對 Azure 資源的存取。 

- [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.3 | 13.3 | 共用 |

利用網路周邊 Azure Marketplace 的協力廠商解決方案，監視是否有未經授權的機密資訊傳輸，並在警示資訊安全性專業人員時封鎖這類傳輸。

針對由 Microsoft 管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並防止客戶資料遺失和曝光。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.4 | 14.4 | 共用 |

加密傳輸中的所有機密資訊。 請確定任何連線到您 Azure 資源的用戶端都能協商 TLS 1.2 或更高版本。

遵循 Azure 資訊安全中心的待用加密建議和傳輸中的加密（如果適用）。

- [瞭解使用 Azure 傳輸加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.5 | 14.5 | 共用 |

當您的特定服務在 Azure 中沒有可用的功能時，請使用協力廠商的 active discovery 工具來識別組織的技術系統所儲存、處理或傳輸的所有機密資訊，包括位於網站上或遠端服務提供者的資料，以及更新組織的機密資訊清查。

使用 Azure 資訊保護來識別 Office 365 檔中的機密資訊。

使用 Azure SQL 資訊保護來協助分類和標記儲存在 Azure SQL Database 中的資訊。

- [如何執行 Azure SQL 資料探索](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [如何執行 Azure 資訊保護](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用以角色為基礎的存取控制來控制對資源的存取

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.6 | 14.6 | 客戶 |

使用 Azure AD RBAC 來控制對資料和資源的存取，否則請使用服務特定的存取控制方法。

- [如何在 Azure 中設定 RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.7 | 14.7 | 共用 |

如果在計算資源上符合規範，請執行協力廠商工具（例如自動化主機型資料遺失防護解決方案），以強制執行資料的存取控制，即使是從系統複製資料時也一樣。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.8 | 14.8 | 客戶 |

在所有 Azure 資源上使用靜態加密。 Microsoft 建議讓 Azure 管理您的加密金鑰，但在某些情況下，您可以選擇管理您自己的金鑰。 

- [瞭解 Azure 中的待用加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [如何設定客戶管理的加密金鑰](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.9 | 14.9 | 客戶 |

使用 Azure 監視器搭配 Azure 活動記錄，以針對重要的 Azure 資源進行變更時，建立警示。

- [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制：[弱點管理](security-control-vulnerability-management.md)