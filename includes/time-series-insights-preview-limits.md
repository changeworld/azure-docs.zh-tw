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
ms.openlocfilehash: cd6ea6d4967e024ddf88fb9572d5efae8b7a7815
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495326"
---
### <a name="property-limits"></a>屬性限制

在 Gen1 中，Azure 時間序列深入解析的屬性限制已從最大上限800增加至1000。 提供的事件屬性具有對應的 JSON、CSV 和圖表資料行，您可以在[Azure 時間序列深入解析 Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)中加以查看。

| SKU | 最大屬性 |
| --- | --- |
| Gen2 （L1） | 1000屬性（資料行） |
| Gen1 （S1） | 600屬性（資料行） |
| Gen1 （S2） | 800屬性（資料行） |

### <a name="event-sources"></a>事件來源

每個實例最多可支援兩個事件來源。

* 瞭解如何[新增事件中樞來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* 設定[IoT 中樞來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

根據預設， [Gen2 環境支援](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-throughput-limitations)每個環境每**秒最多 1 mb （MB/s）** 的輸入速率。 如有需要，客戶可以調整其環境最多**16 MB/秒**的輸送量。 此外，每個分割區的限制為**0.5 MB/秒**。

### <a name="api-limits"></a>API 限制

Azure 時間序列深入解析 Gen2 的 REST API 限制是在[REST API 參考檔](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)中指定。
