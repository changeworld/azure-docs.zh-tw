---
title: 安全性控制
description: 用於評估 Azure 資源管理器服務的內置安全控制項的清單。
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485620"
---
# <a name="security-controls-for-azure-resource-manager"></a>Azure 資源管理器的安全控制項

本文記錄 Azure 資源管理器中內置的安全控制項。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密：微軟管理的金鑰 | 是 |  |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | HTTPS/TLS。 |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | N/A | Azure 資源管理器不存儲任何客戶內容，僅控制資料。 |
| 列級加密（Azure 資料服務）| 是 | |
| API 呼叫加密| 是 | |

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| 否 | |
| VNet 注入支援| 是 | |
| 網路隔離和防火牆支援| 否 |  |
| 強制隧道支援| 否 |  |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援（日誌分析、應用見解等）| 否 | |
| 控制和管理平面日誌記錄和審核| 是 | 活動日誌公開對資源執行的所有寫入操作（PUT、POST、DELETE）;請參閱[查看活動日誌以審核對資源的操作](view-activity-logs.md)。 |
| 資料平面日誌記錄和審核| N/A | |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 基於[Azure 活動目錄](/azure/active-directory)。|
| 授權| 是 | |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 建構管理支援（配置版本控制等）| 是 |  |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../../security/fundamentals/security-controls.md)。