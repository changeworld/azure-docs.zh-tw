---
title: 包含檔案
description: 包含檔案
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287403"
---
### <a name="property-limits"></a>屬性限制

在 Gen1 中，Azure 時間序列深入解析屬性限制已從最大端點800增加到1000。 提供的事件屬性具有對應的 JSON、CSV 和圖表資料行，可讓您在 [Azure 時間序列深入解析 Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)內查看。

| SKU | 最大屬性 |
| --- | --- |
| Gen2 (L1)  | 1000屬性 (資料行)  |
| Gen1 (S1)  | 600屬性 (資料行)  |
| Gen1 (S2)  | 800屬性 (資料行)  |

### <a name="streaming-ingestion"></a>串流內嵌

* 每個環境最多可有兩個 [事件來源](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) 。

* 您可以在[這裡](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)找到事件來源的最佳作法和一般指引

* 根據預設，Azure 時間序列深入解析 Gen2 可以用每 **秒最多 1 mb 的速率來內嵌傳入資料 (MBps) 每個 Azure 時間序列深入解析 Gen2 環境**。 [每個中樞分割區](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)都有額外的限制。 透過 Azure 入口網站提交支援票證，可提供最多 8 MBps 的速率。 若要深入瞭解，請閱讀 [串流內嵌輸送量限制](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)。

### <a name="api-limits"></a>API 限制

REST API Azure 時間序列深入解析 Gen2 的限制會在 [REST API 參考檔](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)中指定。
