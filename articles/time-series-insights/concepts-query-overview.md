---
title: 查詢資料-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: Azure 時間序列深入解析 Gen2 中的資料查詢概念和 REST API 總覽。
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: abc1f1de45fe846f9f50a6a7aa3dae2ed837b52b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486789"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>從 Azure 時間序列深入解析 Gen2 查詢資料

Azure 時間序列深入解析 Gen2 可讓您透過公用表面 Api 來查詢儲存在環境中的事件和中繼資料。 [Azure 時間序列深入解析的 Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)也會使用這些 api。

Azure 時間序列深入解析 Gen2 中提供三個主要 API 類別：

* **環境 api**：這些 api 會在 Azure 時間序列深入解析 Gen2 環境本身上啟用查詢。 這些可以用來收集呼叫者有權存取的環境和環境中繼資料的清單。
* **時間序列模型查詢（TSM-Q） api**：針對環境的時間序列模型中儲存的中繼資料啟用建立、讀取、更新和刪除（CRUD）作業。 這些可以用來存取和編輯實例、類型和階層。
* **時間序列查詢（TSQ） api**：啟用遙測或事件資料的抓取，因為它是從來源提供者記錄，並使用 advanced 純量和彙總函式來啟用資料的效能計算和匯總。

Azure 時間序列深入解析 Gen2 使用以字串為基礎的豐富運算式語言，[時間序列運算式（TSX）](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)來表示[時間序列變數](./concepts-variables.md)中的計算。

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Azure 時間序列深入解析 Gen2 Api 總覽

支援下列核心 API。

[![時間序列查詢總覽](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>環境 API

* [取得環境 API](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment)：傳回呼叫者有權存取的環境清單。
* [取得環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability)：傳回事件時間戳記上事件計數的分佈 `$ts` 。 此 API 會傳回細分為時間間隔的事件計數（如果有的話），以協助判斷環境中是否有任何事件。
* [取得事件架構 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)：傳回指定搜尋範圍的事件架構中繼資料。 此 API 可協助擷取指定搜尋範圍的結構描述中可用的所有中繼資料和屬性。

## <a name="time-series-model-query-tsm-q-apis"></a>時間序列模型查詢 (TSM-Q) API

這些 Api 大多支援批次執行作業，以啟用多個時間序列模型實體的批次 CRUD 作業：

* [模型設定 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api)：啟用 [預設類型] 和 [環境的模型名稱] 的*GET*和*PATCH* 。
* [類型 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api)：啟用時間序列類型及其相關聯變數的 CRUD。
* 階層[API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api)：啟用時間序列階層和其相關聯欄位路徑上的 CRUD。
* [實例 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api)：在時間序列實例和其相關聯的實例欄位上啟用 CRUD。 此外，實例 API 支援下列作業：
  * [搜尋](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)：根據實例屬性，抓取搜尋時間序列實例的部分點擊清單。
  * [建議](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)：搜尋並建議在根據實例屬性搜尋時間序列實例時的部分點擊清單。

## <a name="time-series-query-tsq-apis"></a>時間序列查詢 (TSQ) API

這些 Api 可跨多層式儲存體解決方案中的存放區（暖和冷）使用。 查詢 URL 參數是用來指定查詢應該在其上執行的[存放區類型](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)：

* [取得事件 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)：可讓您查詢和抓取原始事件和相關聯的事件時間戳記，因為它們記錄在從來源提供者 Azure 時間序列深入解析 Gen2 中。 此 API 可讓您針對指定的時間序列識別碼和搜尋範圍來抓取原始事件。 此 API 支援分頁，以取得所選輸入的完整回應資料集。 

  > [!IMPORTANT]
  > * 在[即將變更的 JSON](https://docs.microsoft.com/azure/time-series-insights/ingestion-rules-update)簡維和「轉義」規則中，陣列將會儲存為**動態**類型。 儲存為此類型的裝載屬性**只能透過取得事件 API 來存取**。

* [取得數列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)：藉由套用原始事件上的變數所定義的計算，啟用計算值和相關事件時間戳記的查詢和抓取。 這些變數可以定義于時間序列模型中，或在查詢中以內嵌方式提供。 此 API 支援分頁，以取得所選輸入的完整回應資料集。 

* [匯總數列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)：藉由套用原始事件上的變數所定義的計算，啟用匯總值的查詢和抓取，以及相關聯的間隔時間戳記。 這些變數可以定義于時間序列模型中，或在查詢中以內嵌方式提供。 此 API 支援分頁，以取得所選輸入的完整回應資料集。 
  
  針對指定的搜尋範圍和間隔，此 API 會針對時間序列識別碼，傳回每個變數每個間隔的匯總回應。 回應資料集內的間隔數目是藉由計算 epoch 刻度（自 Unix epoch-Jan 1 到1970之後所經過的毫秒數），並將刻度除以查詢中指定的間隔範圍大小來計算。

  回應集中傳回的時間戳記是左邊的間隔界限，而不是間隔中取樣的事件。 

## <a name="next-steps"></a>後續步驟

- 深入瞭解可在[時間序列模型](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)中定義的不同變數。
- 閱讀更多有關如何從[Azure 時間序列深入解析 Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)查詢資料的資訊。
