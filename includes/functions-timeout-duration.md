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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77198321"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>函數應用程式逾時持續時間 

函數應用程式的逾時持續時間是由 [host. json](../articles/azure-functions/functions-host-json.md#functiontimeout) 專案檔中的 `functionTimeout` 屬性所定義。 下表顯示計畫和不同執行階段版本的預設和最大值 (以分鐘為單位)：

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
> 不論函數應用程式逾時設定為何，230 秒是 HTTP 觸發函式回應要求所能花費的最大時間量。 這是因為 [Azure Load Balancer 的預設閒置逾時](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)。 對於較長的處理時間，請考慮使用 [Durable Functions 非同步模式](../articles/azure-functions/durable/durable-functions-overview.md#async-http)或[延遲實際工作並傳回立即回應](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)。
