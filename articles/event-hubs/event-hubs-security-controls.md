---
title: Azure 事件中心的安全控制
description: 本文提供了用於評估 Azure 事件中心（網路、標識、資料保護等）的安全控制清單。
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309501"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure 事件中心的安全控制

本文將記錄內置於 Azure 事件中心中的安全控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 服務終結點支援| 是 |  |  |
| VNet 注入支援| 否 | |  |
| 網路隔離和防火牆支援| 是 |  |  |
| 強制隧道支援| 否 |  |  |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | |  |
| 控制和管理平面日誌記錄和審核| 是 |  |  |
| 資料平面日誌記錄和審核| 是 |   |  |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 驗證| 是 | | [授權訪問 Azure 事件中心](authorize-access-event-hubs.md)，[使用 Azure 活動目錄授權訪問事件中心資源](authorize-access-azure-active-directory.md)，[使用共用訪問簽名授權訪問事件中心資源](authorize-access-shared-access-signature.md) |
| 授權|  是 | | [使用 Azure 活動目錄驗證託管標識以訪問事件中心資源](authenticate-managed-identity.md)、[使用 Azure 活動目錄驗證應用程式以訪問事件中心資源](authenticate-application.md)、[使用共用訪問簽名 （SAS） 驗證對事件中心資源的訪問](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 伺服器端靜態加密：微軟管理的金鑰 |  是 | |  |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | 是。 可用於專用群集。 | Azure KeyVault 中的客戶託管金鑰可用於加密靜態事件中心上的資料。 | [配置客戶管理的金鑰，以便使用 Azure 門戶加密靜態 Azure 事件中心資料](configure-customer-managed-key.md) |
| 列級加密（Azure 資料服務）| N/A | |  |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | |  |
| API 呼叫加密| 是 |  |  |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 建構管理支援（配置版本控制等）| 是 | |  |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。
