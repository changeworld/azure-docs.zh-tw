---
title: 包含檔案
description: 包含檔案
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198321"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>功能應用超時持續時間 

函數應用的超時持續時間由[host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) `functionTimeout`專案檔案中的屬性定義。 下表顯示了平面和不同執行階段版本的預設值和最大值（以分鐘計）：

| 計畫 | 執行階段版本 | 預設 | 最大值 |
|------|---------|---------|---------|
| 耗用量 | 1.x | 5 | 10 |
| 耗用量 | 2.x | 5 | 10 |
| 耗用量 | 3.x | 5 | 10 |
| Premium | 1.x | 30 | 無限制 |
| Premium | 2.x | 30 | 無限制 |
| Premium | 3.x | 30 | 無限制 |
| App Service 方案 | 1.x | 無限制 | 無限制 |
| App Service 方案 | 2.x | 30 | 無限制 |
| App Service 方案 | 3.x | 30 | 無限制 |

> [!NOTE] 
> 無論函數應用超時設置如何，230 秒都是 HTTP 觸發函數回應請求所花費的最大時間。 這是因為[Azure 負載等化器的預設空閒超時](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)。 對於較長的處理時間，請考慮使用[持久函數非同步模式](../articles/azure-functions/durable/durable-functions-overview.md#async-http)或[推遲實際工作並返回即時回應](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)。
