---
title: Azure Key Vault 的安全性控制
description: 用於評估 Azure Key Vault 的安全性控制檢查清單
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81429859"
---
# <a name="security-controls-for-azure-key-vault"></a>Azure Key Vault 的安全性控制

本文記錄 Azure Key Vault 內建的安全性控制項。 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 使用虛擬網路 (VNet) 服務端點。 |
| VNet 插入支援| 否 |  |
| 網路隔離和防火牆支援| 是 | 使用 VNet 防火牆規則。 |
| 強制通道支援| 否 |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 是 | 使用 Log Analytics。 |
| 控制/管理平面記錄和稽核| 是 | 使用 Log Analytics。 |
| 資料平面記錄和審核| 是 | 使用 Log Analytics。 |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 是 | 使用 Key Vault 存取原則。 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 | 是 | 所有物件都會加密。 |
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | 是 | 客戶會控制其 Key Vault 中的所有索引鍵。 指定硬體安全模組 (HSM) 備份金鑰時，FIPS 層級 2 HSM 會保護金鑰、憑證或秘密。 |
| Azure 資料服務) 的資料行層級加密 (| N/A |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet-VNet 加密) | 是 | 所有通訊皆透過加密的 API 呼叫來進行 |
| API 呼叫加密| 是 | 使用 HTTPS。 |

## <a name="access-controls"></a>存取控制

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面存取控制 | 是 | Azure Resource Manager 角色型存取控制 (RBAC) |
| 資料平面存取控制 (在每個服務層級上) | 是 | Key Vault 存取原則 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../../security/fundamentals/security-controls.md)。