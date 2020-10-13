---
title: Azure 時間序列深入解析 Gen2 中的內嵌和簡維規則即將變更 |Microsoft Docs
description: 內嵌規則變更
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 320d92ef0ad6d02dbe7c31b883eb7f73472378ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667804"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>新環境的 JSON 簡維和轉義規則即將變更

> [!IMPORTANT]
> 這些變更只會套用至 *新建立* 的 Microsoft Azure 時間序列深入解析 Gen2 環境。 這些變更不適用於 Gen1 環境。

您的 Azure 時間序列深入解析 Gen2 環境會在一組特定的命名慣例之後，以動態方式建立您的儲存資料行。 當事件內嵌時，時間序列深入解析會將一組規則套用至 JSON 承載和屬性名稱。 JSON 資料如何簡維和儲存的變更會在2020年7月新的 Azure 時間序列深入解析 Gen2 環境中生效。 這項變更會在下列情況下影響您：

* 您的 JSON 承載包含嵌套物件。
* 您的 JSON 承載包含陣列。
* 您可以在 JSON 屬性名稱中使用下列任何四個特殊字元： `[` `\` `.``'`
* 您的一或多個時間序列 (TS) 識別碼屬性在嵌套物件內。

如果您建立新的環境，而且其中一或多個案例適用于您的事件裝載，您的資料將會以不同方式進行壓平合併和儲存。 下表摘要說明這些變更：

| 目前的規則 | 新增規則 | 範例 JSON | 先前的資料行名稱 | 新增資料行名稱
|---|---| ---| ---|  ---|
| 使用底線作為 delineator 來簡維 JSON。 |使用句點做為 delineator 來簡維 JSON。  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| 特殊字元不會被轉義。 | 包含特殊字元的 JSON 屬性名稱， `.` `[`   `\` 並 `'` 使用和來進行轉義 `['` `']` 。 在 `['` 和中 `']` ，有一個單引號和反斜線的額外轉義。 單引號會以 `\'` 和反斜線形式寫成 `\\` 。  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| 基本類型的陣列會儲存為字串。 | 基本類型的陣列會儲存為動態類型。  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
物件的陣列一律會壓平合併，並產生多個事件。 | 如果陣列中的物件沒有 TS 識別碼或時間戳記屬性，則會將物件的陣列整個儲存為動態類型。 | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>新環境的建議變更

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>如果您的 TS 識別碼和/或時間戳記屬性在物件中嵌套

任何新的部署都必須符合新的內嵌規則。 例如，如果您的 TS 識別碼為 `telemetry_tagId` ，您需要更新任何 Azure Resource Manager 範本或自動化部署腳本，以設定 `telemetry.tagId` 為環境 TS 識別碼。 您也需要針對嵌套 JSON 中的事件來源時間戳記進行這項變更。

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>如果您的承載包含嵌套 JSON 或特殊字元，而且您會自動撰寫 [時間序列模型](.\time-series-insights-update-tsm.md) 變數運算式

更新執行 [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) 的用戶端程式代碼，以符合新的內嵌規則。 例如，您應該將先前的 [時間序列運算式](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) 更新 `"value": {"tsx": "$event.series_value.Double"}` 為下列其中一個選項：

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>後續步驟

* 瞭解 [Azure 時間序列深入解析 Gen2 儲存體和](./time-series-insights-update-storage-ingress.md)輸入。

* 瞭解如何使用 [時間序列查詢 api](./concepts-query-overview.md)來查詢您的資料。

* 深入瞭解新的 [時間序列運算式語法](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)。
