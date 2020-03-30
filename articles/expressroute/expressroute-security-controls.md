---
title: Azure 快速路由：安全控制
description: 用於評估 Azure ExpressRoute 的安全控制清單
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079921"
---
# <a name="security-controls-for-azure-expressroute"></a>Azure 快速路由的安全控制

本文將記錄 Azure ExpressRoute 中內置的安全控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| N/A |  |
| VNet 注入支援| N/A | |
| 網路隔離和防火牆支援| 是 | 每個客戶都包含在自己的路由域中，並隧道到自己的 VNet |
| 強制隧道支援| N/A | 通過邊境閘道協定 （BGP）。 |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | 請參閱[ExpressRoute 監視、指標和警報](expressroute-monitoring-metrics-alerts.md)。|
| 控制和管理平面日誌記錄和審核| 是 |  |
| 資料平面日誌記錄和審核| 否 |   |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 微軟 （GWM） 閘道 （控制器） 的服務帳戶;及時 （JIT） 對開發人員和 OP 的訪問。 |
| 授權|  是 |微軟 （GWM） 閘道 （控制器） 的服務帳戶;及時 （JIT） 對開發人員和 OP 的訪問。 |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密：微軟管理的金鑰 |  N/A | ExpressRoute 不存儲客戶資料。 |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | N/A |  |
| 列級加密（Azure 資料服務）| N/A | |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 否 | |
| API 呼叫加密| 是 | 通過[Azure 資源管理器](../azure-resource-manager/index.yml)和 HTTPS。 |


## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 建構管理支援（配置版本控制等）| 是 | 通過網路資來源提供者 （NRP）。 |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。