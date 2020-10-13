---
title: Azure 安全性基準測試 V2-資料保護
description: Azure 安全性基準測試 V2 資料保護
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ab42acbc07072f48d28b3adb5bc4c65672f257bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314312"
---
# <a name="security-control-v2-data-protection"></a>安全性控制 V2：資料保護

資料保護涵蓋對待用、傳輸中的資料保護，以及經由授權的存取機制的控制。 這包括使用 Azure 中的存取控制、加密和記錄來探索、分類、保護及監視機密資料資產。

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1：探索、分類及標記敏感性資料

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| DP-1 | 13.1、14.5、14。7 | SC-28 |

探索、分類和標示您的機密資料，讓您可以設計適當的控制項，以確保組織的技術系統會安全地儲存、處理及傳輸機密資訊。 

針對 Azure、內部部署、Office 365 和其他位置中的 Office 檔中的機密資訊，使用 Azure 資訊保護 (及其相關的掃描工具) 。 

您可以使用 Azure SQL Information Protection 來協助分類和標記儲存在 Azure SQL 資料庫中的資訊。

- [使用 Azure 資訊保護標記機密資訊](/azure/information-protection/what-is-information-protection) 

- [如何執行 Azure SQL 資料探索](/azure/sql-database/sql-database-data-discovery-and-classification)

**責任**：共用

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [資料安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2：保護敏感性資料

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| DP-2 | 13.2、2.10 | SC-7、AC-4 |

藉由使用 Azure 角色型存取控制來限制存取權， (Azure RBAC) 、網路型存取控制和 Azure 服務中的特定控制項 (例如 SQL 中的加密和其他資料庫) 。 

為了確保存取控制的一致性，所有類型的存取控制都應符合您的企業分割策略。 企業分割策略也應由機密或業務關鍵資料和系統的位置來通知。

針對 Microsoft 所管理的基礎平臺，Microsoft 會將所有客戶內容視為機密資料，並防止客戶資料遺失和公開。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已實行一些預設的資料保護控制項和功能。

- [Azure 角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)

- [瞭解 Azure 中的客戶資料保護](../fundamentals/protection-customer-data.md)

**責任**：共用

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [資料安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3：監視是否有未經授權的機密資料傳輸

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| DP-3 | 13.3 | AC-4，SI-4 |

監視未授權將資料傳輸至企業可見度和控制外部的位置。 這通常牽涉到監視異常活動 (大型或不尋常的傳輸) 可能表示未經授權的資料遭到外泄。 

Azure 儲存體 Advanced 威脅防護 (ATP) 和 Azure SQL ATP 可能會在可能表示未經授權傳送機密資訊的異常傳送資訊時發出警示。 

Azure 資訊保護 (AIP) 為已分類及標示的資訊提供監視功能。 

如果需要預防資料遺失防止 (DLP) ，您可以使用主機型 DLP 解決方案來強制執行一致性及/或預防措施，以防止資料遭到外泄。

- [啟用 Azure SQL ATP](../../azure-sql/database/threat-detection-overview.md)

- [啟用 Azure 儲存體 ATP](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**責任**：共用

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security) 

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密傳輸中的機密資訊

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| DP-4 | 14.4 | SC-8 |

為了補充存取控制，傳輸中的資料應該受到保護，以防範「頻外」攻擊 (例如，使用加密的流量捕捉) ，以確保攻擊者無法輕易地讀取或修改資料。 

雖然這對私人網路的流量是選擇性的，但對於外部和公用網路上的流量而言是不可或缺的。 針對 HTTP 流量，請確定任何連線至 Azure 資源的用戶端都可以協調 TLS 1.2 或更新版本。 若要進行遠端系統管理，請使用適用于 Linux) 的 SSH (或適用于 Windows) 的 RDP/TLS (，而不是未加密的通訊協定。 已淘汰的 SSL、TLS 和 SSH 版本和通訊協定，以及弱式密碼都應停用。  

根據預設，Azure 會為 Azure 資料中心之間傳輸中的資料提供加密功能。 

- [瞭解 Azure 中的傳輸加密](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [TLS 安全性的資訊](/security/engineering/solving-tls1-problem)

- [Azure 資料傳輸中的雙重加密](../fundamentals/double-encryption.md#data-in-transit)

**責任**：共用

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [資料安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5：加密待用的機密資料

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| DP-5 | 14.8 | SC-28、SC-12 |

為了補充存取控制，待用資料應受到保護，以防止「頻外」攻擊 (例如使用加密存取基礎儲存體) 。 這有助於確保攻擊者無法輕易地讀取或修改資料。 

Azure 預設會提供待用資料的加密。 針對高度敏感的資料，您可以選擇在所有可用的 Azure 資源上執行額外的靜態加密。 根據預設，azure 會管理您的加密金鑰，但 Azure 會提供選項來管理您自己的金鑰 (客戶管理的金鑰) 適用于特定的 Azure 服務。

- [瞭解 Azure 中的待用加密](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [如何設定客戶管理的加密金鑰](../../storage/common/storage-encryption-keys-portal.md)

- [加密模型和金鑰管理表](../fundamentals/encryption-models.md)

- [Azure 中的待用資料加密](../fundamentals/double-encryption.md#data-at-rest)

**責任**：共用

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [資料安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

