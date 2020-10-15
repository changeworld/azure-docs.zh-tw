---
title: Azure Resource Manager 範本範例 - Azure Front Door
description: 深入了解適用於 Front Door 的 Resource Manager 範本範例，包括用來建立基本 Front Door 和設定 Front Door 速率限制的範本。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2020
ms.author: duau
ms.openlocfilehash: 961214b3a815eb8ae9b0fcb283599b3474d4706e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89399356"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Front Door 的 Azure Resource Manager 部署模型範本

下表包含 Azure Front Door 的 Azure Resource Manager 部署模型範本連結。 

| [範本] | 描述 |
| ---| ---|
| [建立基本 Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| 建立具有單一後端的基本 Front Door 設定。 |
| [建立具有多個後端和後端集區及 URL 型路由的 Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| 建立已根據 URL 路徑為後端集區中及跨後端集區的多個後端設定負載平衡的 Front Door。 |
| [使用 Front Door 上架自訂網域](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| 將自訂網域新增到您的 Front Door。 |
| [建立具有地區篩選功能的 Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| 建立允許/封鎖來自特定國家/地區之流量的 Front Door。 |
| [在 Front Door 上控制後端的健康狀態探查](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| 藉由更新探查路徑及探查的傳送間隔，來更新 Front Door 以變更健康狀態探查設定。 |
| [建立具有作用中/待命後端設定的 Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| 建立示範「作用中/待命」應用程式拓撲之優先順序型路由的 Front Door，也就是預設會將所有流量傳送至主要 (最高優先順序) 後端，直到它變成無法使用為止。 |
| [建立已針對特定路由啟用快取功能的 Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| 建立已針對所定義路由設定啟用快取功能的 Front Door，藉此為您的工作負載快取任何靜態資產。 |
| [為 Front Door 主機名稱設定工作階段親和性](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | 更新 Front Door 來為您的前端主機啟用工作階段親和性，藉此將來自相同使用者工作階段的後續流量傳送至相同的後端。 |
| [設定 Front Door 以將用戶端 IP 列入允許清單或封鎖清單](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| 使用運用用戶端 IP 位址的自訂存取控制，設定讓 Front Door 限制來自特定用戶端 IP 的流量。 |
| [使用特定 http 參數來設定讓 Front Door 採取動作](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| 藉由使用運用 http 參數的自訂存取控制規則，設定讓 Front Door 根據連入要求中的 http 參數來允許或封鎖特定流量。 |
| [設定 Front Door 速率限制](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| 設定讓 Front Door 限制所指定前端主機的連入流量速率。 |
| | |

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
