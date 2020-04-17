---
title: Azure Windows 虛擬機器的安全控制
description: 以評估 Azure Windows 虛擬機器的安全控制清單
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 569c989e8876bb7213d1469c7a70da99aa0b1e9c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455337"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Windows 虛擬機器的安全控制

本文記錄 Windows 虛擬機器中內置的安全控制項。

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| 是 | |
| VNet 注射支援| 是 | |
| 網路隔離和防火牆支援| 是 |  |
| 強制隧道支援| 是 | 請參考[Azure 資源管理員部署模型設定強制隧道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)。 |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援(紀錄分析、應用見解等)| 是 | [監視並更新 Azure 中的 Windows 虛擬機器](tutorial-monitoring.md)。 |
| 控制並管理平面紀錄記錄和稽核| 是 |  |
| 資料平面紀錄記錄和稽核 | 否 |  |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 |  |
| 授權| 是 |  |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密:微軟管理的金鑰 | 是 | 請參考[在 Windows VM 上加密虛擬磁碟](/azure/virtual-machines/windows/disk-encryption-overview)。 |
| 傳輸中的加密(如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密)| 是 | Azure 虛擬機器支援[ExpressRoute](/azure/expressroute)和 VNet 加密。 請參考[VM 中的傳輸加密](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。 |
| 伺服器端靜態加密:客戶管理的金鑰 (BYOK) | 是 | 客戶管理的密鑰是受支援的 Azure 加密方案;因此,這些密鑰受支援。請參考[Azure 加密概述](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。|
| 欄位編碼(Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 通過 HTTPS 和 TLS。 |



## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援(設定版本控制等)| 是 |  | 

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中內建安全控制器](../../security/fundamentals/security-controls.md)。
