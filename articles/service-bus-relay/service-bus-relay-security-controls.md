---
title: Azure 服務匯流排中繼的安全控制
description: 本文提供了用於評估 Azure 服務匯流排中繼的內置安全控制清單。
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514012"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Azure 服務匯流排中繼的安全控制

本文記錄 Azure 服務匯流排中繼中內置的安全控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 服務終結點支援| 否 |  |   |
| 網路隔離和防火牆支援| 否 |  |   |
| 強制隧道支援| N/A | 繼電器是 TLS 隧道  |   |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | |   |
| 控制和管理平面日誌記錄和審核| 是 | 通過[Azure 資源管理器](../azure-resource-manager/index.yml)。 |   |
| 資料平面日誌記錄和審核| 是 | 連接成功/失敗和錯誤並記錄。  |   |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 驗證| 是 | 通過 SAS。 | [Azure 轉送驗證和授權](relay-authentication-and-authorization.md) |
| 授權|  是 | 通過 SAS。 | [Azure 轉送驗證和授權](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 伺服器端靜態加密：微軟管理的金鑰 |  N/A | 中繼是 Web 通訊端，不保留資料。 |   |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | 否 | 僅使用微軟 TLS 證書。  |   |
| 列級加密（Azure 資料服務）| N/A | |   |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | 服務需要 TLS。 |   |
| API 呼叫加密| 是 | HTTPS. |


## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 建構管理支援（配置版本控制等）| 是 | 通過[Azure 資源管理器](../azure-resource-manager/index.yml)。|   |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。