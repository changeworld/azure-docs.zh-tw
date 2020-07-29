---
title: 支援的資料類型-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析 Gen2 中支援的資料類型。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 9d29d83ed92ee0fa2217bb1c27fbf6c2fbb3584c
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170884"
---
# <a name="supported-data-types"></a>支援的資料類型

下表列出 Azure 時間序列深入解析 Gen2 所支援的資料類型

| 資料類型 | 描述 | 範例 | Parquet 中的屬性資料行名稱
|---|---|---|---|
| **bool** | 具有下列兩種狀態之一的資料類型：`true` 或 `false`。 | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | 表示時間的瞬間，通常以一天的日期和時間表示。 以 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 格式表示。 Datetime 屬性一律以 UTC 格式儲存。 如果正確格式化，則會套用時區位移，然後再套用以 UTC 儲存的值。 如需環境時間戳記屬性和日期時間位移的詳細資訊[，請參閱本節](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **double** | 雙精確度64位數位  | `"value": 31.0482941` | value_double
| **long** | 帶正負號的64位整數  | `"value" : 31` | value_long
| **string** | 文字值必須包含有效的 UTF-8。 Null 和空字串的處理方式相同。 |  `"site": "DIM_MLGGG"` | site_string
| **動態** | 複雜（非基本）型別，由陣列或屬性包（字典）組成。 目前只有 stringified 基本的 JSON 陣列或不包含 TS ID 或 timestamp 屬性的物件陣列會儲存為動態。 閱讀這[篇文章](./concepts-json-flattening-escaping-rules.md)以瞭解如何將物件壓平合併，以及如何 unrolled 陣列。 儲存為此類型的裝載屬性可透過 Azure 時間序列深入解析 Gen2 Explorer 和 `GetEvents`   查詢 API 來存取。 |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>傳送混合資料類型

您的 Azure 時間序列深入解析 Gen2 環境是強型別。 如果裝置或標籤會針對裝置屬性傳送不同類型的資料，值將會儲存在兩個分隔的資料行中，而且當您在 API 呼叫中定義時間序列模型變數運算式時，應該使用[聯合（）函數](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)。

Azure 時間序列深入解析 Explorer 提供了一種方式，可以自動合併相同裝置屬性的個別資料行。 在下列範例中，感應器會傳送一個 `PresentValue` 可以是 Long 或 Double 的屬性。 若要對屬性的所有儲存值（不論資料類型為何）進行查詢 `PresentValue` ，請選擇，然後將資料行結合在 `PresentValue (Double | Long)` 一起。

[![Explorer 自動合併](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>物件和陣列

您可以傳送複雜的類型（例如物件和陣列）做為事件裝載的一部分。 將會壓平合併物件，並將陣列儲存為 `dynamic` 或壓平合併，以根據您的環境設定和 JSON 圖形來產生多個事件。 若要深入瞭解 JSON 簡維[和轉義規則](./concepts-json-flattening-escaping-rules.md)的詳細資訊，請參閱

## <a name="next-steps"></a>後續步驟

* 閱讀 JSON 簡維[和「轉義規則](./concepts-json-flattening-escaping-rules.md)」，以瞭解事件的儲存方式。

* 瞭解您環境的[輸送量限制](./concepts-streaming-ingress-throughput-limits.md)

* 瞭解用來內嵌串流資料的[事件來源](concepts-streaming-ingestion-event-sources.md)。
