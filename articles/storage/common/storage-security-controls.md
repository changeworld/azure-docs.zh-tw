---
title: 安全性控制
titleSuffix: Azure Storage
description: 用於評估 Azure 存儲的安全控制清單。
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: f03f497051367d36bd229a3f358d28a1130ec620
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082351"
---
# <a name="security-controls-for-azure-storage"></a>Azure 儲存的安全控制

本文將記錄 Azure 儲存中內置的安全控件。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密:微軟管理的金鑰 | 是 |  |
| 伺服器端靜態加密:客戶管理的金鑰 (BYOK) | 是 | 請參考[Azure 金鑰保存的資料庫中的客戶管理金鑰來儲存服務加密](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| 欄位編碼(Azure 資料服務)| N/A |  |
| 傳輸中的加密(如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密)| 是 | 支持標準 HHHH-H/TLS機制。  使用者還可以在數據傳輸到服務之前對其進行加密。 |
| API 呼叫加密| 是 |  |

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| 是 |  |
| 支援服務標記| 是 | 有關 Azure 儲存支援的服務標記的詳細資訊,請參閱[Azure 服務標記概述](../../virtual-network/service-tags-overview.md)。 |
| VNet 注射支援| N/A |  |
| 網路隔離和防火牆支援| 是 | |
| 強制隧道支援| N/A |  |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援(紀錄分析、應用見解等)| 是 | Azure 監視器指標|
| 控制並管理平面紀錄記錄和稽核 | 是 | Azure 資源管理員活動紀錄 |
| 資料平面紀錄記錄和稽核| 是 | 維修診斷日誌。|

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure 活動目錄、共享密鑰、共享訪問權杖。 |
| 授權| 是 | 透過 RBAC、POSIX ACL 與 SAS 權杖支援授權 |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援(設定版本控制等)| 是 | 支援透過 Azure 資源管理員 API 資源提供者版本控制 |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中內建安全控制器](../../security/fundamentals/security-controls.md)。