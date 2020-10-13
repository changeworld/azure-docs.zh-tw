---
title: 安全性控制
description: 用於評估 Azure Resource Manager 服務的內建安全性控制項檢查清單。
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86054452"
---
# <a name="security-controls-for-azure-resource-manager"></a>Azure Resource Manager 的安全性控制

本文記錄 Azure Resource Manager 內建的安全性控制項。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 | 是 |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet-VNet 加密) | 是 | HTTPS/TLS。 |
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | N/A | Azure Resource Manager 不會儲存任何客戶內容，只會控制資料。 |
| Azure 資料服務) 的資料行層級加密 (| 是 | |
| API 呼叫加密| 是 | |

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 否 |  |
| 強制通道支援| 否 |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 否 | |
| 控制和管理平面記錄和審核| 是 | 活動記錄會公開所有寫入作業， (在您的資源上執行 PUT、POST、DELETE) ;請參閱 [View 活動記錄以對資源進行審核動作](view-activity-logs.md)。 |
| 資料平面記錄和審核| N/A | |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 以[Azure Active Directory](../../active-directory/index.yml)為基礎。|
| 授權| 是 | |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定版本等 ) | 是 |  |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../../security/fundamentals/security-controls.md)。
