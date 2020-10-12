---
title: 使用 Application Insights 監視 Azure Kubernetes Service (AKS) 上的應用程式 - Azure 監視器 | Microsoft Docs
description: Azure 監視器會與在 Kubernetes 上執行的應用程式緊密整合，讓您能即時找出應用程式的問題。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87075309"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Kubernetes 的零檢測設備應用程式監視 - Azure Monitor Application Insights

> [!IMPORTANT]
>  目前，您可以針對在 Kubernetes 上執行的 JAVA 應用程式啟用監視，而不需要檢測您的程式碼 - 使用 [JAVA 獨立代理程式](./java-in-process-agent.md)。 雖然可順暢地啟用應用程式監視的解決方案是在其他語言的文章中，但請使用 SDK 來監視在 AKS 上執行的應用程式：[ASP.NET Core](./asp-net-core.md)、[ASP.NET](./asp-net.md)、[Node.js](./nodejs.md)、[JavaScript](./javascript.md) 和 [Python](./opencensus-python.md)。

## <a name="application-monitoring-without-instrumenting-the-code"></a>應用程式監視而無需檢測程式碼
目前，只有 JAVA 可讓您啟用應用程式監視，而不需要檢測程式碼。 若要以其他語言監視應用程式，請使用 SDK。 

## <a name="java"></a>Java
啟用之後，該 Java 代理程式將自動從最廣泛使用的程式庫和架構中大量收集要求、相依性、記錄和計量。

遵循[詳細的指示](./java-in-process-agent.md)來監視在 Kubernetes 應用程式以及其他環境中執行的 JAVA 應用程式。 

## <a name="other-languages"></a>其他語言

針對其他語言的應用程式，我們目前建議使用 SDK：
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure 監視器](../overview.md) 與 [Application Insights](./app-insights-overview.md)
* 了解[分散式追蹤](./distributed-tracing.md)的概觀，並了解[應用程式對應](./app-map.md?tabs=net)可為您的業務做什麼
