---
title: 包含檔案
description: 包含檔案
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77123214"
---
### <a name="general-availability-and-preview-comparison"></a>一般可用性和預覽比較

下表總結了 Azure 時序見解通用性 （GA） 和預覽實例之間的幾個主要區別。

| | GA | 預覽 |
| --- | --- | ---|
| 一等公民 | 以事件為中心 | 以時間序列為中心 |
| 語義推理 | 低級別（參考資料） | 高級（型號） |
| 資料上下文化 | 非設備級別 | 設備和非設備級別 |
| 計算邏輯存儲 | 否 | 存儲在模型的類型變數中 |
| 存儲和存取控制 | 否 | 通過模型啟用 |
| 聚合/採樣 | 否 | 事件加權和時間加權 |
| 信號重建 | 否 | 插 值 |
| 生產派生時間序列 | 否 | 是，合併並聯接 |
| 語言靈活性 | 不可組合 | 可組合 |
| 運算式語言 | 謂詞字串 | 時間序列運算式（謂詞字串、值、運算式和函數） |

### <a name="property-limits"></a>屬性限制

時序見解屬性限制從 GA 中最大上限 800 增加到 1，000。 提供的事件屬性具有相應的 JSON、CSV 和圖表列，您可以在[時序見解預覽資源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)中查看這些列。

| SKU | 最大屬性 |
| --- | --- |
| 預覽 PAYG | 1，000 個屬性（列） |
| GA S1 | 600 個屬性（列） |
| GA S2 | 800 個屬性（列） |

### <a name="event-sources"></a>事件來源

每個實例最多支援兩個事件源。 

* 瞭解如何[添加事件中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。
* [配置 IoT 中心源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)。

預設情況下，[預覽環境支援](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations)每個環境高達 1 **MB/秒 （MB/s）** 的入侵率。 如有必要，客戶可能會將其預覽環境擴展至**16 MB/s**輸送量。 每個分區也有**0.5 MB/s**的限制。 

### <a name="api-limits"></a>API 限制

[在 REST API 參考文檔中](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)指定了時間序列見解預覽的 REST API 限制。
