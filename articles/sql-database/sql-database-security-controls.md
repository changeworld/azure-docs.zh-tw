---
title: 安全性控制
description: 評估 Azure SQL Database 的安全性控制檢查清單
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190682"
---
# <a name="security-controls-for-azure-sql-database"></a>Azure SQL Database 的安全性控制項

本文記載 Azure SQL Database 內建的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database 包括[單一資料庫](sql-database-single-index.yml)和[受控實例](sql-database-managed-instance.md)。 下列專案適用于這兩個供應專案，除非另有注明。

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意事項 |
|---|---|--|
| 服務端點支援| 是 | 僅適用于[單一資料庫](sql-database-single-index.yml)。 |
| Azure 虛擬網路插入支援| 是 | 僅適用于[受控實例](sql-database-managed-instance.md)。 |
| 網路隔離和防火牆支援| 是 | 資料庫層級和伺服器層級的防火牆。 網路隔離僅適用于[受控實例](sql-database-managed-instance.md)。 |
| 強制通道支援| 是 | 經由[ExpressRoute](../expressroute/index.yml) VPN 的[受控實例](sql-database-managed-instance.md)。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意事項|
|---|---|--|
| Azure 監視支援，例如 Log Analytics 或 Application Insights| 是 | SecureSphere （來自 Imperva 的 SIEM 解決方案）也透過[SQL 審核](sql-database-auditing.md) [Azure 事件中樞](../event-hubs/index.yml)整合來支援。 |
| 控制平面和管理平面記錄和審核| 是 | 是，僅適用于某些事件 |
| 資料平面記錄和審核 | 是 | 透過[SQL audit](sql-database-auditing.md) |

## <a name="identity"></a>相同比較

| 安全性控制 | 是/否 | 注意事項|
|---|---|--|
| 驗證| 是 | Azure Active Directory (Azure AD) |
| 授權| 是 | 無 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意事項 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 是 | 如[Always Encrypted](sql-database-always-encrypted.md)一文所述，稱為「加密使用中」。 伺服器端加密會使用[透明資料加密](transparent-data-encryption-azure-sql.md)。|
| 傳輸中加密：<ul><li>Azure ExpressRoute 加密</li><li>虛擬網路中的加密</li><li>虛擬網路之間的加密</ul>| 是 | 使用 HTTPS。 |
| 加密金鑰處理，例如 CMK 或 BYOK| 是 | 提供服務管理和客戶管理的金鑰處理。 後者會透過[Azure Key Vault](../key-vault/index.yml)來提供。 |
| Azure data services 提供的資料行層級加密| 是 | 透過[Always Encrypted](sql-database-always-encrypted.md)。 |
| 加密的 API 呼叫| 是 | 使用 HTTPS/TLS。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意事項|
|---|---|--|
| 設定管理支援，例如設定的版本設定| 否  | 無 |

## <a name="additional-security-controls-for-sql-database"></a>SQL Database 的其他安全性控制

| 安全性控制 | 是/否 | 注意事項|
|---|---|--|
| 預防性：弱點評估 | 是 | 請參閱[SQL 弱點評定服務協助您識別資料庫弱點](sql-vulnerability-assessment.md)。 |
| 預防性：資料探索與分類  | 是 | 請參閱[Azure SQL Database 和 SQL 資料倉儲資料探索 & 分類](sql-database-data-discovery-and-classification.md)。 |
| 偵測：威脅偵測 | 是 | 請參閱[Azure SQL Database 的 Advanced 威脅防護](sql-database-threat-detection-overview.md)。 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。
