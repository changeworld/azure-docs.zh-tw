---
title: Azure 存儲的安全控制
description: 用於評估 Azure 存儲的安全控制清單
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 379acaf48c02f0a579c07773cd48366d962a44f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061123"
---
# <a name="security-controls-for-azure-storage"></a>Azure 存儲的安全控制

本文將記錄 Azure 存儲中內置的安全控制項。 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密：微軟管理的金鑰 | 是 |  |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | 是 | 請參閱[使用 Azure 金鑰保存庫中的客戶管理金鑰進行存儲服務加密](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| 列級加密（Azure 資料服務）| N/A |  |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | 支援標準 HTTPS/TLS 機制。  使用者還可以在資料傳輸到服務之前對其進行加密。 |
| API 呼叫加密| 是 |  |

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| 是 |  |
| VNet 注入支援| N/A |  |
| 網路隔離和防火牆支援| 是 | |
| 強制隧道支援| N/A |  |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | Azure 監視器指標|
| 控制和管理平面日誌記錄和審核 | 是 | Azure 資源管理器活動日誌 |
| 資料平面日誌記錄和審核| 是 | 維修診斷日誌。|

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure 活動目錄、共用金鑰、共用訪問權杖。 |
| 授權| 是 | 通過 RBAC、POSIX ACL 和 SAS 權杖支援授權 |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 建構管理支援（配置版本控制等）| 是 | 支援通過 Azure 資源管理器 API 進行資來源提供者版本控制 |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../../security/fundamentals/security-controls.md)。