---
title: Azure VPN 閘道的安全控制
description: 用於評估 Azure VPN 閘道的安全控制清單
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972285"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Azure VPN 閘道的安全控制

本文將記錄 Azure VPN 閘道中內置的安全控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| N/A | |
| VNet 注入支援| N/A | |
| 網路隔離和防火牆支援| 是 | VPN 閘道是每個客戶虛擬網路的專用 VM 實例  |
| 強制隧道支援| 是 |  |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | 請參閱[Azure 監視器診斷日誌/警報](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure 監視器指標/警報](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。  |
| 控制和管理平面日誌記錄和審核| 是 | Azure 資源管理器活動日誌。 |
| 資料平面日誌記錄和審核 | 是 | [Azure 監視診斷日誌](../azure-resource-manager/management/view-activity-logs.md)，用於 VPN 連接日誌記錄和審核。 |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 用於管理服務和配置 Azure VPN 閘道的[Azure 活動目錄](../active-directory/fundamentals/active-directory-whatis.md)。 |
| 授權| 是 | 通過[RBAC](../role-based-access-control/overview.md)提供支援授權 。 |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密：微軟管理的金鑰 | N/A | VPN 閘道傳輸客戶資料，不存儲客戶資料 |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | VPN 閘道在 Azure VPN 閘道和客戶本地 VPN 設備 （S2S） 或 VPN 用戶端 （P2S） 之間加密客戶資料包。 VPN 閘道還支援 VNet 到 VNet 加密。 |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | 否 | 客戶指定的預共用金鑰在靜態時進行加密;但尚未與 CMK 集成。 |
| 列級加密（Azure 資料服務）| N/A | |
| API 呼叫加密| 是 | 通過[Azure 資源管理器](../azure-resource-manager/index.yml)和 HTTPS  |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 建構管理支援（配置版本控制等）| 是 | 對於管理操作，Azure VPN 閘道配置的狀態可以匯出為 Azure 資源管理器範本，並隨著時間的推移進行版本控制。 |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。
