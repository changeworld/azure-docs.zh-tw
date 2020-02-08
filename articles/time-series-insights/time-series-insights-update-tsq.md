---
title: 預覽中的資料查詢-Azure 時間序列深入解析 |Microsoft Docs
description: Azure 時間序列深入解析 Preview 中的資料查詢概念和 HTTP REST API 總覽。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 898515f49672a19ed8bf1c62439128b6727afc73
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087401"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽中的資料查詢

Azure 時間序列深入解析預覽版允許透過公用介面 API，針對事件和儲存在環境中的中繼資料進行資料查詢。 這些 API 也會在[時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)中使用。

時間序列深入解析中提供了三個主要 API 類別：

* **環境 api**：這些 api 會在時間序列深入解析環境本身上啟用查詢。 查詢範例是呼叫端可以存取的環境清單以及環境中繼資料。
* **時間序列模型查詢（TSM-Q） api**：針對儲存在時間序列模型環境中的中繼資料，啟用建立、讀取、更新和刪除（CRUD）作業。 範例包括執行個體、類型和階層。
* **時間序列查詢（TSQ） api**：可讓您抓取遙測或事件資料，因為它是從來源提供者記錄，或藉由使用純量和彙總函式儲存的部分變數來減少資料。 這些 Api 可以執行作業，以轉換、結合和套用時間序列資料的計算。

時間序列深入解析使用以字串為基礎的豐富運算式語言，[時間序列運算式（TSX）](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)來表示計算。

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure 時間序列深入解析預覽版核心 API

支援下列核心 API。

[![時間序列查詢總覽](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>環境 API

下列環境 API 可以使用：

* [取得環境 API](/rest/api/time-series-insights/management/environments/get)：傳回呼叫者有權存取的環境清單。
* [取得環境可用性 API](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)：傳回事件時間戳記 `$ts`的事件計數分佈。 此 API 藉由傳回事件計數 (如果有的話) 來協助判斷時間戳記中是否存在任何事件。
* [取得事件架構 API](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)：傳回指定搜尋範圍的事件架構中繼資料。 此 API 可協助擷取指定搜尋範圍的結構描述中可用的所有中繼資料和屬性。

## <a name="time-series-model-query-tsm-q-apis"></a>時間序列模型查詢 (TSM-Q) API

下列時間序列模型-查詢 Api 可供使用。 這些 Api 大多支援批次執行作業，以啟用多個時間序列模型實體的批次 CRUD 作業：

* [模型設定 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)：啟用 [預設類型] 和 [環境的模型名稱] 的*GET*和*PATCH* 。
* [類型 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)：啟用時間序列類型及其相關聯變數的 CRUD。
* 階層[API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)：啟用時間序列階層和其相關聯欄位路徑上的 CRUD。
* [實例 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)：在時間序列實例和其相關聯的實例欄位上啟用 CRUD。 此外，實例 API 支援下列作業：
  * [搜尋](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search)：根據實例屬性，抓取搜尋時間序列實例的部分點擊清單。
  * [建議](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest)：搜尋並建議在根據實例屬性搜尋時間序列實例時的部分點擊清單。

## <a name="time-series-query-tsq-apis"></a>時間序列查詢 (TSQ) API

下列時間序列查詢 Api 可供使用。 這些 Api 適用于時間序列深入解析中所有支援的多層式儲存體。 查詢 URL 參數是用來指定查詢應該在其上執行的[存放區類型](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters)：

* [取得事件 API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)：當事件從來源提供者的時間序列深入解析記錄時，可讓您查詢和抓取時間序列深入解析資料。 此 API 可讓您針對指定的時間序列識別碼和搜尋範圍來抓取原始事件。 此 API 支援分頁，以取得所選輸入的完整資料集。 

* [取得數列 API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)：使用網路上記錄的資料，從已捕捉的事件中查詢和抓取時間序列深入解析資料。 傳回的值是以模型或提供的內嵌中定義的變數為基礎。 此 API 支援分頁，以取得所選輸入的完整資料集。 此 API 有助於定義計算屬性或資料行。

    >[!NOTE]
    > 即使已在模型或提供的內嵌中指定彙總子句，也會忽略它。

  取得數列 API 會為每個間隔傳回每個變數的時間序列值。 時間序列值是時間序列深入解析用於從查詢輸出 JSON 的格式。 傳回的值是以時間序列識別碼和一組所提供的變數為基礎。

* [匯總數列 API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable)：藉由取樣和匯總已記錄的資料，啟用從已捕捉事件查詢和抓取時間序列深入解析資料的功能。 此 API 支援使用接續 token 來執行[持續](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage)性。

  彙總數列 API 會為每個間隔傳回每個變數的時間序列值。 值是以時間序列識別碼和一組所提供的變數為基礎。 彙總數列 API 透過使用儲存在時間序列模型或提供的內嵌中的變量來彙總或取樣資料，以達到降低的目的。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 Azure 時間序列深入解析 Preview 中的[儲存體和](./time-series-insights-update-storage-ingress.md)輸入。
- 閱讀時間序列深入解析預覽[資料模型](./time-series-insights-update-tsm.md)化文章。
- 探索[選擇時間序列識別碼的最佳做法](./time-series-insights-update-how-to-id.md)。
