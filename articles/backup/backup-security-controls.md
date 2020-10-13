---
title: 安全性控制
description: 瞭解 Azure 備份服務中所使用的安全性控制項。 這些控制項可協助服務防止、偵測及回應安全性弱點。
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 7ff3ff5c1b024a228778b0214e67239d3c8ab721
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89418750"
---
# <a name="security-controls-for-azure-backup"></a>Azure 備份的安全性控制

本文記錄 Azure 備份內建的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 | 文件
|---|---|--|--|
| 服務端點支援| 否 |  |  |
| VNet 插入支援| 否 |  |  |
| 網路隔離和防火牆支援| 是 | |  |
| Azure Vm 的強制通道支援 | 是  |  |  |
| 在 Azure Vm 中執行之應用程式的強制通道支援| 否  |  |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意| 文件
|---|---|--|--|
| Azure 監視支援 (例如 Log analytics、App insights) | 是 | 您可以透過資源記錄來支援 Log Analytics。 如需詳細資訊，請參閱 [使用 Log Analytics 監視 Azure 備份受保護的工作負載](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)。 |  |
| 控制和管理平面記錄和審核| 是 | Azure 入口網站中所有由客戶觸發的動作都會都記錄到活動記錄。 |  |
| 資料平面記錄和審核| 否 | 無法直接存取 Azure 備份資料平面。  |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意| 文件
|---|---|--|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |  |
| 授權| 是 | 系統會使用已建立的客戶和 Azure 內建角色。 如需詳細資訊，請參閱 [使用 Role-Based 存取控制來管理 Azure 備份復原點](./backup-rbac-rs-vault.md)。 |  |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 | 文件
|---|---|--|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 | 是 | 對儲存體帳戶使用儲存體服務加密。 |  |
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | 否 |  |  |
| Azure 資料服務) 的資料行層級加密 (| 否 |  |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet-VNet 加密) | 否 | 使用 HTTPS。 |  |
| API 呼叫加密| 是 |  |  |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意| 文件
|---|---|--|--|
| 設定管理支援 (設定的版本設定，) | 是|  |  |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../security/fundamentals/security-controls.md)。
