---
title: Azure Windows 虛擬機器的安全性控制
description: 評估 Azure Windows 虛擬機器的安全性控制清單
ms.service: virtual-machines
ms.subservice: security
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: ac1ed9ac25d65d0391175fc6d43b48048da74926
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82101581"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Windows 虛擬機器的安全性控制項

本文記載內建在 Windows 虛擬機器中的安全性控制項。

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備忘稿 |
|---|---|--|
| 服務端點支援| 是 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 是 |  |
| 強制通道支援| 是 | 請參閱[使用 Azure Resource Manager 部署模型設定強制通道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備忘稿|
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| 是 | [在 Azure 中監視和更新 Windows 虛擬機器](tutorial-monitoring.md)。 |
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核 | 否 |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備忘稿|
|---|---|--|
| 驗證| 是 |  |
| 授權| 是 |  |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備忘稿 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 是 | 請參閱[加密 WINDOWS VM 上的虛擬磁片](/azure/virtual-machines/windows/disk-encryption-overview)。 |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | Azure 虛擬機器支援[ExpressRoute](/azure/expressroute)和 VNet 加密。 請參閱[Vm 中的傳輸中加密](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 是 | 客戶管理的金鑰是支援的 Azure 加密案例;請參閱[Azure 加密總覽](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。|
| 資料行層級加密（Azure 資料服務）| 不適用 | |
| API 呼叫加密| 是 | 透過 HTTPS 和 TLS。 |



## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備忘稿|
|---|---|--|
| 設定管理支援（設定的版本設定等）| 是 |  | 

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../../security/fundamentals/security-controls.md)。
