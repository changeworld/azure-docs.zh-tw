---
title: Azure VPN 閘道的安全性控制
description: 評估 Azure VPN 閘道的安全性控制清單
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fc5b4c901254decdb2d34281a10ababd4d79d45
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82127861"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Azure VPN 閘道的安全性控制

本文記載內建于 Azure VPN 閘道的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備忘稿 |
|---|---|--|
| 服務端點支援| 不適用 | |
| VNet 插入支援| 不適用 | |
| 網路隔離和防火牆支援| 是 | VPN 閘道是每個客戶的專用 VM 實例虛擬網路  |
| 強制通道支援| 是 |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備忘稿|
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| 是 | 請參閱[Azure 監視器記錄警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)  &  [Azure 監視器計量警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。  |
| 控制和管理平面記錄和審核| 是 | Azure Resource Manager 活動記錄。 |
| 資料平面記錄和審核 | 是 | Azure 監視器用於 VPN 連線記錄與審核的[活動記錄](../azure-resource-manager/management/view-activity-logs.md)。 |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備忘稿|
|---|---|--|
| 驗證| 是 | 用於管理服務和設定 Azure VPN 閘道的[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 。 |
| 授權| 是 | 透過[RBAC](../role-based-access-control/overview.md)支援授權。 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備忘稿 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 不適用 | VPN 閘道傳輸客戶資料，不會儲存客戶資料 |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | VPN 閘道會加密 Azure VPN 閘道與客戶內部部署 VPN 裝置（S2S）或 VPN 用戶端（P2S）之間的客戶封包。 VPN 閘道也支援 VNet 對 VNet 加密。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 否 | 客戶指定的預先共用金鑰會在待用時加密;但尚未與 CMK 整合。 |
| 資料行層級加密（Azure 資料服務）| 不適用 | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS  |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備忘稿|
|---|---|--|
| 設定管理支援（設定的版本設定等）| 是 | 針對管理作業，Azure VPN 閘道設定的狀態可以匯出為 Azure Resource Manager 範本，並在一段時間後進行版本設定。 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。
