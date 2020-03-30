---
title: Azure 服務匯流排消息的安全控制
description: 用於評估 Azure 服務匯流排消息傳遞的安全控制清單
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75903255"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Azure 服務匯流排消息的安全控制

本文將記錄 Azure 服務匯流排消息中內置的安全控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 服務終結點支援| 是（僅限高級層） | 僅[服務匯流排高級層](service-bus-premium-messaging.md)支援 VNet 服務終結點。 |  |
| VNet 注入支援| 否 | |  |
| 網路隔離和防火牆支援| 是（僅限高級層） |  |  |
| 強制隧道支援| 否 |  |  |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | 通過[Azure 監視器和警報提供支援](service-bus-metrics-azure-monitor.md)。 |  |
| 控制和管理平面日誌記錄和審核| 是 | 動作記錄可用。  | [服務匯流排診斷記錄](service-bus-diagnostic-logs.md) |
| 資料平面日誌記錄和審核| 否 |  |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 驗證| 是 | 通過[Azure 活動目錄託管服務標識進行管理](service-bus-managed-service-identity.md)。| [服務匯流排身份驗證和授權](service-bus-authentication-and-authorization.md)。 |
| 授權| 是 | 支援通過[RBAC](authenticate-application.md)和 SAS 權杖進行授權。 | [服務匯流排身份驗證和授權](service-bus-authentication-and-authorization.md)。 |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 伺服器端靜態加密：微軟管理的金鑰 |  預設情況下，伺服器端靜態加密為"是"。 |  |  |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | 是。 | Azure KeyVault 中的客戶託管金鑰可用於加密服務匯流排命名空間上靜態的資料。 | [配置客戶管理金鑰，以便使用 Azure 門戶加密靜態 Azure 服務匯流排資料](configure-customer-managed-key.md)  |
| 列級加密（Azure 資料服務）| N/A | |   |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | 支援標準 HTTPS/TLS 機制。 |   |
| API 呼叫加密| 是 | API 呼叫通過[Azure 資源管理器](../azure-resource-manager/index.yml)和 HTTPS 進行。 |   |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 建構管理支援（配置版本控制等）| 是 | 支援通過 Azure 資源管理器[API](/rest/api/resources/)進行資來源提供者版本控制。|   |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。
