---
title: Azure 服務結構的安全控制項
description: 瞭解 Azure 服務結構的安全控制項。 包括內置安全控制清單。
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645424"
---
# <a name="security-controls-for-azure-service-fabric"></a>Azure 服務結構的安全控制項

本文將記錄 Azure 服務結構中內置的安全控制項。 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 服務終結點支援| 是 |  |
| VNet 注入支援| 是 |  |
| 網路隔離和防火牆支援| 是 | 使用網路安全性群組 (NSG)。 |
| 強制隧道支援| 是 | Azure 網路提供強制通道功能。 |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | 使用 Azure 監視支援和協力廠商支援。 |
| 控制和管理平面日誌記錄和審核| 是 | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面日誌記錄和審核| N/A | 客戶擁有叢集。  |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 是 | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接調用群集終結點支援兩個角色：使用者角色和管理員。客戶可以將 API 映射到任一角色。 |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 |
|---|---|--|
| 伺服器端靜態加密：微軟管理的金鑰 | 是 | 客戶擁有群集和構建群集的虛擬機器規模集。 可以在虛擬機器縮放集中啟用 Azure 磁片加密。 |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | 是 | 客戶擁有群集和構建群集的虛擬機器規模集。 可以在虛擬機器縮放集中啟用 Azure 磁片加密。 |
| 列級加密（Azure 資料服務）| N/A |  |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 |  |
| API 呼叫加密| 是 | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意|
|---|---|--|
| 建構管理支援（配置版本控制等）| 是 | |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。