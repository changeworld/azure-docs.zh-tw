---
title: 安全性控制
description: 評估 Azure SQL Database 的安全性控制檢查清單
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 97d2cd8e9ba51e4fc6ebab8459b04f4f37e876d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668418"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 和 SQL 受控執行個體的安全性控制項
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文記載 Azure SQL Database 和 Azure SQL 受控執行個體內建的安全性控制項。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備註 |
|---|---|--|
| 服務端點支援| Yes | 僅適用于[SQL Database](../index.yml) 。 |
| Azure 虛擬網路插入支援| Yes | 僅適用于[SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)。 |
| 網路隔離和防火牆支援| Yes | 資料庫層級和伺服器層級的防火牆。 網路隔離僅適用于[SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)。 |
| 強制通道支援| Yes | 透過[ExpressRoute](../expressroute/../index.yml) VPN 的[SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| Azure 監視支援，例如 Log Analytics 或 Application Insights| Yes | SecureSphere （來自 Imperva 的 SIEM 解決方案）也透過[SQL 審核](../../azure-sql/database/auditing-overview.md) [Azure 事件中樞](../event-hubs/../index.yml)整合來支援。 |
| 控制平面和管理平面記錄和審核| Yes | 是，僅適用于某些事件 |
| 資料平面記錄和審核 | Yes | 透過[SQL audit](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 驗證| Yes | Azure Active Directory (Azure AD) |
| 授權| Yes | None |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備註 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | Yes | 如[Always Encrypted](always-encrypted-certificate-store-configure.md)一文所述，稱為「加密使用中」。 伺服器端加密會使用[透明資料加密](transparent-data-encryption-tde-overview.md)。|
| 傳輸中加密：<ul><li>Azure ExpressRoute 加密</li><li>虛擬網路中的加密</li><li>虛擬網路之間的加密</ul>| Yes | 使用 HTTPS。 |
| 加密金鑰處理，例如 CMK 或 BYOK| Yes | 提供服務管理和客戶管理的金鑰處理。 後者會透過[Azure Key Vault](../key-vault/../index.yml)來提供。 |
| Azure data services 提供的資料行層級加密| Yes | 透過[Always Encrypted](always-encrypted-certificate-store-configure.md)。 |
| 加密的 API 呼叫| Yes | 使用 HTTPS/TLS。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 設定管理支援，例如設定的版本設定| 否  | None |

## <a name="additional-security-controls-for-sql-database"></a>SQL Database 的其他安全性控制

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 預防性：弱點評估 | Yes | 請參閱[SQL 弱點評定服務協助您識別資料庫弱點](sql-vulnerability-assessment.md)。 |
| 預防性：資料探索與分類  | Yes | 請參閱[Azure SQL Database 和 SQL 資料倉儲資料探索 & 分類](data-discovery-and-classification-overview.md)。 |
| 偵測：威脅偵測 | Yes | 請參閱[Azure SQL Database 的 Advanced 威脅防護](threat-detection-overview.md)。 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../../security/fundamentals/security-controls.md)。
