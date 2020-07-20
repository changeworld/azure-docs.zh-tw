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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81263333"
---
以下摘要說明一般可用性的主要限制。

### <a name="sku-ingress-rates-and-capacities"></a>SKU 輸入速率和容量

設定新的時間序列深入解析環境時，S1 和 S2 SKU 輸入速率和容量可提供彈性。 您的 SKU 容量會根據所儲存的事件或位元組數（以先發生者為准），指出您的每日輸入速率。 請注意，輸入會以*每分鐘*測量，而**節流**會使用權杖 bucket 演算法來套用。 輸入是以 1 KB 區塊來測量。 例如，0.8-KB 的實際事件會當做一個事件來測量，而 2.6 KB 的事件則會計為三個事件。

| S1 SKU 容量 | 輸入速率 | 儲存體容量上限
| --- | --- | --- |
| 1 | 每天 1 GB （1000000個事件） | 一個月 30 GB (3 千萬個事件) |
| 10 | 每天 10 GB （10000000個事件） | 一個月 300 GB (3 億個事件) |

| S2 SKU 容量 | 輸入速率 | 儲存體容量上限
| --- | --- | --- |
| 1 | 每天 10 GB （10000000個事件） | 一個月 300 GB (3 億個事件) |
| 10 | 每天 100 GB （100000000個事件） | 一個月 3 TB (30 億個事件) |

> [!NOTE]
> 容量是以線性方式擴充或減少，因此容量 2 的 S1 SKU 支援的輸入速率為一天 2 GB (2 百萬) 的事件，和 一個月 60 GB (6 千萬個事件)。

S2 SKU 環境支援每月大幅增加的事件，且輸入容量會大幅提高。

| SKU  | 每月事件計數  | 每分鐘的事件計數 | 每分鐘的事件大小  |
|---------|---------|---------|---------|---------|
| S1     |   3,000 萬   |  720    |  720 KB   |
 |S2     |   3 億   | 7,200   | 7,200 KB  |

### <a name="property-limits"></a>屬性限制

GA 屬性限制取決於選取的 SKU 環境。 提供的事件屬性具有可在[時間序列深入解析總管](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)內查看的對應 JSON、CSV 和圖表資料行。

| SKU | 最大屬性 |
| --- | --- |
| S1 | 600屬性（資料行） |
| S2 | 800屬性（資料行） |

### <a name="event-sources"></a>事件來源

每個實例最多可支援兩個事件來源。 

* 瞭解如何[新增事件中樞來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* 設定[IoT 中樞來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

### <a name="api-limits"></a>API 限制

[REST API 參考檔](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)中指定了時間序列深入解析一般可用性的 REST API 限制。