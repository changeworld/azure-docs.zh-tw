---
title: Azure API 管理的安全控制
description: 用於評估 API 管理的安全控制清單
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751132"
---
# <a name="security-controls-for-api-management"></a>API 管理的安全控制

本文將記錄 API 管理中內置的安全控制項。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 服務終結點支援| 否 | |  |
| VNet 注入支援| 是 | |  |
| 網路隔離和防火牆支援| 是 | 分別使用網路安全性群組 （NSG） 和 Azure 應用程式閘道（或其他軟體設備）。 |  |
| 強制隧道支援| 是 | Azure 網路提供強制通道功能。 |  |

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | | |
| 控制和管理平面日誌記錄和審核| 是 | [Azure 監視器活動日誌](../azure-monitor/platform/platform-logs-overview.md) | |
| 資料平面日誌記錄和審核| 是 | [Azure 監視診斷日誌](../azure-monitor/platform/platform-logs-overview.md)和（可選[）Azure 應用程式見解](../azure-monitor/app/app-insights-overview.md)。  | |


## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 驗證| 是 | |  |
| 授權| 是 | |  |

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 | 文件 |
|---|---|--|--|
| 伺服器端靜態加密：微軟管理的金鑰 | 是 | 敏感性資料（如證書、金鑰和機密命名值）使用服務管理（每個服務實例鍵）進行加密。 |  |
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | 否 | 所有加密金鑰都是每個服務實例，並且都是服務託管的。 |  |
| 列級加密（Azure 資料服務）| N/A | |  |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | [快速路由](../expressroute/index.yml)和 VNet 加密由[Azure 網路](../virtual-network/index.yml)提供。 |  |
| API 呼叫加密| 是 | 管理平面調用通過[Azure 資源管理器](../azure-resource-manager/index.yml)通過 TLS 進行。 需要有效的 JSON Web 權杖 (JWT)。  資料平面調用可以通過 TLS 和受支援的身份驗證機制之一（例如，用戶端憑證或 JWT）進行保護。 |   |
 |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意| 文件 |
|---|---|--|--|
| 建構管理支援（配置版本控制等）| 是 | 使用[Azure API 管理開發人員資源工具組](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>漏洞掃描誤報

本節記錄不影響 Azure API 管理的常見漏洞。

| 弱點               | 描述                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 票務出血 （CVE-2016-9244） | 票證出血是某些 F5 產品中 TLS 會話票證擴展實施中的漏洞。 它允許從未初始化記憶體中洩漏多達 31 個位元組的資料（"出血"）。 這是由 TLS 堆疊填充從用戶端傳遞的會話 ID 引起的，資料使其長 32 位。 |

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。