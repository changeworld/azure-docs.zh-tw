---
title: 安全性控制
titleSuffix: Azure Storage
description: 查看評估 Azure 儲存體的安全性控制檢查清單。 涵蓋的區域包括資料保護、網路、監視和記錄、身分識別和設定。
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: b816e7bd5e00b21700bc994fc0860195d39f2915
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826512"
---
# <a name="security-controls-for-azure-storage"></a>Azure 儲存體的安全性控制項

本文記載內建在 Azure 儲存體中的安全性控制項。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 是 |  |
| 待用的伺服器端加密：客戶管理的金鑰 (BYOK)  | 是 | 請參閱[儲存體服務加密在 Azure Key Vault 中使用客戶管理的金鑰](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| Azure 資料服務) 的資料行層級加密 (| N/A |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet VNet 加密) | 是 | 支援標準 HTTPS/TLS 機制。  使用者也可以在將資料傳送至服務之前，將其加密。 |
| API 呼叫加密| 是 |  |

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| 服務標記支援| 是 | 如需 Azure 儲存體支援之服務標籤的詳細資訊，請參閱[Azure 服務標記總覽](../../virtual-network/service-tags-overview.md)。 |
| VNet 插入支援| N/A |  |
| 網路隔離和防火牆支援| 是 | |
| 強制通道支援| N/A |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 是 | Azure 監視器計量|
| 控制和管理平面記錄和審核 | 是 | Azure 活動記錄檔 |
| 資料平面記錄和審核| 是 | Azure 監視器資源記錄 |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure Active Directory、共用金鑰、共用存取權杖。 |
| 授權| 是 | 透過 RBAC、POSIX Acl 和 SAS 權杖支援授權 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援設定的 (版本設定等 ) | 是 | 透過 Azure Resource Manager Api 支援資源提供者版本設定 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../../security/fundamentals/security-controls.md)。