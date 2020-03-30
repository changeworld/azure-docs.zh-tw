---
title: 包含檔案
description: 包含檔案
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: f097479b67a7b48eca4a2710db3bd7eed6ddc982
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013700"
---
下面總結了"通用"中的關鍵限制。

### <a name="sku-ingress-rates-and-capacities"></a>SKU 入口率和容量

S1 和 S2 SKU 入口速率和容量在配置新的時序見解環境時提供了靈活性。

| S1 SKU 容量 | 入口率 | 最大存儲容量
| --- | --- | --- |
| 1 | 1 GB (1 百萬個事件) | 一個月 30 GB (3 千萬個事件) |
| 10 | 10 GB (1 千萬個事件) | 一個月 300 GB (3 億個事件) |

| S2 SKU 容量 | 入口率 | 最大存儲容量
| --- | --- | --- |
| 1 | 10 GB (1 千萬個事件) | 一個月 300 GB (3 億個事件) |
| 10 | 100 GB (1 億個事件) | 一個月 3 TB (30 億個事件) |

> [!NOTE]
> 容量是以線性方式擴充或減少，因此容量 2 的 S1 SKU 支援的輸入速率為一天 2 GB (2 百萬) 的事件，和 一個月 60 GB (6 千萬個事件)。

S2 SKU 環境每月支援的事件大量增加，並且具有高得多的進入容量。

| SKU  | 每月事件計數  | 每月活動大小  | 每分鐘事件計數 | 每分鐘事件大小  |
|---------|---------|---------|---------|---------|
| S1     |   3,000 萬     |  30 GB     |  720    |  720 KB   |
 |S2     |   3 億    |   300 GB   | 7,200   | 7,200 KB  |

### <a name="property-limits"></a>屬性限制

GA 屬性限制取決於所選的 SKU 環境。 提供的事件屬性具有相應的 JSON、CSV 和圖表列，這些列可以在[時序見解資源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)中查看。

| SKU | 最大屬性 |
| --- | --- |
| S1 | 600 個屬性（列） |
| S2 | 800 個屬性（列） |

### <a name="event-sources"></a>事件來源

每個實例最多支援兩個事件源。 

* 瞭解如何[添加事件中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* [配置 IoT 中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

### <a name="api-limits"></a>API 限制

REST API 時間限制的時序見解通用可用性在[REST API 參考文檔中](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)指定。