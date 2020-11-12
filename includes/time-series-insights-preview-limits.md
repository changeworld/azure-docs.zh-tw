---
title: 包含檔案
description: 包含檔案
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 5ec1f71d13d5d2fb3af86c1c63d5e61ce13d2c82
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553218"
---
### <a name="property-limits"></a>屬性限制

暖儲存體的 Azure 時間序列深入解析屬性限制已增加至1000，非經常性存取儲存體則沒有屬性限制。 提供的事件屬性具有對應的 JSON、CSV 和圖表資料行，可讓您在 [Azure 時間序列深入解析 Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)內查看。

| SKU | 最大屬性 |
| --- | --- |
| Gen2 (L1)  | 1000屬性 (資料行) 適用于暖儲存體，而不限冷儲存體|
| Gen1 (S1)  | 600屬性 (資料行)  |
| Gen1 (S2)  | 800屬性 (資料行)  |

### <a name="streaming-ingestion"></a>串流內嵌

* 每個環境最多可有兩個 [事件來源](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) 。

* 您可以在[這裡](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)找到事件來源的最佳作法和一般指引

* 根據預設，Azure 時間序列深入解析 Gen2 可以用每 **秒最多 1 mb 的速率來內嵌傳入資料 (MBps) 每個 Azure 時間序列深入解析 Gen2 環境** 。 [每個中樞分割區](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)都有額外的限制。 透過 Azure 入口網站提交支援票證，可提供最多 8 MBps 的速率。 若要深入瞭解，請閱讀 [串流內嵌輸送量限制](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)。

### <a name="api-limits"></a>API 限制

REST API Azure 時間序列深入解析 Gen2 的限制會在 [REST API 參考檔](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)中指定。
