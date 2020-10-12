---
title: Azure ExpressRoute：安全性控制措施
description: 瞭解 Azure ExpressRoute 中的安全性控制項，這是有助於預防、偵測及回應安全性弱點的品質或功能。
services: expressroute
ms.service: expressroute
author: duongau
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: duau
ms.openlocfilehash: 24057de44f3d28df96bcb93e89af9c3afa6fa3c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394902"
---
# <a name="security-controls-for-azure-expressroute"></a>適用于 Azure ExpressRoute 的安全性控制

本文說明 Azure ExpressRoute 內建的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A |  |
| VNet 插入支援| N/A | |
| 網路隔離和防火牆支援| 是 | 每個客戶都包含在自己的路由網域中，並通道傳送至其本身的 VNet |
| 強制通道支援| N/A | Via 邊界閘道協定 (BGP) 。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 是 | 請參閱 [ExpressRoute 監視、計量和警示](expressroute-monitoring-metrics-alerts.md)。|
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核| 否 |   |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 適用于 Microsoft (GWM)  (控制器) ; 的閘道服務帳戶及時 (JIT) 開發和 OP 的存取。 |
| 授權|  是 |適用于 Microsoft (GWM)  (控制器) ; 的閘道服務帳戶及時 (JIT) 開發和 OP 的存取。 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 |  N/A | ExpressRoute 不會儲存客戶資料。 |
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | N/A |  |
| Azure 資料服務) 的資料行層級加密 (| N/A | |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet-VNet 加密) | 否 | |
| API 呼叫加密| 是 | 透過 [Azure Resource Manager](../azure-resource-manager/index.yml) 和 HTTPS。 |


## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定版本等 ) | 是 | 透過網路資源提供者 (NRP) 。 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../security/fundamentals/security-controls.md)。