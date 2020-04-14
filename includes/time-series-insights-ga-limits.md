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
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263333"
---
下面總結了"通用"中的關鍵限制。

### <a name="sku-ingress-rates-and-capacities"></a>SKU 入口率和容量

S1 和 S2 SKU 入口速率和容量在配置新的時序見解環境時提供了靈活性。 您的 SKU 容量指示您每天的入侵率,具體取決於存儲的事件數或位元組數,以先到者為準。 請注意,入口是*每分鐘*測量的,並使用權杖儲存桶演算法應用**限制**。 入口以 1 KB 塊為單位進行測量。 例如,0.8 KB 的實際事件將測量為一個事件,2.6 KB 事件計為三個事件。

| S1 SKU 容量 | 入口率 | 最大儲存容量
| --- | --- | --- |
| 1 | 每天 1 GB(100 萬次事件) | 一個月 30 GB (3 千萬個事件) |
| 10 | 每天 10 GB(1000 萬次事件) | 一個月 300 GB (3 億個事件) |

| S2 SKU 容量 | 入口率 | 最大儲存容量
| --- | --- | --- |
| 1 | 每天 10 GB(1000 萬次事件) | 一個月 300 GB (3 億個事件) |
| 10 | 每天 100 GB(1 億個事件) | 一個月 3 TB (30 億個事件) |

> [!NOTE]
> 容量是以線性方式擴充或減少，因此容量 2 的 S1 SKU 支援的輸入速率為一天 2 GB (2 百萬) 的事件，和 一個月 60 GB (6 千萬個事件)。

S2 SKU 環境每月支援的事件大量增加,並且具有高得多的進入容量。

| SKU  | 每月事件計數  | 每分鐘事件計數 | 每分鐘事件大小  |
|---------|---------|---------|---------|---------|
| S1     |   3,000 萬   |  720    |  720 KB   |
 |S2     |   3 億   | 7,200   | 7,200 KB  |

### <a name="property-limits"></a>屬性限制

GA 屬性限制取決於所選的 SKU 環境。 提供的事件屬性具有相應的 JSON、CSV 和圖表列,這些列可以在[時序見解資源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)中查看。

| SKU | 最大屬性 |
| --- | --- |
| S1 | 600 個屬性(欄) |
| S2 | 800 個屬性(欄) |

### <a name="event-sources"></a>事件來源

每個實例最多支援兩個事件源。 

* 瞭解如何[新增事件中心來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* [設定 IoT 中心來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

### <a name="api-limits"></a>API 限制

REST API 時間限制的時序見解通用可用性在[REST API 參考文件中](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)指定。