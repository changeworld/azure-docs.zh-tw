---
title: Azure 負載等化器的安全控制
description: 用於評估負載平衡器的安全控制清單
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214907"
---
# <a name="security-controls-for-azure-load-balancer"></a>Azure 負載等化器的安全控制

本文將記錄 Azure 負載等化器中內置的安全控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| N/A | |
| VNet 注入支援| N/A | |
| 網路隔離和防火牆支援| N/A |  |
| 強制隧道支援| N/A | |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | 有關[公共基本負載平衡器，請參閱 Azure 監視器日誌](load-balancer-monitor-log.md)。 |
| 控制和管理平面日誌記錄和審核| 是 | 有關[公共基本負載平衡器，請參閱 Azure 監視器日誌](load-balancer-monitor-log.md)。 |
| 資料平面日誌記錄和審核 | N/A |  |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| N/A |  |
| 授權| N/A |  |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密：微軟管理的金鑰 | N/A | |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| N/A | |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | N/A | |
| 列級加密（Azure 資料服務）| N/A | |
| API 呼叫加密| 是 | 通過[Azure 資源管理器](../azure-resource-manager/index.yml)。 |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 建構管理支援（配置版本控制等）| N/A |  | 

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。