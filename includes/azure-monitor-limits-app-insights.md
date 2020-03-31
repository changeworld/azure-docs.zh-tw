---
title: 包含檔案
description: 包含檔案
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 32a04518d3cd097a02ec3045da891237fc0e405a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334904"
---
每個應用程式的指標和事件數有一些限制，即每個檢測金鑰。 限制取決於您選擇的[定價方案](https://azure.microsoft.com/pricing/details/application-insights/)。

| 資源 | 限制 | 附註
| --- | --- | --- |
| 每日資料總量 | 100 GB | 您可以設定上限來減少資料。 如果您需要更多資料，可以從入口網站將限制增加到最多 1,000 GB。 對於容量超過 1，000 GB，請發送電子郵件至AIDataCap@microsoft.com。
| 節流 | 32，000 事件/秒 | 此限制會測量超過一分鐘。
| 資料保留 | 90 天 | 此資源適用於[搜尋](../articles/azure-monitor/app/diagnostic-search.md)、[分析](../articles/azure-monitor/app/analytics.md)和[計量瀏覽器](../articles/azure-monitor/app/metrics-explorer.md)。
| [可用性多步驟測試](../articles/azure-monitor/app/availability-multistep.md)詳述的結果保留期 | 90 天 | 此資源會提供每個步驟的詳細結果。
| 事件大小上限 | 64，000，000 位元組 |
| 屬性和度量名稱長度 | 150 | 請參閱[類型結構描述](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/)。
| 屬性值字串長度 | 8,192 | 請參閱[類型結構描述](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/)。
| 追蹤和例外狀況訊息長度 | 32,768  | 請參閱[類型結構描述](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/)。
| 每個應用程式的[可用性測試](../articles/azure-monitor/app/monitor-web-app-availability.md)計數 | 100 |
| [分析工具](../articles/azure-monitor/app/profiler.md)資料保留期 | 5 天 |
| 每天傳送的[分析工具](../articles/azure-monitor/app/profiler.md)資料 | 10 GB |

如需詳細資訊，請參閱[關於 Application Insights 中的價格和配額](../articles/azure-monitor/app/pricing.md)。
