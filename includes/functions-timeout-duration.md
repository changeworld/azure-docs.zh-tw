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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77198321"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>函數應用程式超時時間 

函式應用程式的超時期間是由專案檔 `functionTimeout` [上host.js](../articles/azure-functions/functions-host-json.md#functiontimeout)中的屬性所定義。 下表顯示計畫和不同執行階段版本的預設和最大值（以分鐘為單位）：

| 計畫 | 執行階段版本 | 預設 | 最大值 |
|------|---------|---------|---------|
| 耗用量 | 1.x | 5 | 10 |
| 耗用量 | 2.x | 5 | 10 |
| 耗用量 | 3.x | 5 | 10 |
| Premium | 1.x | 30 | 無限制 |
| Premium | 2.x | 30 | 無限制 |
| Premium | 3.x | 30 | 無限制 |
| App Service | 1.x | 無限制 | 無限制 |
| App Service | 2.x | 30 | 無限制 |
| App Service | 3.x | 30 | 無限制 |

> [!NOTE] 
> 不論函數應用程式的超時設定為何，230秒是 HTTP 觸發函式回應要求所能採取的最大時間量。 這是因為 Azure Load Balancer 的[預設空閒超時](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)。 針對較長的處理時間，請考慮使用[Durable Functions 非同步模式](../articles/azure-functions/durable/durable-functions-overview.md#async-http)，或[延遲實際的工作並傳回立即回應](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)。
