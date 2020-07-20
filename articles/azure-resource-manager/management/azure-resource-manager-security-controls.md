---
title: 安全性控制
description: 用於評估 Azure Resource Manager 服務的內建安全性控制檢查清單。
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054452"
---
# <a name="security-controls-for-azure-resource-manager"></a>Azure Resource Manager 的安全性控制項

本文記載內建在 Azure Resource Manager 中的安全性控制項。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備註 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | Yes |  |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| Yes | HTTPS/TLS。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | N/A | Azure Resource Manager 不會儲存任何客戶內容，只會儲存控制資料。 |
| 資料行層級加密（Azure 資料服務）| Yes | |
| API 呼叫加密| Yes | |

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備註 |
|---|---|--|
| 服務端點支援| No | |
| VNet 插入支援| Yes | |
| 網路隔離和防火牆支援| No |  |
| 強制通道支援| No |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| No | |
| 控制和管理平面記錄和審核| Yes | 活動記錄會公開您資源上執行的所有寫入作業（PUT、POST、DELETE）;請參閱[查看活動記錄以在資源上審核動作](view-activity-logs.md)。 |
| 資料平面記錄和審核| N/A | |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 驗證| Yes | 以[Azure Active Directory](../../active-directory/index.yml)為基礎。|
| 授權| Yes | |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 設定管理支援（設定的版本設定等）| 是 |  |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../../security/fundamentals/security-controls.md)。
