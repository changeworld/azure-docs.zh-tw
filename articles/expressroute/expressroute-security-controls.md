---
title: Azure ExpressRoute：安全性控制
description: 評估 Azure ExpressRoute 的安全性控制清單
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74079921"
---
# <a name="security-controls-for-azure-expressroute"></a>Azure ExpressRoute 的安全性控制

本文記載 Azure ExpressRoute 內建的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備註 |
|---|---|--|
| 服務端點支援| N/A |  |
| VNet 插入支援| N/A | |
| 網路隔離和防火牆支援| Yes | 每個客戶都包含在自己的路由網域中，並通道傳送至自己的 VNet |
| 強制通道支援| N/A | Via 邊界閘道協定（BGP）。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| Yes | 請參閱[ExpressRoute 監視、計量和警示](expressroute-monitoring-metrics-alerts.md)。|
| 控制和管理平面記錄和審核| Yes |  |
| 資料平面記錄和審核| No |   |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 驗證| Yes | 適用于 Microsoft 的閘道服務帳戶（GWM）（控制器）;適用于 Dev 和 OP 的即時（JIT）存取。 |
| 授權|  Yes |適用于 Microsoft 的閘道服務帳戶（GWM）（控制器）;適用于 Dev 和 OP 的即時（JIT）存取。 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備註 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 |  N/A | ExpressRoute 不會儲存客戶資料。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | N/A |  |
| 資料行層級加密（Azure 資料服務）| N/A | |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| No | |
| API 呼叫加密| Yes | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS。 |


## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 設定管理支援（設定的版本設定等）| Yes | 透過網路資源提供者（NRP）。 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。