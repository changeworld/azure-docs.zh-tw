---
title: 支援的資料類型-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析 Preview 中支援的資料類型。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 1a1ab9fa19956341e930c85d13d7067be4449cea
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049835"
---
# <a name="supported-data-types"></a>支援的資料類型

下表列出支援的資料類型時間序列深入解析

| 資料類型 | 描述 | 範例 | Parquet 中的屬性資料行名稱
|---|---|---|---|
| **bool** | 具有下列兩種狀態之一的資料類型：`true` 或 `false`。 | "isQuestionable"： true | isQuestionable_bool
| **datetime** | 表示時間的瞬間，通常以一天的日期和時間表示。 以 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 格式表示。 Datetime 屬性一律以 UTC 格式儲存。 如果正確格式化，則會套用時區位移，然後再套用以 UTC 儲存的值。 如需環境時間戳記屬性和日期時間位移的詳細資訊[，請參閱本節](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) | "eventProcessedLocalTime"： "2020-03-20T09：03： 32.8301668 Z" | eventProcessedLocalTime_datetime 
| **double** | 雙精確度64位數位  | "value"：31.0482941 | value_double
| **long** | 帶正負號的64位整數  | "value"：31 | value_long
| **string** | 文字值必須包含有效的 UTF-8。 |  "site"： "DIM_MLGGG" | site_string
| **動態** | 複雜（非基本）型別，由陣列或屬性包（字典）組成。 目前只有 stringified 基本或物件陣列（不包含 TS 識別碼或時間戳記屬性）的 JSON 陣列會儲存為動態。 閱讀這[篇文章](./concepts-json-flattening-escaping-rules.md)以瞭解如何將物件壓平合併，以及如何將陣列取消匯總 |  "values"： "[197，194，189，188]" | values_dynamic

> [!IMPORTANT]
>
> * TSI 環境是強型別。 如果裝置或標籤同時傳送整數和非整數資料，裝置屬性值將會儲存在兩個不同的 double 和 long 資料行中，而且在進行 API 呼叫和定義您的時間序列模型變數運算式時，應該使用[聯合（）函數](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

#### <a name="objects-and-arrays"></a>物件和陣列

您可以傳送複雜的類型（例如物件和陣列）做為事件裝載的一部分。 將會壓平合併物件，並將陣列儲存為 `dynamic` 或壓平合併，以根據您的環境設定和 JSON 圖形來產生多個事件。 若要深入瞭解 JSON 簡維[和轉義規則](./concepts-json-flattening-escaping-rules.md)的詳細資訊，請參閱

## <a name="next-steps"></a>後續步驟

* 閱讀 JSON 簡維[和「轉義規則](./concepts-json-flattening-escaping-rules.md)」，以瞭解事件的儲存方式。 

* 瞭解您環境的[輸送量限制](concepts-streaming-throughput-limitations.md)

* 瞭解用來內嵌串流資料的[事件來源](concepts-streaming-ingestion-event-sources.md)。
