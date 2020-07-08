---
title: Azure Service Fabric 的安全性控制
description: 瞭解 Azure Service Fabric 的安全性控制。 包含內建安全性控制項的檢查清單。
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75645424"
---
# <a name="security-controls-for-azure-service-fabric"></a>Azure Service Fabric 的安全性控制

本文記載內建于 Azure Service Fabric 的安全性控制項。 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備註 |
|---|---|--|
| 服務端點支援| Yes |  |
| VNet 插入支援| Yes |  |
| 網路隔離和防火牆支援| Yes | 使用網路安全性群組 (NSG)。 |
| 強制通道支援| Yes | Azure 網路提供強制通道功能。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| Yes | 使用 Azure 監視支援和協力廠商支援。 |
| 控制和管理平面記錄和審核| Yes | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面記錄和審核| N/A | 客戶擁有叢集。  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 驗證| Yes | 驗證會透過 Azure Active Directory 進行。 |
| 授權| Yes | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接呼叫叢集端點可支援兩個角色：使用者和管理員。客戶可以將 Api 對應至任一角色。 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備註 |
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | Yes | 客戶擁有叢集，以及用來建立叢集的虛擬機器擴展集。 可以在虛擬機器擴展集上啟用 Azure 磁片加密。 |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | Yes | 客戶擁有叢集，以及用來建立叢集的虛擬機器擴展集。 可以在虛擬機器擴展集上啟用 Azure 磁片加密。 |
| 資料行層級加密（Azure 資料服務）| N/A |  |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| Yes |  |
| API 呼叫加密| Yes | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備註|
|---|---|--|
| 設定管理支援（設定的版本設定等）| 是 | |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。