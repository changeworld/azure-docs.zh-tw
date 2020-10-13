---
title: 適用于 Azure Windows 虛擬機器的安全性控制
description: 用來評估 Azure Windows 虛擬機器的安全性控制檢查清單
ms.service: virtual-machines
ms.subservice: security
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6ab6133faef4a6c7a8eb929e5f4cd1840e971a99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088337"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Windows 虛擬機器的安全性控制

本文記錄 Windows 虛擬機器內建的安全性控制項。

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 是 |  |
| 強制通道支援| 是 | 請參閱 [使用 Azure Resource Manager 部署模型設定強制通道](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 是 | [在 Azure 中監視和更新 Windows 虛擬機器](./tutorial-monitor.md)。 |
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核 | 否 |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 |  |
| 授權| 是 |  |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 | 是 | 請參閱 [加密 WINDOWS VM 上的虛擬磁片](./disk-encryption-overview.md)。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet-VNet 加密 ) | 是 | Azure 虛擬機器支援 [ExpressRoute](../../expressroute/index.yml) 和 VNet 加密。 查看 [Vm 中的傳輸中加密](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)。 |
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | 是 | 客戶管理的金鑰是支援的 Azure 加密案例;請參閱 [Azure 加密總覽](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)。|
| Azure 資料服務) 的資料行層級加密 (| N/A | |
| API 呼叫加密| 是 | Via HTTPS 和 TLS。 |



## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定版本等 ) | 是 |  | 

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../../security/fundamentals/security-controls.md)。
