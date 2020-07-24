---
title: Azure 虛擬機器擴展集的安全性控制
description: 評估 Azure 虛擬機器擴展集的安全性控制清單
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4480ad425d9a3953fd5779f99d27b5b6b037e61e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029411"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Azure 虛擬機器擴展集的安全性控制

本文記載內建于 Azure 虛擬機器擴展集的安全性控制項。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 附註 |
|---|---|--|
| 服務端點支援| 是 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 是 |  |
| 強制通道支援| 是 | 請參閱[使用 Azure Resource Manager 部署模型設定強制通道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 附註|
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| 是 | 請參閱[在 azure 中監視和更新 Linux 虛擬機器](../virtual-machines/linux/tutorial-monitor.md)和[在 azure 中監視和更新 Windows 虛擬機器](../virtual-machines/windows/tutorial-monitor.md)。 |
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核 | 否 |  |

## <a name="identity"></a>身份識別

| 安全性控制 | 是/否 | 附註|
|---|---|--|
| 驗證| 是 |  |
| 授權| 是 |  |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 附註 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 是 | 請參閱[虛擬機器擴展集的 Azure 磁碟加密](disk-encryption-overview.md)。 |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | Azure 虛擬機器支援[ExpressRoute](../expressroute/index.yml)和 VNet 加密。 請參閱[Vm 中的傳輸中加密](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 是 | 客戶管理的金鑰是支援的 Azure 加密案例;請參閱[虛擬機器擴展集的 Azure 磁碟加密](disk-encryption-overview.md)|
| 資料行層級加密（Azure 資料服務）| 不適用 | |
| API 呼叫加密| 是 | 透過 HTTPS 和 TLS。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 附註|
|---|---|--|
| 設定管理支援（設定的版本設定等）| 是 |  | 

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。
