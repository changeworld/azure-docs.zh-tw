---
title: Azure Application Insights 中的分散式追蹤 | Microsoft Docs
description: 提供 Microsoft 透過 OpenCensus 專案中的合作關係，支援分散式追蹤的相關資訊
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9eb753981821a6b53d81b1d03e65abf68e0064dc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539987"
---
# <a name="what-is-distributed-tracing"></a>什麼是分散式追蹤？

新式雲端和[微服務](https://azure.com/microservices)架構的推出提供了簡單且獨立部署的服務，可協助降低成本，同時增加可用性和輸送量。 但是雖然這些移動作業讓個別的服務更容易瞭解，但它們也使整體系統更棘手，以及進行 debug。

在整合型架構中，我們已用來以呼叫堆疊進行調試。 呼叫堆疊是很出色的工具，可用來顯示執行的流程 (方法 A 呼叫方法 B，方法 B 呼叫方法 C)，以及有關每個呼叫的詳細資料和參數。 非常適合於在單一程序中執行的單體或服務，但我們如何在呼叫跨越程序界限 (不只限於本機堆疊上的參考) 時進行偵錯？ 

這就是分散式追蹤的來源。  

分散式追蹤相當於新式雲端和微服務架構的呼叫堆疊，但增添了非常簡單的效能分析工具。 在 Azure 監視器中，我們提供兩種取用分散式追蹤資料的體驗。 第一種是[交易診斷](./transaction-diagnostics.md)檢視，就像是新增了時間維度的呼叫堆疊。 交易診斷檢視可顯示單一交易/要求，並可協助您找出可靠性問題的根本原因及每個要求的效能瓶頸。

Azure 監視器也提供可彙總許多交易的[應用程式對應](./app-map.md)檢視，以顯示系統互動方式的拓撲檢視，以及平均效能和錯誤率。 

## <a name="how-to-enable-distributed-tracing"></a>如何啟用分散式追蹤

在應用程式中啟用跨服務的分散式追蹤，就像將適當的代理程式、SDK 或程式庫新增至每個服務一樣簡單，也就是以服務的實作為基礎來執行。

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>透過自動檢測或 Sdk 啟用 via Application Insights

適用于 .NET、.NET Core、JAVA、Node.js 和 JavaScript 的 Application Insights 代理程式和（或） Sdk 全都以原生方式支援分散式追蹤。 以下可取得安裝和設定每個 Application Insights SDK 的指示：

* [.NET](../learn/quick-monitor-portal.md)
* [.NET Core](../learn/dotnetcore-quick-start.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../learn/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

安裝和設定適當的 Application Insights SDK 後，SDK 相依性自動收集器就會針對熱門的架構、程式庫和技術自動收集追蹤資訊。 完整的支援技術清單位於[相依性自動收集文件](./auto-collect-dependencies.md)中。

 此外，只要在 [TeleletryClient](./api-custom-events-metrics.md) 上呼叫 [TrackDependency](./api-custom-events-metrics.md)，即可手動追蹤任何技術。

## <a name="enable-via-opencensus"></a>透過 OpenCensus 啟用

除了 Application Insights SDK，Application Insights 也支援透過 [OpenCensus](https://opencensus.io/) 的分散式追蹤。 OpenCensus 是開放原始碼、廠商中立的單一程式庫散發，可提供服務的計量收集和分散式追蹤。 它也可讓開放原始碼社群，使用熱門的技術 (例如 Redis、Memcached 或 MongoDB) 進行分散式追蹤。 [Microsoft 在 OpenCensus 上與其他數個監視和雲端夥伴共同作業](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)。

[Python](opencensus-python.md) 

OpenCensus 網站會維護 [Python](https://opencensus.io/api/python/trace/usage.html) 與 [Go](https://godoc.org/go.opencensus.io) 的 API 參考文件，以及可供使用 OpenCensus 的各種不同指南。 

## <a name="next-steps"></a>後續步驟

* [OpenCensus Python 使用指南](https://opencensus.io/api/python/trace/usage.html)
* [應用程式對應](./../../azure-monitor/app/app-map.md)
* [端對端效能監視](./../../azure-monitor/learn/tutorial-performance.md)
