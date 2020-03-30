---
title: 安全性控制
description: 用於評估 Azure SQL 資料庫的安全控制清單
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190682"
---
# <a name="security-controls-for-azure-sql-database"></a>Azure SQL 資料庫的安全控制

本文記錄內置於 Azure SQL 資料庫中的安全控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL 資料庫包括[單個資料庫](sql-database-single-index.yml)和[託管實例](sql-database-managed-instance.md)。 以下條目適用于這兩個產品/服務，除非另有說明。

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| 是 | 僅適用于[單個資料庫](sql-database-single-index.yml)。 |
| Azure 虛擬網路注入支援| 是 | 僅適用于[託管實例](sql-database-managed-instance.md)。 |
| 網路隔離和防火牆支援| 是 | 資料庫級別和伺服器級別的防火牆。 網路隔離僅適用于[託管實例](sql-database-managed-instance.md)。 |
| 強制隧道支援| 是 | 通過[ExpressRoute](../expressroute/index.yml) VPN[託管實例](sql-database-managed-instance.md)。 |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援，如日誌分析或應用程式見解| 是 | SecureSphere 是 Imperva 的 SIEM 解決方案，通過[SQL 審核](sql-database-auditing.md)通過[Azure 事件中心](../event-hubs/index.yml)集成也得到支援。 |
| 控制平面和管理平面日誌記錄和審核| 是 | 對於某些事件，為 |
| 資料平面日誌記錄和審核 | 是 | 通過[SQL 審核](sql-database-auditing.md) |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure Active Directory (Azure AD) |
| 授權| 是 | None |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密：微軟管理的金鑰 | 是 | 稱為"使用中的加密"，如文章["始終加密](sql-database-always-encrypted.md)"中所述。 伺服器端加密使用[透明資料加密](transparent-data-encryption-azure-sql.md)。|
| 傳輸中的加密：<ul><li>Azure 快速路由加密</li><li>虛擬網路中的加密</li><li>虛擬網路之間的加密</ul>| 是 | 使用 HTTPS。 |
| 加密金鑰處理，如 CMK 或 BYOK| 是 | 提供服務管理和客戶管理的關鍵處理。 後者通過 Azure[金鑰保存庫](../key-vault/index.yml)提供。 |
| Azure 資料服務提供的列級加密| 是 | 通過[始終加密](sql-database-always-encrypted.md)。 |
| 加密 API 呼叫| 是 | 使用 HTTPS/TLS。 |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 建構管理支援，如配置版本控制| 否  | None |

## <a name="additional-security-controls-for-sql-database"></a>SQL 資料庫的其他安全控制

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 預防：漏洞評估 | 是 | 請參閱[SQL 漏洞評估服務可説明您識別資料庫漏洞](sql-vulnerability-assessment.md)。 |
| 預防性：資料發現和分類  | 是 | 請參閱[Azure SQL 資料庫和 SQL 資料倉儲資料發現&分類](sql-database-data-discovery-and-classification.md)。 |
| 檢測：威脅檢測 | 是 | 請參閱[Azure SQL 資料庫的高級威脅保護](sql-database-threat-detection-overview.md)。 |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。
