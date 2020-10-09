---
title: Azure 事件中樞的安全性控制
description: 本文提供用來評估 Azure 事件中樞 (網路、身分識別、資料保護等 ) 的安全性控制檢查清單。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: da20778f1e24372e445d635e675df6484905f195
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85315393"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure 事件中樞的安全性控制

本文記錄 Azure 事件中樞內建的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 服務端點支援| 是 |  |  |
| VNet 插入支援| 否 | |  |
| 網路隔離和防火牆支援| 是 |  |  |
| 強制通道支援| 否 |  |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 是 | |  |
| 控制和管理平面記錄和審核| 是 |  |  |
| 資料平面記錄和審核| 是 |   |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 驗證| 是 | | [授權存取 Azure 事件中樞](authorize-access-event-hubs.md)、 [使用 Azure Active Directory 授權存取事件中樞資源](authorize-access-azure-active-directory.md)、 [使用共用存取簽章授權存取事件中樞資源](authorize-access-shared-access-signature.md) |
| 授權|  是 | | 使用[Azure Active Directory 來驗證受控識別，以存取事件中樞資源](authenticate-managed-identity.md)、使用[Azure Active Directory 驗證應用程式以存取事件中樞資源](authenticate-application.md)、[使用共用存取簽章 (SAS 來驗證事件中樞資源的存取權) ](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 |  是 | |  |
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | 是。 適用于專用叢集。 | 您可以使用 Azure KeyVault 中的客戶管理金鑰，將事件中樞上的資料加密。 | [使用 Azure 入口網站，設定客戶管理的金鑰來加密 Azure 事件中樞待用資料](configure-customer-managed-key.md) |
| Azure 資料服務) 的資料行層級加密 (| N/A | |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet-VNet 加密) | 是 | |  |
| API 呼叫加密| 是 |  |  |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 設定管理支援 (設定版本等 ) | 是 | |  |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../security/fundamentals/security-controls.md)。
