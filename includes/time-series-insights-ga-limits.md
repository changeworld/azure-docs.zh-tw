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
ms.openlocfilehash: 1aff5780aa5f4cf21d862a5cee3b140179dac03c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88704357"
---
以下摘要說明 Azure 時間序列深入解析 Gen1 中的主要限制。

### <a name="sku-ingress-rates-and-capacities"></a>SKU 輸入速率和容量

S1 和 S2 SKU 輸入速率和容量可在設定新的 Azure 時間序列深入解析環境時提供彈性。 您的 SKU 容量會根據所儲存的事件數或位元組數（以先達到者為准）來指出每日輸入速率。 請注意，輸入會 *每分鐘*測量一次，且會使用權杖 bucket 演算法來套用 **節流** 。 輸入會以 1 KB 的區塊來測量。 例如，0.8-KB 的實際事件會以一個事件來測量，而 2.6-KB 的事件則視為三個事件。

| S1 SKU 容量 | 輸入速率 | 儲存體容量上限
| --- | --- | --- |
| 1 | 每日) 1 GB (1000000 事件 | 一個月 30 GB (3 千萬個事件) |
| 10 | 每日) 10 GB (10000000 事件 | 一個月 300 GB (3 億個事件) |

| S2 SKU 容量 | 輸入速率 | 儲存體容量上限
| --- | --- | --- |
| 1 | 每日) 10 GB (10000000 事件 | 一個月 300 GB (3 億個事件) |
| 10 | 100 GB (每日) 100000000 個事件 | 一個月 3 TB (30 億個事件) |

> [!NOTE]
> 容量是以線性方式擴充或減少，因此容量 2 的 S1 SKU 支援的輸入速率為一天 2 GB (2 百萬) 的事件，和 一個月 60 GB (6 千萬個事件)。

S2 SKU 環境支援每個月大幅增加的事件，並具有大幅增加的輸入容量。

| SKU  | 每月事件計數  | 每分鐘的事件計數 | 每分鐘的事件大小  |
|---------|---------|---------|---------|---------|
| S1     |   3,000 萬   |  720    |  720 KB   |
 |S2     |   3 億   | 7,200   | 7,200 KB  |

### <a name="property-limits"></a>屬性限制

Gen1 屬性限制取決於所選的 SKU 環境。 提供的事件屬性具有對應的 JSON、CSV 和圖表資料行，可在 [Azure 時間序列深入解析 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)內查看。

| SKU | 最大屬性 |
| --- | --- |
| S1 | 600屬性 (資料行)  |
| S2 | 800屬性 (資料行)  |

### <a name="event-sources"></a>事件來源

每個實例最多可支援兩個事件來源。

* 瞭解如何 [新增事件中樞來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* 設定 [IoT 中樞來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

### <a name="api-limits"></a>API 限制

REST API Azure 時間序列深入解析 Gen1 的限制會在 [REST API 參考檔](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)中指定。