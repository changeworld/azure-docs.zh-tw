---
title: Azure 安全性控制-資料保護
description: Azure 安全性控制資料保護
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8e2144a61d83f6c7dece8f34232031192b51cde8
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412692"
---
# <a name="security-control-data-protection"></a>安全性控制：資料保護

資料保護建議著重于解決加密、存取控制清單、身分識別型存取控制和資料存取的審核記錄相關問題。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.1 | 13.1 | 客戶 |

使用標籤協助追蹤儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.2 | 13.2、2.10 | 客戶 |

針對個別的安全性網域（例如環境類型和資料敏感度層級），使用不同的訂用帳戶和管理群組來執行隔離。 您可以限制您的應用程式和企業環境所需的 Azure 資源存取層級。 您可以透過 Azure 角色型存取控制 (Azure RBAC) 來控制 Azure 資源的存取權。 

- [如何建立額外的 Azure 訂閱](../../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.3 | 13.3 | 共用 |

利用來自網路周邊 Azure Marketplace 的協力廠商解決方案，以監視是否有未經授權的機密資訊傳輸並封鎖這類傳輸，同時警示資訊安全專業人員。

針對 Microsoft 所管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並防止客戶資料遺失和公開。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../fundamentals/protection-customer-data.md)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.4 | 14.4 | 共用 |

加密傳輸中的所有機密資訊。 確定任何連線至 Azure 資源的用戶端都能夠協商 TLS 1.2 或更新版本。

遵循 Azure 資訊安全中心待用加密及傳輸中加密的建議（適用時）。

- [瞭解 Azure 中的傳輸加密](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.5 | 14.5 | 共用 |

當您在 Azure 中的特定服務沒有任何可用的功能時，請使用協力廠商主動式探索工具來識別組織技術系統儲存、處理或傳輸的所有機密資訊，包括位於現場或遠端服務提供者的機密資訊，以及更新組織的機密資訊清查。

使用 Azure 資訊保護來識別 Microsoft 365 檔中的機密資訊。

使用 Azure SQL 資訊保護來協助分類和標記儲存在 Azure SQL Database 中的資訊。

- [如何執行 Azure SQL 資料探索](../../azure-sql/database/data-discovery-and-classification-overview.md)

- [如何執行 Azure 資訊保護](/azure/information-protection/deployment-roadmap)

- [瞭解 Azure 中的客戶資料保護](../fundamentals/protection-customer-data.md)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.6 | 14.6 | 客戶 |

使用 Azure 角色型存取控制 (Azure RBAC) 來控制資料和資源的存取權，否則請使用服務特定的存取控制方法。

- [如何設定 Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.7 | 14.7 | 共用 |

如果需要對計算資源進行合規性，請執行協力廠商工具（例如自動化的主機型資料遺失防護解決方案），即使在系統上複製資料時，也會對資料強制執行存取控制。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../fundamentals/protection-customer-data.md)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.8 | 14.8 | 客戶 |

在所有 Azure 資源上使用待用加密。 Microsoft 建議讓 Azure 管理您的加密金鑰，但在某些情況下，您可以選擇管理您自己的金鑰。 

- [瞭解 Azure 中的待用加密](../fundamentals/encryption-atrest.md)

- [如何設定客戶管理的加密金鑰](../../storage/common/customer-managed-keys-configure-key-vault.md)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.9 | 14.9 | 客戶 |

使用 Azure 監視器搭配 Azure 活動記錄，以針對重要 Azure 資源發生變更時建立警示。

- [如何建立 Azure 活動記錄事件的警示](../../azure-monitor/platform/alerts-activity-log.md)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制：  [弱點管理](security-control-vulnerability-management.md)