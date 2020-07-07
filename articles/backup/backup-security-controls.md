---
title: 安全性控制
description: 瞭解 Azure 備份服務中使用的安全性控制項。 這些控制項可協助服務預防、偵測及回應安全性弱點。
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74172146"
---
# <a name="security-controls-for-azure-backup"></a>Azure 備份的安全性控制項

本文記載內建在 Azure 備份中的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備註 | 文件
|---|---|--|
| 服務端點支援| No |  |  |
| VNet 插入支援| No |  |  |
| 網路隔離和防火牆支援| Yes | 支援以強制通道進行 VM 備份。 對於在 VM 內執行的工作負載則不支援強制通道。 |  |
| 強制通道支援| No |  |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備註| | 文件
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| Yes | Log Analytics 可透過資源記錄來支援。 如需詳細資訊，請參閱[使用 Log Analytics 監視 Azure 備份受保護的工作負載](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)。 |  |
| 控制和管理平面記錄和審核| Yes | Azure 入口網站中所有由客戶觸發的動作都會都記錄到活動記錄。 |  |
| 資料平面記錄和審核| No | 無法直接存取 Azure 備份資料平面。  |  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備註| | 文件
|---|---|--|
| 驗證| Yes | 驗證會透過 Azure Active Directory 進行。 |  |
| 授權| Yes | 會使用建立客戶和內建的 RBAC 角色。 如需詳細資訊，請參閱[使用以角色為基礎的存取控制來管理 Azure 備份復原點](/azure/backup/backup-rbac-rs-vault)。 |  |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備註 | | 文件
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | Yes | 對儲存體帳戶使用儲存體服務加密。 |  |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | No |  |  |
| 資料行層級加密（Azure 資料服務）| No |  |  |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| No | 使用 HTTPS。 |  |
| API 呼叫加密| Yes |  |  |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備註| | 文件
|---|---|--|
| 設定管理支援（設定的版本設定等）| 是|  |  |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。
