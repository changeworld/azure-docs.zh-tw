---
title: 安全性控制
description: 瞭解 Azure 備份服務中使用的安全控制項。 這些控制項可説明服務預防、檢測和回應安全性漏洞。
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172146"
---
# <a name="security-controls-for-azure-backup"></a>Azure 備份的安全控制

本文將記錄 Azure 備份中內置的安全控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 | 文件
|---|---|--|
| 服務終結點支援| 否 |  |  |
| VNet 注入支援| 否 |  |  |
| 網路隔離和防火牆支援| 是 | 支援以強制通道進行 VM 備份。 對於在 VM 內執行的工作負載則不支援強制通道。 |  |
| 強制隧道支援| 否 |  |  |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意| | 文件
|---|---|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | 通過資源日誌支援日誌分析。 有關詳細資訊，請參閱[使用日誌分析監視 Azure 備份受保護的工作負荷](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)。 |  |
| 控制和管理平面日誌記錄和審核| 是 | Azure 入口網站中所有由客戶觸發的動作都會都記錄到活動記錄。 |  |
| 資料平面日誌記錄和審核| 否 | 無法直接存取 Azure 備份資料平面。  |  |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意| | 文件
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |  |
| 授權| 是 | 會使用建立客戶和內建的 RBAC 角色。 有關詳細資訊，請參閱[使用基於角色的存取控制來管理 Azure 備份復原點](/azure/backup/backup-rbac-rs-vault)。 |  |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 | | 文件
|---|---|--|
| 伺服器端靜態加密：微軟管理的金鑰 | 是 | 對儲存體帳戶使用儲存體服務加密。 |  |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | 否 |  |  |
| 列級加密（Azure 資料服務）| 否 |  |  |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 否 | 使用 HTTPS。 |  |
| API 呼叫加密| 是 |  |  |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意| | 文件
|---|---|--|
| 建構管理支援（配置版本控制等）| 是|  |  |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。
