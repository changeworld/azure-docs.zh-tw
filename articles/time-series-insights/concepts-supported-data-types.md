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
ms.date: 08/12/2020
ms.openlocfilehash: 254732630dcf28b90413a1269a34d3aa388cb06c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997858"
---
# <a name="supported-data-types"></a>支援的資料類型

下表列出 Azure 時間序列深入解析所支援的資料類型 Gen2

| 資料類型 | 描述 | 範例 | [時間序列運算式語法](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) | Parquet 中的屬性資料行名稱
|---|---|---|---|---|
| **bool** | 具有下列兩種狀態之一的資料類型：`true` 或 `false`。 | `"isQuestionable" : true` | `$event.isQuestionable.Bool` 或 `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | 表示時間的瞬間，通常以一天的日期和時間表示。 以 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 格式表示。 Datetime 屬性一律會以 UTC 格式儲存。 時區位移（如果格式正確）會套用，然後以 UTC 格式儲存值。 如需環境時間戳記屬性和日期時間位移的詳細資訊，請參閱[本節](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  如果 "eventProcessedLocalTime" 是事件來源時間戳記： `$event.$ts` 。 如果是另一個 JSON 屬性： `$event.eventProcessedLocalTime.DateTime` 或 `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | 雙精確度64位數位  | `"value": 31.0482941` | `$event.value.Double` 或 `$event['value'].Double` |  `value_double`
| **long** | 帶正負號的64位整數  | `"value" : 31` | `$event.value.Long` 或 `$event['value'].Long` |  `value_long`
| **string** | 文字值必須由有效的 UTF-8 組成。 Null 和空字串的處理方式相同。 |  `"site": "DIM_MLGGG"`| `$event.site.String` 或 `$event['site'].String`| `site_string`
| **動態** | 複雜 (非基本) 類型，其中包含陣列或屬性包 (字典) 。 目前只有 stringified 的基本或物件陣列的 JSON 陣列不包含 TS 識別碼或時間戳記屬性 () 將會儲存為動態。 請閱讀 [本文，以瞭解](./concepts-json-flattening-escaping-rules.md) 如何將物件壓平合併，以及如何展開陣列。 只有 `Explore Events` 在 TSI Explorer 中選取以查看原始事件，或透過 [`GetEvents`](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)   查詢 API 進行用戶端剖析時，才可存取儲存為此類型的承載屬性。 |  `"values": "[197, 194, 189, 188]"` | 尚未支援在時間序列運算式中參考動態類型 | `values_dynamic`

> [!NOTE]
> 支援64位整數值，但 Azure 時間序列深入解析 Explorer 可以安全地表示的最大數位是 9007199254740991 (2 ^ 53-1) 因為 JavaScript 的限制。 如果您使用上述資料模型中的數位，您可以藉由建立 [時間序列模型變數](/concepts-variables#numeric-variables) 並 [轉換](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) 該值來縮減大小。

> [!NOTE]
> **字串** 類型不可為 null：
>   * [時間序列運算式 (TSX) ](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)以[時間序列查詢](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)表示，並比較空字串的值 (**' '**) 與**Null**的行為方式相同：相當 `$event.siteid.String = NULL` 于 `$event.siteid.String = ''` 。
>   * 即使原始事件包含空字串，API 仍可能會傳回 **Null** 值。
>   * 請勿依賴**字串**資料行中的**Null**值進行比較或評估，而是將它們視為空字串的相同方式來處理。

## <a name="sending-mixed-data-types"></a>傳送混合的資料類型

您的 Azure 時間序列深入解析 Gen2 環境是強型別。 如果裝置或標記傳送裝置屬性的不同類型資料，則值會儲存在兩個分隔的資料行中，而在 API 呼叫中定義時間序列模型變數運算式時，應使用 [聯合 ( # A1](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) 函式。

Azure 時間序列深入解析 Explorer 提供了一種方式，可自動合併相同裝置屬性的個別資料行。 在下列範例中，感應器傳送的 `PresentValue` 屬性可以是 Long 或 Double。 若要查詢所有儲存的值 (不論屬性的資料類型) 為何 `PresentValue` ，請選擇，然後將會 `PresentValue (Double | Long)` 為您合併資料行。

[![Explorer 自動聯合](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>物件和陣列

您可以將物件和陣列等複雜類型傳送為事件裝載的一部分。 嵌套物件將會壓平合併，而陣列將儲存為 `dynamic` 或壓平合併，以根據您的環境設定和 JSON 圖形來產生多個事件。 深入瞭解 JSON 簡維 [和轉義規則](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>後續步驟

* 閱讀 JSON 簡維 [和轉義規則](./concepts-json-flattening-escaping-rules.md) ，以瞭解事件的儲存方式。

* 瞭解您環境的 [輸送量限制](./concepts-streaming-ingress-throughput-limits.md)

* 瞭解用來內嵌串流資料的 [事件來源](concepts-streaming-ingestion-event-sources.md) 。
