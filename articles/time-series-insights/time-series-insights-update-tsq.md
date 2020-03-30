---
title: 預覽中的資料查詢 - Azure 時間序列見解 |微軟文檔
description: Azure 時間序列見解預覽中的資料查詢概念和 HTTP REST API 概述。
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284886"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Azure 時間序列見解預覽中的資料查詢

Azure 時間序列見解支援通過公共曲面 API 對環境中存儲的事件和中繼資料進行資料查詢。 這些 API 也由[時間序列見解資源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)使用。

時間序列深入解析中提供了三個主要 API 類別：

* **環境 API**：這些 API 支援對時序見解環境本身的查詢。 這些可用於收集調用方有權訪問的環境和環境中繼資料的清單。
* **時序模型查詢 （TSM-Q） API**：啟用對環境時間序列模型中存儲的中繼資料的創建、讀取、更新和刪除 （CRUD） 操作。 這些可用於訪問和編輯實例、類型和層次結構。
* **時間序列查詢 （TSQ） API**： 啟用從來源提供者記錄的資料的遙測或事件資料，並使用高級標量和彙總函式對資料執行計算和聚合。

時序見解使用基於字串的豐富運算式語言[時序運算式 （TSX）](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)來表示計算。

## <a name="azure-time-series-insights-core-apis"></a>Azure 時間序列見解核心 API

支援下列核心 API。

[![時間序列查詢概述](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>環境 API

* [獲取環境 API](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get)：返回調用方有權訪問的環境清單。
* [獲取環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)：返回事件計數在事件時間戳記`$ts`上的分佈。 此 API 通過返回按時間間隔（如果存在任何時間）的事件計數，説明確定環境中是否存在任何事件。
* [獲取事件架構 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)：返回給定搜尋範圍的事件架構中繼資料。 此 API 可協助擷取指定搜尋範圍的結構描述中可用的所有中繼資料和屬性。

## <a name="time-series-model-query-tsm-q-apis"></a>時間序列模型查詢 (TSM-Q) API

大多數 API 都支援批次處理執行操作，以便在多個時間序列模型實體上啟用批次處理 CRUD 操作：

* [模型設置 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api)：在預設類型和環境的模型名稱上啟用*GET*和*PATCH。*
* [類型 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api)： 啟用 CRUD 時間序列類型及其關聯的變數。
* [層次結構 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api)： 啟用時間序列層次結構及其關聯的欄位路徑上的 CRUD。
* [實例 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api)：在時間序列實例及其關聯的實例欄位上啟用 CRUD。 此外，實例 API 支援以下操作：
  * [搜索](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search)：根據實例屬性檢索搜索時間序列實例的部分命中清單。
  * [建議](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest)：搜索，並根據實例屬性建議搜索時間序列實例時的熱門點擊部分清單。

## <a name="time-series-query-tsq-apis"></a>時間序列查詢 (TSQ) API

這些 API 可在我們的多層存儲解決方案中的"時間序列洞察"中的所有兩個存儲上提供。 查詢 URL 參數用於指定查詢應執行的[存儲類型](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters)：

* [獲取事件 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)：啟用原始事件和相關事件時間戳記的查詢和檢索，因為它們記錄在來源提供者的時間序列見解中。 此 API 允許檢索給定時間序列 ID 和搜尋範圍的原始事件。 此 API 支援分頁以檢索所選輸入的完整回應資料集。 

* [獲取系列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)：通過在原始事件上應用由變數定義的計算，啟用對計算值和相關事件時間戳記的查詢和檢索。 這些變數可以在時間序列模型中定義，也可以在查詢中內聯提供。 此 API 支援分頁以檢索所選輸入的完整回應資料集。 

* [聚合系列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)：通過在原始事件上應用由變數定義的計算，啟用查詢和檢索聚合值和關聯的間隔時間戳記。 這些變數可以在時間序列模型中定義，也可以在查詢中內聯提供。 此 API 支援分頁以檢索所選輸入的完整回應資料集。 
  
  對於指定的搜尋範圍和間隔，此 API 返回時間序列 ID 每個間隔每個變數的聚合回應。 回應資料集中的間隔數的計算方法是計算紀元刻度（自 Unix 紀元 - 1970 年 1 月 1 日以來經過的毫秒數），並將刻度除以查詢中指定的間隔範圍大小。

  回應集中返回的時間戳記為左間隔邊界，而不是間隔中採樣的事件。 

## <a name="next-steps"></a>後續步驟

- 閱讀有關可在[時間序列模型中](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)定義的不同變數的更多內容。
- 閱讀有關如何從[時間序列見解資源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)查詢資料的更多內容。
