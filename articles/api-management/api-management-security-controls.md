---
title: Azure API 管理的安全性控制
description: 用於評估 API 管理的安全性控制清單
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75751132"
---
# <a name="security-controls-for-api-management"></a>API 管理的安全性控制

本文記載 API 管理內建的安全性控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 服務端點支援| 否 | |  |
| VNet 插入支援| 是 | |  |
| 網路隔離和防火牆支援| 是 | 分別使用網路安全性群組（NSG）和 Azure 應用程式閘道（或其他軟體應用裝置）。 |  |
| 強制通道支援| 是 | Azure 網路提供強制通道功能。 |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| Azure 監視支援（Log analytics、App insights 等）| 是 | | |
| 控制和管理平面記錄和審核| 是 | [Azure 監視器活動記錄](../azure-monitor/platform/platform-logs-overview.md) | |
| 資料平面記錄和審核| 是 | [Azure 監視器診斷記錄](../azure-monitor/platform/platform-logs-overview.md)和（選擇性） [Azure 應用程式深入](../azure-monitor/app/app-insights-overview.md)解析。  | |


## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 驗證| 是 | |  |
| 授權| 是 | |  |

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 是 | 機密資料（例如憑證、金鑰和密碼名稱值）會使用服務管理的每個服務實例金鑰進行加密。 |  |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 否 | 所有加密金鑰都是針對每個服務實例，而且受服務管理。 |  |
| 資料行層級加密（Azure 資料服務）| N/A | |  |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | [Express Route](../expressroute/index.yml)和 VNet 加密是由[Azure 網路](../virtual-network/index.yml)提供。 |  |
| API 呼叫加密| 是 | 管理平面呼叫是透過 TLS 的[Azure Resource Manager](../azure-resource-manager/index.yml)來進行。 需要有效的 JSON Web 權杖 (JWT)。  您可以使用 TLS 和支援的其中一種驗證機制（例如用戶端憑證或 JWT）來保護資料平面呼叫。 |   |
 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 設定管理支援（設定的版本設定等）| 是 | 使用[AZURE API 管理 DevOps 資源套件](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>弱點掃描誤報

本節記載不會影響 Azure API 管理的常見弱點。

| 弱點               | 描述                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed （CVE-2016-9244） | Ticketbleed 是在某些 F5 產品中找到的 TLS SessionTicket 擴充功能的弱點。 它允許從未初始化的記憶體中，最多31個位元組的資料洩漏（「不規則」）。 這是由 TLS 堆疊填補從用戶端傳遞的會話識別碼所造成，其資料會使其32位長。 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。