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
ms.date: 08/31/2020
ms.custom: seodec18
ms.openlocfilehash: 0fb50e01e86c00ac863352118eb36f7470251435
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489148"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>查詢 Azure 時間序列深入解析 Gen2 中的資料

Azure 時間序列深入解析 Gen2 可讓您透過公用介面 Api，在環境中儲存的事件和中繼資料上進行查詢。 [AZURE 時間序列深入解析 TSI Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)也會使用這些 api。

Azure 時間序列深入解析 Gen2 中提供三個主要 API 類別：

* **環境 api**：這些 api 會在 Azure 時間序列深入解析 Gen2 環境本身啟用查詢。 這些可用來收集呼叫者有權存取的環境清單和環境中繼資料。
* **時間序列模型-查詢 (的 TSM-Q) api**：啟用在環境的時間序列模型中儲存之中繼資料的建立、讀取、更新和刪除 (CRUD) 作業。 這些可以用來存取和編輯實例、類型和階層。
* **時間序列查詢 (TSQ) api**：當遙測或事件資料從來源提供者記錄時，可進行抓取，並使用 advanced 純量和彙總函式來啟用資料的效能計算和匯總。

Azure 時間序列深入解析 Gen2 使用以字串為基礎的豐富運算式語言， [ (TSX) 的時間序列運算式 ](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)來表示 [時間序列變數](./concepts-variables.md)中的計算。

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Azure 時間序列深入解析 Gen2 Api 總覽

支援下列核心 API。

[![時間序列查詢總覽](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>環境 API

* [取得環境 API](https://docs.microsoft.com/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment)：傳回呼叫者有權存取的環境清單。
* [取得環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability)：傳回事件計數在事件時間戳記上的分佈 `$ts` 。 此 API 會傳回細分為時間間隔的事件計數（如果有的話），以協助判斷環境中是否有任何事件。
* [取得事件架構 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)：傳回指定搜尋範圍的事件架構中繼資料。 此 API 可協助擷取指定搜尋範圍的結構描述中可用的所有中繼資料和屬性。

## <a name="time-series-model-query-tsm-q-apis"></a>時間序列模型查詢 (TSM-Q) API

這些 Api 大多支援批次執行作業，以在多個時間序列模型實體上啟用批次 CRUD 作業：

* [模型設定 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis)：啟用預設類型的 *GET* 和 *PATCH* 以及環境的模型名稱。
* 型別[API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#types-api)：在時間序列類型和其相關變數上啟用 CRUD。
* 階層[API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#hierarchies-api)：在時間序列階層和其相關聯的欄位路徑上啟用 CRUD。
* [實例 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#instances-api)：在時間序列實例和其相關聯的實例欄位上啟用 CRUD。 此外，實例 API 支援下列作業：
  * [搜尋](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)：根據實例屬性，取得搜尋時間序列實例的部分命中清單。
  * [建議](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)：搜尋並建議在搜尋以實例屬性為基礎的時間序列實例時的部分命中清單。

## <a name="time-series-query-tsq-apis"></a>時間序列查詢 (TSQ) API

這些 Api 可用於多層式儲存體解決方案中的兩個存放區 (暖和冷) 。 查詢 URL 參數是用來指定應執行查詢的 [存放區類型](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) ：

* [取得事件 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)：啟用原始事件的查詢和抓取，以及記錄在來源提供者 Azure 時間序列深入解析 Gen2 中的相關事件時間戳記。 此 API 可讓您抓取指定時間序列識別碼和搜尋範圍的原始事件。 此 API 支援分頁，以取得所選輸入的完整回應資料集。

  > [!IMPORTANT]

  > * 在 [即將變更的 JSON 簡維和轉義規則](https://docs.microsoft.com/azure/time-series-insights/ingestion-rules-update)中，陣列會儲存為 **動態** 類型。 儲存為此類型的裝載屬性 **只能透過 Get EVENTS API 來存取**。

* [取得數列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)：在原始事件上套用變數所定義的計算，以啟用計算值的查詢和抓取以及相關聯的事件時間戳記。 這些變數可以在時間序列模型中定義，或在查詢中提供內嵌。 此 API 支援分頁，以取得所選輸入的完整回應資料集。

* [匯總數列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)：藉由套用原始事件的變數所定義的計算，啟用匯總值的查詢和抓取以及相關聯的間隔時間戳記。 這些變數可以在時間序列模型中定義，或在查詢中提供內嵌。 此 API 支援分頁，以取得所選輸入的完整回應資料集。
  
  針對指定的搜尋範圍和間隔，此 API 會針對時間序列識別碼，傳回每個變數每個間隔的匯總回應。 回應資料集中的間隔數目計算方式是計算 epoch 滴答 (自 Unix epoch-1 月1日起經過的毫秒數，1970) ，然後將刻度除以查詢中指定的間隔範圍大小。

  回應集中傳回的時間戳記是左邊的間隔界限，而不是間隔中的取樣事件。

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解可在 [時間序列模型](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)中定義的不同變數。
* 閱讀有關如何從 [Azure 時間序列深入解析 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)查詢資料的詳細資訊。
