---
title: Azure 服務匯流排訊息的安全性控制
description: 用於評估 Azure 服務匯流排訊息的安全性控制檢查清單
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3130150a227076befae3f58f65e00a36578b68d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341635"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Azure 服務匯流排訊息的安全性控制

本文記載 Azure 服務匯流排訊息內建的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 服務端點支援| 是 (進階層僅)  | VNet 服務端點僅支援 [服務匯流排 Premium 層](service-bus-premium-messaging.md) 。 |  |
| VNet 插入支援| 否 | |  |
| 網路隔離和防火牆支援| 是 (進階層僅)  |  |  |
| 強制通道支援| 否 |  |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 是 | 支援 [Azure 監視器和警示](service-bus-metrics-azure-monitor.md)。 |  |
| 控制和管理平面記錄和審核| 是 | 可用的作業記錄。  | [服務匯流排診斷記錄](service-bus-diagnostic-logs.md) |
| 資料平面記錄和審核| 否 |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 驗證| 是 | 透過 [Azure Active Directory 受控服務識別](service-bus-managed-service-identity.md)來管理。| [服務匯流排驗證和授權](service-bus-authentication-and-authorization.md)。 |
| 授權| 是 | 透過 [RBAC](authenticate-application.md) 和 SAS 權杖支援授權。 | [服務匯流排驗證和授權](service-bus-authentication-and-authorization.md)。 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 |  是，預設為伺服器端加密。 |  |  |
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | 是。 | Azure KeyVault 中的客戶管理金鑰可以用來加密待用服務匯流排命名空間上的資料。 | [使用 Azure 入口網站，設定客戶管理的金鑰來加密 Azure 服務匯流排待用資料](configure-customer-managed-key.md)  |
| Azure 資料服務) 的資料行層級加密 (| N/A | |   |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet-VNet 加密) | 是 | 支援標準 HTTPS/TLS 機制。 |   |
| API 呼叫加密| 是 | API 呼叫會透過 [Azure Resource Manager](../azure-resource-manager/index.yml) 和 HTTPS 進行。 |   |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 設定管理支援 (設定版本等 ) | 是 | 透過 [AZURE RESOURCE MANAGER API](/rest/api/resources/)支援資源提供者版本控制。|   |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../security/fundamentals/security-controls.md)。
