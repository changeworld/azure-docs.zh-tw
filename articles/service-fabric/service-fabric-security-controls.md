---
title: 適用于 Azure Service Fabric 的安全性控制
description: 瞭解 Azure Service Fabric 的安全性控制。 包含內建安全性控制項的檢查清單。
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75645424"
---
# <a name="security-controls-for-azure-service-fabric"></a>適用于 Azure Service Fabric 的安全性控制

本文記錄 Azure Service Fabric 內建的安全性控制項。 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| 是 |  |
| 網路隔離和防火牆支援| 是 | 使用網路安全性群組 (NSG)。 |
| 強制通道支援| 是 | Azure 網路提供強制通道功能。 |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 是 | 使用 Azure 監視支援和協力廠商支援。 |
| 控制和管理平面記錄和審核| 是 | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面記錄和審核| N/A | 客戶擁有叢集。  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 是 | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接呼叫叢集端點支援兩個角色：使用者和系統管理員。客戶可以將 Api 對應到任一個角色。 |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 | 是 | 客戶擁有叢集和叢集建立所在的虛擬機器擴展集。 您可以在虛擬機器擴展集上啟用 Azure 磁片加密。 |
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | 是 | 客戶擁有叢集和叢集建立所在的虛擬機器擴展集。 您可以在虛擬機器擴展集上啟用 Azure 磁片加密。 |
| Azure 資料服務) 的資料行層級加密 (| N/A |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet-VNet 加密) | 是 |  |
| API 呼叫加密| 是 | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定版本等 ) | 是 | |

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../security/fundamentals/security-controls.md)。