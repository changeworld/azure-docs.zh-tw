---
title: Azure Windows 虛擬機器的安全控制
description: 用於評估 Azure Windows 虛擬機器的安全控制清單
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: f105eac0f6f21ea3358340a4e2aaec7d1f1a95ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190553"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Windows 虛擬機器的安全控制

本文記錄 Windows 虛擬機器中內置的安全控制項。

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| 是 | |
| VNet 注入支援| 是 | |
| 網路隔離和防火牆支援| 是 |  |
| 強制隧道支援| 是 | 請參閱[使用 Azure 資源管理器部署模型配置強制隧道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)。 |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | [監視和更新 Azure 中的 Windows 虛擬機器](tutorial-monitoring.md)。 |
| 控制和管理平面日誌記錄和審核| 是 |  |
| 資料平面日誌記錄和審核 | 否 |  |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 |  |
| 授權| 是 |  |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密：微軟管理的金鑰 | 是 | 請參閱[在 Windows VM 上加密虛擬磁片](/azure/virtual-machines/windows/encrypt-disks)。 |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | Azure 虛擬機器支援[ExpressRoute](/azure/expressroute)和 VNet 加密。 請參閱[VM 中的傳輸加密](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。 |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | 是 | 客戶管理的金鑰是受支援的 Azure 加密方案;因此，這些金鑰受支援。請參閱[Azure 加密概述](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。|
| 列級加密（Azure 資料服務）| N/A | |
| API 呼叫加密| 是 | 通過 HTTPS 和 TLS。 |



## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 建構管理支援（配置版本控制等）| 是 |  | 

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../../security/fundamentals/security-controls.md)。
