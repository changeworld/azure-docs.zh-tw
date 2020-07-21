---
title: Azure 時間序列深入解析 Gen2 中的內嵌和簡維規則即將進行的變更 |Microsoft Docs
description: 內嵌規則變更
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: lyhughes
ms.openlocfilehash: f667ca5ad82182fcf40d5c1fbb325f2ea99a7e08
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495103"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>新環境的 JSON 簡維和轉義規則即將進行的變更

**這些變更只會套用到*新建立*的 Azure 時間序列深入解析 Gen2 環境。這些變更不適用於 Gen1 環境。**

您的 Azure 時間序列深入解析 Gen2 環境會遵循一組特定的命名慣例，以動態方式建立您的儲存體資料行。 內嵌事件時，會將一組規則套用至 JSON 承載和屬性名稱。 將 JSON 資料壓平合併並儲存的變更將會在2020年7月新的 Azure 時間序列深入解析 Gen2 環境中生效。 這項變更會影響您在下列情況下的情況：

* 如果您的 JSON 承載包含嵌套物件
*  如果您的 JSON 承載包含陣列
*  如果您在 JSON 屬性名稱中使用下列四個特殊字元的任何一個： [\。 '
*  如果您的一或多個 TS ID 屬性是在嵌套物件中。

如果您建立新的環境，且上述的一或多個案例適用于您的事件裝載，您會看到資料以簡維方式儲存並以不同方式儲存。 以下是變更的摘要：

| 目前的規則 | 新增規則 | 範例 JSON | 先前的資料行名稱 | 新的資料行名稱
|---|---| ---| ---|  ---|
| 使用底線做為 delineator 的嵌套 JSON |嵌套的 JSON 會使用句點作為 delineator 進行壓平合併  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| 特殊字元不會進行轉義 | 包含特殊字元的 JSON 屬性名稱。 [\ 和 ' 使用 [' 和 '] 進行轉義。 在 [' 和 '] 內有額外的單引號和反斜線的轉義。 將會寫成單引號 \' ，並將反斜線寫成\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` | 
| 基本陣列會儲存為字串 | 基本類型的陣列會儲存為動態類型  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
物件的陣列一律會壓平合併，並產生多個事件 | 如果陣列中的物件沒有 TS ID 或 timestamp 屬性（），則物件的陣列會整體儲存為動態類型 | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>新環境的建議變更

#### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>如果您的 TS 識別碼和/或時間戳記屬性是在物件內嵌套的：

*  所有新的部署都必須符合新的內嵌規則。 例如，如果您的 TS 識別碼是， `telemetry_tagId` 您必須更新任何 ARM 範本或自動部署腳本，以將設定 `telemetry.tagId` 為環境 TS 識別碼。 在 nested JSON 中，事件來源時間戳記也需要進行這項變更。

 #### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>如果您的裝載包含嵌套的 JSON 或特殊字元，而且您自動撰寫[時間序列模型](.\time-series-insights-update-tsm.md)變數運算式

*  更新您的用戶端程式代碼，執行[TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)以符合新的內嵌規則。 例如，的上一個[時間序列運算式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) `"value": {"tsx": "$event.series_value.Double"}` 應該更新為下列其中一個選項：
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`


## <a name="next-steps"></a>後續步驟

- 閱讀[Azure 時間序列深入解析 Gen2 儲存體和](./time-series-insights-update-storage-ingress.md)輸入。

- 深入瞭解如何使用[時間序列查詢 api](./concepts-query-overview.md)來查詢您的資料。

- 深入瞭解新的[時間序列運算式語法](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

