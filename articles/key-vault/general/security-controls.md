---
title: Azure 金鑰保管庫的安全控制
description: 用於評估 Azure 金鑰保管庫的安全控制清單
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429859"
---
# <a name="security-controls-for-azure-key-vault"></a>Azure 金鑰保管庫的安全控制

本文將記錄 Azure 密鑰保管庫中內置的安全控制件。 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| 是 | 使用虛擬網路 (VNet) 服務終結點。 |
| VNet 注射支援| 否 |  |
| 網路隔離和防火牆支援| 是 | 使用 VNet 防火牆規則。 |
| 強制隧道支援| 否 |  |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援(紀錄分析、應用見解等)| 是 | 使用 Log Analytics。 |
| 控制/管理平面記錄和稽核| 是 | 使用 Log Analytics。 |
| 資料平面紀錄記錄和稽核| 是 | 使用 Log Analytics。 |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 是 | 使用 Key Vault 存取原則。 |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密:微軟管理的金鑰 | 是 | 所有物件都會加密。 |
| 伺服器端靜態加密:客戶管理的金鑰 (BYOK) | 是 | 客戶控制其金鑰保管庫中的所有密鑰。 指定硬體安全模組 (HSM) 支援金鑰後,FIPS 2 級 HSM 可保護金鑰、證書或密鑰。 |
| 欄位編碼(Azure 資料服務)| N/A |  |
| 傳輸中的加密(如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密)| 是 | 所有通訊皆透過加密的 API 呼叫來進行 |
| API 呼叫加密| 是 | 使用 HTTPS。 |

## <a name="access-controls"></a>存取控制

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面存取控制 | 是 | Azure Resource Manager 角色型存取控制 (RBAC) |
| 資料平面存取控制 (在每個服務層級上) | 是 | Key Vault 存取原則 |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中內建安全控制器](../../security/fundamentals/security-controls.md)。