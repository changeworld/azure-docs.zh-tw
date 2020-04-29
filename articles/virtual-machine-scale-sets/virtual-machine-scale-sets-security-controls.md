---
title: Azure 虛擬機器擴展集的安全性控制
description: 評估 Azure 虛擬機器擴展集的安全性控制清單
ms.service: virtual-machine-scale-sets
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 4007f4adeee065fe32492d3bd16f3a06d24e7d96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190599"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Azure 虛擬機器擴展集的安全性控制

本文記載內建于 Azure 虛擬機器擴展集的安全性控制項。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備忘錄 |
|---|---|--|
| 服務端點支援| 是 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 是 |  |
| 強制通道支援| 是 | 請參閱[使用 Azure Resource Manager 部署模型設定強制通道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備忘錄|
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| 是 | 請參閱[在 azure 中監視和更新 Linux 虛擬機器](/azure/virtual-machines/linux/tutorial-monitoring)和[在 azure 中監視和更新 Windows 虛擬機器](/azure/virtual-machines/windows/tutorial-monitoring)。 |
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核 | 否 |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備忘錄|
|---|---|--|
| 驗證| 是 |  |
| 授權| 是 |  |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備忘錄 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 是 | 請參閱[虛擬機器擴展集的 Azure 磁碟加密](disk-encryption-overview.md)。 |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | Azure 虛擬機器支援[ExpressRoute](/azure/expressroute)和 VNet 加密。 請參閱[Vm 中的傳輸中加密](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 是 | 客戶管理的金鑰是支援的 Azure 加密案例;請參閱[虛擬機器擴展集的 Azure 磁碟加密](disk-encryption-overview.md)|
| 資料行層級加密（Azure 資料服務）| N/A | |
| API 呼叫加密| 是 | 透過 HTTPS 和 TLS。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備忘錄|
|---|---|--|
| 設定管理支援（設定的版本設定等）| 是 |  | 

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。
