---
title: Azure Linux 虛擬機器的安全性控制-Linux
description: 評估 Azure Linux 虛擬機器的安全性控制清單
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 132f696dd1298384c302eeadc264e857d1edff96
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080058"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Linux 虛擬機器的安全性控制項

本文記載內建在 Linux 虛擬機器中的安全性控制項。

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 附註 |
|---|---|--|
| 服務端點支援| 是 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 是 |  |
| 強制通道支援| 是 | 請參閱[使用 Azure Resource Manager 部署模型設定強制通道](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 附註|
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| 是 | 請參閱[在 Azure 中監視和更新 Linux 虛擬機器](./tutorial-monitor.md)。 |
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核 | 否 |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 附註|
|---|---|--|
| 驗證| 是 |  |
| 授權| 是 |  |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 附註 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 是 | 請參閱[Linux vm 的 Azure 磁碟加密](disk-encryption-overview.md)。 |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | Azure 虛擬機器支援[ExpressRoute](../../expressroute/index.yml)和 VNet 加密。 請參閱[Vm 中的傳輸中加密](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 是 | 客戶管理的金鑰是支援的 Azure 加密案例;請參閱[Azure 加密總覽](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)。|
| 資料行層級加密（Azure 資料服務）| 不適用 | |
| API 呼叫加密| 是 | 透過 HTTPS 和 TLS。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 附註|
|---|---|--|
| 設定管理支援（設定的版本設定等）| 是 |  | 

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../../security/fundamentals/security-controls.md)。
