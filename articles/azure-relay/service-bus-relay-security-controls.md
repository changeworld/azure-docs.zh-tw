---
title: Azure 轉送的安全性控制
description: 本文提供用來評估 Azure 轉送的內建安全性控制項檢查清單。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 5d55026bfb6e3d6fe955a540b7596a85707398d6
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133339"
---
# <a name="security-controls-for-azure-relay"></a>Azure 轉送的安全性控制

本文記錄 Azure 轉送內建的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備註 | 文件 |
|---|---|--|--|
| 私人端點支援| 否 |  |   |
| 網路隔離和防火牆支援| 否 |  |   |
| 強制通道支援| 不適用 | 轉送是 TLS 通道  |   |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備註| 文件 |
|---|---|--|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 是 | |   |
| 控制和管理平面記錄和審核| 是 | 至 [Azure Resource Manager](../azure-resource-manager/index.yml)。 |   |
| 資料平面記錄和審核| 是 | 連接成功/失敗和錯誤並已記錄。  |   |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備註| 文件 |
|---|---|--|--|
| 驗證| 是 | Via SAS。 | [Azure 轉送驗證和授權](relay-authentication-and-authorization.md) |
| 授權|  是 | Via SAS。 | [Azure 轉送驗證和授權](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備註 | 文件 |
|---|---|--|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 |  不適用 | 轉送是 web 通訊端，不會保存資料。 |   |
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | 否 | 僅使用 Microsoft TLS 憑證。  |   |
| Azure 資料服務) 的資料行層級加密 (| 不適用 | |   |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet-VNet 加密) | 是 | 服務需要 TLS。 |   |
| API 呼叫加密| 是 | IP-HTTPS. |


## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備註| 文件 |
|---|---|--|--|
| 設定管理支援 (設定版本等 ) | 是 | 至 [Azure Resource Manager](../azure-resource-manager/index.yml)。|   |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../security/fundamentals/security-controls.md)。