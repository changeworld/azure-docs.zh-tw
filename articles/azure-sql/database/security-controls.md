---
title: 安全性控制
description: 用於評估 Azure SQL Database 的安全性控制檢查清單
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eec032ad56d00778627fc147761f61c03ba8bafd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442083"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 和 SQL 受控執行個體的安全性控制
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文記載 Azure SQL Database 和 Azure SQL 受控執行個體內建的安全性控制項。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 僅適用于 [SQL Database](../index.yml) 。 |
| Azure 虛擬網路插入支援| 是 | 僅適用于 [SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md) 。 |
| 網路隔離和防火牆支援| 是 | 資料庫層級和伺服器層級的防火牆。 網路隔離僅適用于 [SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md) 。 |
| 強制通道支援| 是 | [SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md) via [ExpressRoute](../expressroute/../index.yml) VPN。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援，例如 Log Analytics 或 Application Insights| 是 | SecureSphere 是 Imperva 中的 SIEM 解決方案，透過[SQL 審核](../../azure-sql/database/auditing-overview.md) [Azure 事件中樞](../event-hubs/../index.yml)整合也受到支援。 |
| 控制平面和管理平面記錄和審核| 是 | 只有某些事件才是 |
| 資料平面記錄和審核 | 是 | Via [SQL audit](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure Active Directory (Azure AD) |
| 授權| 是 | 無 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 | 是 | 稱為「使用中加密」，如 [Always Encrypted](always-encrypted-certificate-store-configure.md)一文所述。 伺服器端加密會使用 [透明資料加密](transparent-data-encryption-tde-overview.md)。|
| 傳輸中加密：<ul><li>Azure ExpressRoute 加密</li><li>虛擬網路中的加密</li><li>虛擬網路之間的加密</ul>| 是 | 使用 HTTPS。 |
| 加密-金鑰處理，例如 CMK 或 BYOK| 是 | 系統會提供服務管理和客戶管理的金鑰處理。 後者是透過 [Azure Key Vault](../key-vault/../index.yml)提供。 |
| Azure 資料服務所提供的資料行層級加密| 是 | 至 [Always Encrypted](always-encrypted-certificate-store-configure.md)。 |
| 加密的 API 呼叫| 是 | 使用 HTTPS/TLS。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援，例如設定的版本控制| 否  | None |

## <a name="additional-security-controls-for-sql-database"></a>SQL Database 的其他安全性控制項

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 預防性：弱點評定 | 是 | 請參閱 [SQL 弱點評估服務，協助您找出資料庫弱點](sql-vulnerability-assessment.md)。 |
| 預防性：資料探索和分類  | 是 | 請參閱 [Azure SQL Database 和 Azure Synapse Analytics 資料探索 & 分類](data-discovery-and-classification-overview.md)。 |
| 偵測：威脅偵測 | 是 | 請參閱 [Azure SQL Database 的 Advanced 威脅防護](threat-detection-overview.md)。 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../../security/fundamentals/security-controls.md)。
