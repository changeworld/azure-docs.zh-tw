---
title: 安全性控制
titleSuffix: Azure Storage
description: 查看用來評估 Azure 儲存體的安全性控制檢查清單。 涵蓋的區域包括資料保護、網路、監視和記錄、身分識別和設定。
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 3b4d74e7ba869e0438a936817d824e841823d472
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715710"
---
# <a name="security-controls-for-azure-storage"></a>Azure 儲存體的安全性控制

本文記錄 Azure 儲存體內建的安全性控制項。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備註 |
|---|---|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 | 是 |  |
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | 是 | 請參閱 [Azure Key Vault 中使用客戶管理金鑰的儲存體服務加密](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| Azure 資料服務) 的資料行層級加密 (| N/A |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet VNet 加密) | 是 | 支援標準 HTTPS/TLS 機制。  使用者也可以在資料傳輸至服務之前加密資料。 |
| API 呼叫加密| 是 |  |

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備註 |
|---|---|--|
| 服務端點支援| 是 |  |
| 服務標記支援| 是 | 如需 Azure 儲存體所支援服務標記的詳細資訊，請參閱 [Azure 服務標記總覽](../../virtual-network/service-tags-overview.md) 。 |
| VNet 插入支援| N/A |  |
| 網路隔離和防火牆支援| 是 | |
| 強制通道支援| N/A |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 是 | Azure 監視器計量|
| 控制和管理平面記錄和審核 | 是 | Azure 活動記錄檔 |
| 資料平面記錄和審核| 是 | Azure 監視器資源記錄 |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 驗證| 是 | Azure Active Directory、共用金鑰、共用存取權杖。 |
| 授權| 是 | 透過 Azure RBAC、POSIX Acl 和 SAS 權杖支援授權 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 設定管理支援 (設定版本等 ) | 是 | 透過 Azure Resource Manager Api 支援資源提供者版本控制 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../../security/fundamentals/security-controls.md)。